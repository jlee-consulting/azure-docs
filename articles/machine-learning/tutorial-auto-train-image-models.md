---
title: 'Tutorial: AutoML- train object detection model'
titleSuffix: Azure Machine Learning
description: Train an object detection model to identify if an image contains certain objects with automated ML and the Azure Machine Learning CLI v2 and Python SDK v2(preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.topic: tutorial
author: swatig007
ms.author: swatig
ms.reviewer: nibaccam
ms.date: 05/26/2022
ms.custom: devx-track-python, automl, event-tier1-build-2022
---

# Tutorial: Train an object detection model (preview) with AutoML and Python

[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]
> [!div class="op_single_selector" title1="Select the version of Azure Machine Learning CLI extension you are using:"]
> * [v1](v1/tutorial-auto-train-image-models-v1.md)
> * [v2 (current version)](tutorial-auto-train-image-models.md)

>[!IMPORTANT]
> The features presented in this article are in preview. They should be considered [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) preview features that might change at any time.

In this tutorial, you learn how to train an object detection model using Azure Machine Learning automated ML with the Azure Machine Learning CLI extension v2 or the Azure Machine Learning Python SDK v2 (preview).
This object detection model identifies whether the image contains objects, such as a can, carton, milk bottle, or water bottle.

Automated ML accepts training data and configuration settings, and automatically iterates through combinations of different feature normalization/standardization methods, models, and hyperparameter settings to arrive at the best model.


You'll write code using the Python SDK in this tutorial and learn the following tasks:

> [!div class="checklist"]
> * Download and transform data
> * Train an automated machine learning object detection model
> * Specify hyperparameter values for your model
> * Perform a hyperparameter sweep
> * Deploy your model
> * Visualize detections

## Prerequisites

* If you don’t have an Azure subscription, create a free account before you begin. Try the [free or paid version](https://azure.microsoft.com/free/) of Azure Machine Learning today.

* Python 3.6 or 3.7 are supported for this feature

* Complete the [Quickstart: Get started with Azure Machine Learning](quickstart-create-resources.md#create-the-workspace) if you don't already have an Azure Machine Learning workspace.

* Download and unzip the [**odFridgeObjects.zip*](https://cvbp-secondary.z19.web.core.windows.net/datasets/object_detection/odFridgeObjects.zip) data file. The dataset is annotated in Pascal VOC format, where each image corresponds to an xml file. Each xml file contains information on where its corresponding image file is located and also contains information about the bounding boxes and the object labels. In order to use this data, you first need to convert it to the required JSONL format as seen in the [Convert the downloaded data to JSONL](https://github.com/Azure/azureml-examples/blob/sdk-preview/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb) section of the notebook. 

# [CLI v2](#tab/CLI-v2)


This tutorial is also available in the [azureml-examples repository on GitHub](https://github.com/Azure/azureml-examples/tree/sdk-preview/cli/jobs/automl-standalone-jobs/cli-automl-image-object-detection-task-fridge-items). If you wish to run it in your own local environment, setup using the following instructions

* Install and [set up CLI (v2)](how-to-configure-cli.md#prerequisites) and make sure you install the `ml` extension.

# [Python SDK v2 (preview)](#tab/SDK-v2)

This tutorial is also available in the [azureml-examples repository on GitHub](https://github.com/Azure/azureml-examples/tree/sdk-preview/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items). If you wish to run it in your own local environment, setup using the following instructions

* Use the following commands to install Azure ML Python SDK v2:
   * Uninstall previous preview version:
   ```python
   pip uninstall azure-ai-ml
   ```
   * Install the Azure ML Python SDK v2:
   ```python
   pip install azure-ai-ml
   ```

    > [!NOTE]
    > Only Python 3.6 and 3.7 are compatible with automated ML support for computer vision tasks. 

---

## Compute target setup

You first need to set up a compute target to use for your automated ML model training. Automated ML models for image tasks require GPU SKUs.

This tutorial uses the NCsv3-series (with V100 GPUs) as this type of compute target leverages multiple GPUs to speed up training. Additionally, you can set up multiple nodes to take advantage of parallelism when tuning hyperparameters for your model.

The following code creates a GPU compute of size `Standard_NC24s_v3` with four nodes.

# [CLI v2](#tab/CLI-v2)
[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

Create a .yml file with the following configuration.

```yml
$schema: https://azuremlschemas.azureedge.net/latest/amlCompute.schema.json 
name: gpu-cluster
type: amlcompute
size: Standard_NC24s_v3
min_instances: 0
max_instances: 4
idle_time_before_scale_down: 120
```

To create the compute, you run the following CLI v2 command with the path to your .yml file, workspace name, resource group and subscription ID.

```azurecli
az ml compute create -f [PATH_TO_YML_FILE] --workspace-name [YOUR_AZURE_WORKSPACE] --resource-group [YOUR_AZURE_RESOURCE_GROUP] --subscription [YOUR_AZURE_SUBSCRIPTION]
```

The created compute can be provided using `compute` key in the `automl` task configuration yaml: 

```yaml
compute: azureml:gpu-cluster
```

# [Python SDK v2 (preview)](#tab/SDK-v2)

```python
from azure.ai.ml.entities import AmlCompute
compute_name = "gpu-cluster"
cluster_basic = AmlCompute(
    name=compute_name,
    type="amlcompute",
    size="Standard_NC24s_v3",
    min_instances=0,
    max_instances=4,
    idle_time_before_scale_down=120,
)
ml_client.begin_create_or_update(cluster_basic)
```
This compute is used later while creating the task specific `automl` job.

---

## Experiment setup

You can use an Experiment to track your model training runs.

# [CLI v2](#tab/CLI-v2)
[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]
Experiment name can be provided using `experiment_name` key as follows: 

```yaml
experiment_name: dpv2-cli-automl-image-object-detection-experiment
```

# [Python SDK v2 (preview)](#tab/SDK-v2)
Experiment name is used later while creating the task specific `automl` job.
```python
exp_name = "dpv2-image-object-detection-experiment"
```
---

## Visualize input data

Once you have the input image data prepared in [JSONL](https://jsonlines.org/) (JSON Lines) format, you can visualize the ground truth bounding boxes for an image. To do so, be sure you have `matplotlib` installed.

```
%pip install --upgrade matplotlib
```
```python

%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image as pil_image
import numpy as np
import json
import os

def plot_ground_truth_boxes(image_file, ground_truth_boxes):
    # Display the image
    plt.figure()
    img_np = mpimg.imread(image_file)
    img = pil_image.fromarray(img_np.astype("uint8"), "RGB")
    img_w, img_h = img.size

    fig,ax = plt.subplots(figsize=(12, 16))
    ax.imshow(img_np)
    ax.axis("off")

    label_to_color_mapping = {}

    for gt in ground_truth_boxes:
        label = gt["label"]

        xmin, ymin, xmax, ymax =  gt["topX"], gt["topY"], gt["bottomX"], gt["bottomY"]
        topleft_x, topleft_y = img_w * xmin, img_h * ymin
        width, height = img_w * (xmax - xmin), img_h * (ymax - ymin)

        if label in label_to_color_mapping:
            color = label_to_color_mapping[label]
        else:
            # Generate a random color. If you want to use a specific color, you can use something like "red".
            color = np.random.rand(3)
            label_to_color_mapping[label] = color

        # Display bounding box
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=2, edgecolor=color, facecolor="none")
        ax.add_patch(rect)

        # Display label
        ax.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

    plt.show()

def plot_ground_truth_boxes_jsonl(image_file, jsonl_file):
    image_base_name = os.path.basename(image_file)
    ground_truth_data_found = False
    with open(jsonl_file) as fp:
        for line in fp.readlines():
            line_json = json.loads(line)
            filename = line_json["image_url"]
            if image_base_name in filename:
                ground_truth_data_found = True
                plot_ground_truth_boxes(image_file, line_json["label"])
                break
    if not ground_truth_data_found:
        print("Unable to find ground truth information for image: {}".format(image_file))
```

Using the above helper functions, for any given image, you can run the following code to display the bounding boxes.

```python
image_file = "./odFridgeObjects/images/31.jpg"
jsonl_file = "./odFridgeObjects/train_annotations.jsonl"

plot_ground_truth_boxes_jsonl(image_file, jsonl_file)
```

## Upload data and create MLTable
In order to use the data for training, upload data to default Blob Storage of your Azure ML Workspace and register it as an asset. The benefits of registering data are:
- Easy to share with other members of the team
- Versioning of the metadata (location, description, etc)
- Lineage tracking

# [CLI v2](#tab/CLI-v2)
[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

Create a .yml file with the following configuration.

```yml
$schema: https://azuremlschemas.azureedge.net/latest/data.schema.json
name: fridge-items-images-object-detection
description: Fridge-items images Object detection
path: ./data/odFridgeObjects
type: uri_folder
```

To upload the images as a data asset, you run the following CLI v2 command with the path to your .yml file, workspace name, resource group and subscription ID.

```azurecli
az ml data create -f [PATH_TO_YML_FILE] --workspace-name [YOUR_AZURE_WORKSPACE] --resource-group [YOUR_AZURE_RESOURCE_GROUP] --subscription [YOUR_AZURE_SUBSCRIPTION]
```

# [Python SDK v2 (preview)](#tab/SDK-v2)
[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=upload-data)]

---

Next step is to create `MLTable` from your data in jsonl format as shown below. MLtable package your data into a consumable object for training.

:::code language="yaml" source="~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/data/training-mltable-folder/MLTable":::

# [CLI v2](#tab/CLI-v2)
[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

The following configuration creates training and validation data from the MLTable.

```yaml
target_column_name: label
training_data:
  path: data/training-mltable-folder
  type: mltable
validation_data:
  path: data/validation-mltable-folder
  type: mltable
```

# [Python SDK v2 (preview)](#tab/SDK-v2)

You can create data inputs from training and validation MLTable with the following code:

[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=data-load)]

---

## Configure your object detection experiment

To configure automated ML runs for image-related tasks, create a task specific AutoML job.

# [CLI v2](#tab/CLI-v2)
[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

```yaml
task: image_object_detection
primary_metric: mean_average_precision
```

# [Python SDK v2 (preview)](#tab/SDK-v2)

[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=image-object-detection-configuration)]

---

In your AutoML job, you can specify the model algorithms by using `model_name` parameter and configure the settings to perform a hyperparameter sweep over a defined search space to find the optimal model.

In this example, we will train an object detection model with `yolov5` and `fasterrcnn_resnet50_fpn`, both of which are pretrained on COCO, a large-scale object detection, segmentation, and captioning dataset that contains over thousands of labeled images with over 80 label categories.

### Hyperparameter sweeping for image tasks

You can perform a hyperparameter sweep over a defined search space to find the optimal model.

The following code, defines the search space in preparation for the hyperparameter sweep for each defined algorithm, `yolov5` and `fasterrcnn_resnet50_fpn`.  In the search space, specify the range of values for `learning_rate`, `optimizer`, `lr_scheduler`, etc., for AutoML to choose from as it attempts to generate a model with the optimal primary metric. If hyperparameter values are not specified, then default values are used for each algorithm.

For the tuning settings, use random sampling to pick samples from this parameter space by using the `random` sampling_algorithm. Doing so, tells automated ML to try a total of 10 trials with these different samples, running two trials at a time on our compute target, which was set up using four nodes. The more parameters the search space has, the more trials you need to find optimal models.

The Bandit early termination policy is also used. This policy terminates poor performing configurations; that is, those configurations that are not within 20% slack of the best performing configuration, which significantly saves compute resources.

# [CLI v2](#tab/CLI-v2)

[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

```yaml
sweep:
  limits:
    max_trials: 10
    max_concurrent_trials: 2
  sampling_algorithm: random
  early_termination:
    type: bandit
    evaluation_interval: 2
    slack_factor: 0.2
    delay_evaluation: 6
```

```yaml
search_space:
  - model_name: "yolov5"
    learning_rate: "uniform(0.0001, 0.01)"
    model_size: "choice('small', 'medium')"
  - model_name: "fasterrcnn_resnet50_fpn"
    learning_rate: "uniform(0.0001, 0.001)"
    optimizer: "choice('sgd', 'adam', 'adamw')"
    min_size: "choice(600, 800)"
```

# [Python SDK v2 (preview)](#tab/SDK-v2)

[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=sweep-settings)]

[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=search-space-settings)]

---

Once the search space and sweep settings are defined, you can then submit the job to train an image model using your training dataset.

# [CLI v2](#tab/CLI-v2)

[!INCLUDE [cli v2](../../includes/machine-learning-cli-v2.md)]

To submit your AutoML job, you run the following CLI v2 command with the path to your .yml file, workspace name, resource group and subscription ID.

```azurecli
az ml job create --file ./hello-automl-job-basic.yml --workspace-name [YOUR_AZURE_WORKSPACE] --resource-group [YOUR_AZURE_RESOURCE_GROUP] --subscription [YOUR_AZURE_SUBSCRIPTION]
```

# [Python SDK v2 (preview)](#tab/SDK-v2)

When you've configured your AutoML Job to the desired settings, you can submit the job.

[!Notebook-python[] (~/azureml-examples-main/sdk/jobs/automl-standalone-jobs/automl-image-object-detection-task-fridge-items/automl-image-object-detection-task-fridge-items.ipynb?name=submit-run)]

---

When doing a hyperparameter sweep, it can be useful to visualize the different configurations that were tried using the HyperDrive UI. You can navigate to this UI by going to the 'Child runs' tab in the UI of the main automl_image_run from above, which is the HyperDrive parent run. Then you can go into the 'Child runs' tab of this one.

# [Python SDK v2 (preview)](#tab/SDK-v2-)

Alternatively, here below you can see directly the HyperDrive parent run and navigate to its 'Child runs' tab:

```python
hd_job = ml_client.jobs.get(returned_job.name + '_HD')
hd_job
```

---

## Register and deploy model as a web service

Once you have your trained model, you can deploy the model on Azure. You can deploy your trained model as a web service on Azure Container Instances (ACI) or Azure Kubernetes Service (AKS). ACI is the perfect option for testing deployments, while AKS is better suited for high-scale, production usage.

You can deploy the model from the [Azure Machine Learning studio UI](https://ml.azure.com/). 
Navigate to the model you wish to deploy in the **Models** tab of the automated ML run and select the **Deploy**.  

![Select model from the automl runs in studio UI  ](./media/how-to-auto-train-image-models/select-model.png)

You can configure the model deployment endpoint name and the inferencing cluster to use for your model deployment in the **Deploy a model** pane.

![Deploy configuration](./media/how-to-auto-train-image-models/deploy-image-model.png)

## Test the web service

You can test the deployed web service to predict new images. For this tutorial, pass a random image from the dataset and pass it to the scoring URI.

```python
import requests

# URL for the web service
scoring_uri = <scoring_uri from web service>

# If the service is authenticated, set the key or token
key, _ = <keys from the web service>

sample_image = './test_image.jpg'

# Load image data
data = open(sample_image, 'rb').read()

# Set the content type
headers = {'Content-Type': 'application/octet-stream'}

# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, data, headers=headers)
print(resp.text)
```

## Visualize detections

Now that you have scored a test image, you can visualize the bounding boxes for this image. To do so, be sure you have matplotlib installed.

```
%pip install --upgrade matplotlib
```

```python
%matplotlib inline
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import matplotlib.patches as patches
from PIL import Image
import numpy as np
import json

IMAGE_SIZE = (18,12)
plt.figure(figsize=IMAGE_SIZE)
img_np=mpimg.imread(sample_image)
img = Image.fromarray(img_np.astype('uint8'),'RGB')
x, y = img.size

fig,ax = plt.subplots(1, figsize=(15,15))
# Display the image
ax.imshow(img_np)

# draw box and label for each detection
detections = json.loads(resp.text)
for detect in detections['boxes']:
    label = detect['label']
    box = detect['box']
    conf_score = detect['score']
    if conf_score > 0.6:
        ymin, xmin, ymax, xmax =  box['topY'],box['topX'], box['bottomY'],box['bottomX']
        topleft_x, topleft_y = x * xmin, y * ymin
        width, height = x * (xmax - xmin), y * (ymax - ymin)
        print('{}: [{}, {}, {}, {}], {}'.format(detect['label'], round(topleft_x, 3),
                                                round(topleft_y, 3), round(width, 3),
                                                round(height, 3), round(conf_score, 3)))

        color = np.random.rand(3) #'red'
        rect = patches.Rectangle((topleft_x, topleft_y), width, height,
                                 linewidth=3, edgecolor=color,facecolor='none')

        ax.add_patch(rect)
        plt.text(topleft_x, topleft_y - 10, label, color=color, fontsize=20)

plt.show()
```

## Clean up resources

Do not complete this section if you plan on running other Azure Machine Learning tutorials.

If you don't plan to use the resources you created, delete them, so you don't incur any charges.

1. In the Azure portal, select **Resource groups** on the far left.
1. From the list, select the resource group you created.
1. Select **Delete resource group**.
1. Enter the resource group name. Then select **Delete**.

You can also keep the resource group but delete a single workspace. Display the workspace properties and select **Delete**.

## Next steps

In this automated machine learning tutorial, you did the following tasks:

> [!div class="checklist"]
> * Configured a workspace and prepared data for an experiment.
> * Trained an automated object detection model
> * Specified hyperparameter values for your model
> * Performed a hyperparameter sweep
> * Deployed your model
> * Visualized detections

* [Learn more about computer vision in automated ML (preview)](concept-automated-ml.md#computer-vision-preview).
* [Learn how to set up AutoML to train computer vision models with Python (preview)](how-to-auto-train-image-models.md).
* [Learn how to configure incremental training on computer vision models](how-to-auto-train-image-models.md#incremental-training-optional).
* See [what hyperparameters are available for computer vision tasks](reference-automl-images-hyperparameters.md).
* Code examples:
  # [CLI v2](#tab/CLI-v2)
  * Review detailed code examples and use cases in the [azureml-examples repository for automated machine learning samples](https://github.com/Azure/azureml-examples/tree/sdk-preview/cli/jobs/automl-standalone-jobs). Please check the folders with 'cli-automl-image-' prefix for samples specific to building computer vision models.
  # [Python SDK v2 (preview)](#tab/SDK-v2)
  * Review detailed code examples and use cases in the [GitHub notebook repository for automated machine learning samples](https://github.com/Azure/azureml-examples/tree/sdk-preview/sdk/jobs/automl-standalone-jobs). Please check the folders with 'automl-image-' prefix for samples specific to building computer vision models.

---

> [!NOTE]
> Use of the fridge objects dataset is available through the license under the [MIT License](https://github.com/microsoft/computervision-recipes/blob/master/LICENSE).
