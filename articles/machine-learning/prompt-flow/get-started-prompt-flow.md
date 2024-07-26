---
title: Get started in prompt flow
titleSuffix: Azure Machine Learning
description: Learn how to use prompt flow in Azure Machine Learning studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: prompt-flow
ms.custom:
  - ignite-2023
  - build-2024
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.reviewer: yijunzhang
ms.date: 09/12/2023
---

# Get started with prompt flow 

This article walks you through the main user journey of using prompt flow in Azure Machine Learning studio. You learn how to enable prompt flow in your Azure Machine Learning workspace, create, and develop your first prompt flow, test, and evaluate it, then deploy it to production.

## Prerequisites 

- Make sure the default data store in your workspace is blob type. 

- If you secure prompt flow with virtual network, please follow [Network isolation in prompt flow](how-to-secure-prompt-flow.md) to learn more detail.

## Set up connection

First you need to set up connection.

Connection helps securely store and manage secret keys or other sensitive credentials required for interacting with LLM (Large Language Models) and other external tools, for example,  Azure Content Safety.

Navigate to the prompt flow homepage, select **Connections** tab. Connection is a shared resource to all members in the workspace. So, if you already see a connection whose provider is AzureOpenAI, you can skip this step, go to Start compute session.

If you aren't already connected to AzureOpenAI, select the **Create** button then *AzureOpenAI* from the drop-down.

:::image type="content" source="./media/get-started-prompt-flow/connection-creation-entry-point.png" alt-text="Screenshot of the connections tab with create highlighted." lightbox = "./media/get-started-prompt-flow/connection-creation-entry-point.png":::

Then a right-hand panel will appear. Here, you'll need to select the subscription and resource name, provide the connection name, API key (if auth type equals to API key), API base, API type, and API version before selecting the **Save** button. Prompt flow also support Microsoft Entra ID as auth type for identity based auth for Azure OpenAI resource. Learn more about [How to configure Azure OpenAI Service with managed identities](../../ai-services/openai/how-to/managed-identity.md).

:::image type="content" source="./media/get-started-prompt-flow/azure-openai-connection.png" alt-text="Screenshot of the added Azure OpenAI connections." lightbox = "./media/get-started-prompt-flow/azure-openai-connection.png":::

To obtain the API key, base, type, and version, you can navigate to the [chat playground](https://oai.azure.com/portal/chat) in the Azure OpenAI portal and select the **View code** button. From here, you can copy the necessary information and paste it into the connection creation panel.

:::image type="content" source="./media/get-started-prompt-flow/create-aoai-connection.png" alt-text="Screenshot of the chat playground after selecting the view code  button that displays a popup with sample code, highlighting the API key." lightbox = "./media/get-started-prompt-flow/create-aoai-connection.png":::

After inputting the required fields, select **Save** to create the connection.

## Create and develop your prompt flow

In **Flows** tab of prompt flow home page, select **Create** to create your first prompt flow. You can create a flow by cloning the samples in the gallery.

### Clone from sample

The built-in samples are shown in the gallery.

In this guide, we use **Web Classification** sample to walk you through the main user journey. You can select **View detail** on Web Classification tile to preview the sample. Then a preview window is popped up. You can browse the sample introduction to see if the sample is similar to your scenario. Or you can just select **Clone** to clone the sample directly, then check the flow. Test it, modify it.

:::image type="content" source="./media/get-started-prompt-flow/sample-in-gallery.png" alt-text="Screenshot of create from galley highlighting web classification. " lightbox = "./media/get-started-prompt-flow/sample-in-gallery.png":::

After selecting **Clone**, a new flow is created, and saved in a specific folder within your workspace file share storage. You can customize the folder name according to your preferences in the right panel.

### Start compute session

Then you enter the flow authoring page. Before we dive in, first start a compute session.

Compute session serves as the computing resources required for the application to run, including a Docker image that contains all necessary dependency packages. It's a must-have for flow execution. 


:::image type="content" source="./media/get-started-prompt-flow/start-compute-session.png" alt-text="Screenshot of starting a start-compute-session." lightbox = "./media/get-started-prompt-flow/start-compute-session.png":::

### Flow authoring page

When the compute session is starting, we can take a look at the flow authoring page.

:::image type="content" source="./media/get-started-prompt-flow/views.png" alt-text="Screenshot of web classification highlighting the main working area." lightbox = "./media/get-started-prompt-flow/flatten-view.png":::

At the left of authoring page, it's the flatten view, the main working area where you can author the flow, for example add a new node, edit the prompt, select the flow input data, etc.

The top right corner shows the folder structure of the flow. Each flow has a folder that contains a flow.dag.yaml file, source code files, and system folders. You can export or import a flow easily for testing, deployment, or collaborative purposes.

In addition to inline editing the node in the flatten view, you can also turn on the **Raw file mode** toggle and select the file name to edit the file in the opening file tab.

:::image type="content" source="./media/get-started-prompt-flow/file-edit-tab.png" alt-text="Screenshot of the file edit tab under raw file mode." lightbox = "./media/get-started-prompt-flow/file-edit-tab.png":::

In the bottom right corner, it's the graph view for visualization only. You can zoom in, zoom out, auto layout, etc.

In this guide, we use **Web Classification** sample to walk you through the main user journey. Web Classification is a flow demonstrating multi-class classification with LLM. Given a URL, it classifies the URL into a web category with just a few shots, simple summarization, and classification prompts. For example, given \"https://www.imdb.com/\", it classifies this URL into \"Movie\".

In the graph view, you can see how the sample flow looks like. The input is a URL to classify, then it uses a Python script to fetch text content from the URL, use LLM to summarize the text content within 100 words, then classify based on the URL and summarized text content, last use Python script to convert LLM output into a dictionary. The prepare_examples node is to feed few-shot examples to classification node's prompt.

### Flow input data

When unfolding **Inputs** section, you can create and view inputs. For Web Classification sample as shown the screenshot below, the flow input is a URL of string type.

:::image type="content" source="./media/get-started-prompt-flow/flow-input.png" alt-text="Screenshot of Web classification highlighting the inputs." lightbox = "./media/get-started-prompt-flow/flow-input.png":::

The input schema (name: url; type: string) and value are already set when cloning samples. You can change to another value manually, for example \"https://www.imdb.com/\".

### Set up LLM nodes

For each LLM node, you need to select a connection to set your LLM API keys.

:::image type="content" source="./media/get-started-prompt-flow/select-a-connection.png" alt-text="Screenshot of Web classification showing the connection drop-down." lightbox = "./media/get-started-prompt-flow/select-a-connection.png":::

For this example, make sure API type is **chat** since the prompt example we provide is for chat API. To learn the prompt format difference of chat and completion API, see  [Develop a flow](./how-to-develop-flow.md). 

Then depending on the connection type you selected, you need to select a deployment or a model. If you use Azure OpenAI connection, you need to select a deployment in drop-down (If you don't have a deployment, create one in Azure OpenAI portal by following [Create a resource and deploy a model using Azure OpenAI](../../cognitive-services/openai/how-to/create-resource.md?pivots=web-portal#deploy-a-model)). If you use OpenAI connection, you need to select a model.

We have two LLM nodes (summarize_text_content and classify_with_llm) in the flow, so you need to set up for each respectively.

### Run single node

To test and debug a single node, select the **Run** icon on node in flatten view. Run status is shown at the top, once running completed, check output in node output section.

:::image type="content" source="./media/get-started-prompt-flow/run-single-node.png" alt-text="Screenshot of Web classification showing first you run the python node then check the output, next you run the LLM node then check its output." lightbox = "./media/get-started-prompt-flow/run-single-node.png":::

Run fetch_text_content_from_url then summarize_text_content, check if the flow can successfully fetch content from web, and summarize the web content.

The single node status is shown in the graph view as well. You can also change the flow input URL to test the node behavior for different URLs.

### Run the whole flow

To test and debug the whole flow, select the **Run** button at the right top.

:::image type="content" source="./media/get-started-prompt-flow/run-flow.png" alt-text="Screenshot of Web classification showing a whole run and highlighting the run button." lightbox = "./media/get-started-prompt-flow/run-flow.png":::

Then you can check the run status and output of each node. The node statuses are shown in the graph view as well. Similarly, you can change the flow input URL to test how the flow behaves for different URLs.

### Set and check flow output

Instead of checking outputs on each node, you can also set flow output and check outputs of multiple nodes in one place. Moreover, flow output helps:

- Check bulk test results in one single table
- Define evaluation interface mapping
- Set deployment response schema

When you clone the sample, the flow outputs (category and evidence) are already set. 

You can select **View trace** button in the banner to view detailed input, output, flow execution and orchestration information. You can see that the flow predicts the input URL with a category and evidence.

:::image type="content" source="./media/get-started-prompt-flow/authoring-view-trace.png" alt-text="Screenshot of viewing trace in authoring page." lightbox = "./media/get-started-prompt-flow/authoring-view-trace.png":::

:::image type="content" source="./media/how-to-develop-flow/authoring-trace.png" alt-text=" Screenshot of view output button in two locations." lightbox ="./media/how-to-develop-flow/authoring-trace.png":::

You can select **View test result** to check all historical tests in a list.

:::image type="content" source="./media/get-started-prompt-flow/view-outputs-entry-point.png" alt-text="Screenshot of Web classification showing the view outputs button." lightbox = "./media/get-started-prompt-flow/view-outputs-entry-point.png":::

:::image type="content" source="./media/how-to-develop-flow/authoring-test-result.png" alt-text="Screenshot of flow test result." lightbox ="./media/how-to-develop-flow/authoring-test-result.png":::

## Test and evaluation

After the flow run successfully with a single row of data, you might want to test if it performs well in large set of data, you can run a bulk test and choose some evaluation methods then check the metrics.

### Prepare data

You need to prepare test data first. We support csv, tsv, and jsonl file for now.

Go to [GitHub](https://aka.ms/web-classification-data) to download "data.csv", the golden dataset for Web Classification sample.

### Evaluate

Select **Evaluate** button next to Run button, then a right panel pops up. It's a wizard that guides you to submit a batch run and to select the evaluation method (optional).​​​​​​​

You need to set a batch run name, description, then select **Add new data** to upload the data you downloaded. After uploading the data or if your colleagues in the workspace already created a dataset, you can choose the dataset from the drop-down and preview first five rows. The dataset selection drop down supports search and autosuggestion.

In addition, the **input mapping** supports mapping your flow input to a specific data column in your dataset, which means that you can use any column as the input, even if the column names don't match.

:::image type="content" source="./media/get-started-prompt-flow/upload-new-data-batch-run.png" alt-text="Screenshot of Batch run and evaluate, highlighting upload new data." lightbox = "./media/get-started-prompt-flow/upload-new-data-batch-run.png":::

Next, select one or multiple evaluation methods. The evaluation methods are also flows that use Python or LLM etc., to calculate metrics like accuracy, relevance score. The built-in evaluation flows and customized ones are listed in the page. Since Web classification is a classification scenario, it's suitable to select the **Classification Accuracy Evaluation** to evaluate.

:::image type="content" source="./media/get-started-prompt-flow/accuracy.png" alt-text="Screenshot of Web classification showing the batch run and evaluate on evaluation methods." lightbox = "./media/get-started-prompt-flow/accuracy.png":::

If you're interested in how the metrics are defined for built-in evaluation methods, you can preview the evaluation flows by selecting **More details**.

After selecting **Classification Accuracy Evaluation** as evaluation method, you can set interface mapping to map the ground truth to flow input and prediction to flow output.

:::image type="content" source="./media/get-started-prompt-flow/accuracy-configure.png" alt-text="Screenshot of Web classification showing the batch run and evaluate on the evaluation settings." lightbox = "./media/get-started-prompt-flow/accuracy-configure.png":::

Then select **Review + submit** to submit a batch run and the selected evaluation.

### Check results

When your run have been submitted successfully, select **View run list** to navigate to the batch run list of this flow.

The batch run might take a while to finish. You can **Refresh** the page to load the latest status. 

After the batch run is completed, select the run, then **Visualize outputs** to view the result of your batch run. Select **View outputs** (eye icon) to append evaluation results to the table of batch run results. You can see the total token count and overall accuracy, then in the table you'll see the results for each row of data: input, flow output and evaluation results (which cases are predicted correctly and which aren't.).

:::image type="content" source="./media/get-started-prompt-flow/check-outputs.png" alt-text="Screenshot of Web classification batch run details page to view outputs." lightbox = "./media/get-started-prompt-flow/check-outputs.png":::

You can adjust column width, hide/unhide columns, change column orders. You can also select **Export** to download the output table for further investigation, we provide 2 options: 
* Download current page: a csv file of the batch run outputs in current page.
* Download all data: what your download is a Jupyter notebook file, you need to run it to download outputs in jsonl or csv format.

As you might know, accuracy isn't the only metric that can evaluate a classification task, for example you can also use recall to evaluate. In this case, you can select **Evaluate** next to "Visualize outputs" button, choose other evaluation methods to evaluate.

## Deployment

After you build a flow and test it properly, you might want to deploy it as an endpoint so that you can invoke the endpoint for real-time inference.

### Configure the endpoint

Select batch run link, then you're directed to the batch run detail page, select **Deploy**. A wizard pops up to allow you to configure the endpoint. Specify an endpoint and deployment name, select a virtual machine, set connections, do some settings (you can use the default settings), select **Review + create** to start the deployment.

### Test the endpoint

You can go to your endpoint detail page from the notification or by navigating to **Endpoints** in the left navigation of studio then select your endpoint in **Real-time endpoints** tab. It takes several minutes to deploy the endpoint. After the endpoint is deployed successfully, you can test it in the **Test** tab. 

Put the url you want to test in the input box, and select **Test**, then you'll see the result predicted by your endpoint.

## Clean up resources

If you plan to continue now to how-to guides and would like to use the resources you created here, skip to [Next steps](#next-steps).

### Stop compute instance

If you're not going to use it now, stop the compute instance:

1. In the studio, in the left navigation area, select **Compute**.
1. In the top tabs, select **Compute instances**
1. Select the compute instance in the list.
1. On the top toolbar, select **Stop**.

### Delete all resources

If you don't plan to use any of the resources that you created, delete them so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.
1. From the list, select the resource group that you created.
1. Select **Delete resource group**.

## Next steps

Now that you have an idea of what's involved in flow developing, testing, evaluating, and deploying, learn more about the process in these tutorials:

- [Manage compute session](how-to-manage-compute-session.md)
- [Develop a standard flow](how-to-develop-a-standard-flow.md)
- [Submit bulk test and evaluate a flow](how-to-develop-a-standard-flow.md)
- [Tune prompts using variants](how-to-tune-prompts-using-variants.md)
- [Deploy a flow](how-to-deploy-for-real-time-inference.md)
