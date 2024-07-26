---
title: "Quickstart: Document Intelligence (formerly Form Recognizer) Studio | v3.0"
titleSuffix: Azure AI services
description: Form and document processing, data extraction, and analysis using Document Intelligence Studio
author: laujan
manager: nitinme
ms.service: azure-ai-document-intelligence
ms.custom:
  - ignite-2023
ms.topic: quickstart
ms.date: 07/09/2024
ms.author: lajanuar
monikerRange: '>=doc-intel-3.0.0'
---


<!-- markdownlint-disable MD001 -->

# Get started: Document Intelligence Studio

[!INCLUDE [applies to v4.0 v3.1 v3.0](../includes/applies-to-v40-v31-v30.md)]

[Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/) is an online tool for visually exploring, understanding, and integrating features from the Document Intelligence service in your applications. You can get started by exploring the pretrained models with sample or your own documents. You can also create projects to build custom template models and reference the models in your applications using the [Python SDK](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) and other quickstarts.

## Prerequisites for new users

To use Document Intelligence Studio, you need the following assets and settings:

* An active [**Azure account**](https://azure.microsoft.com/free/cognitive-services/). If you don't have one, you can [**create a free account**](https://azure.microsoft.com/free/).

* A [**Document Intelligence**](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) or [**multi-service**](https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) resource.

> [!TIP]
> Create an Azure AI services resource if you plan to access multiple Azure AI services under a single endpoint/key. For Document Intelligence access only, create a Document Intelligence resource. Please note that you'll need a single-service resource if you intend to use [Microsoft Entra authentication](../../../active-directory/authentication/overview-authentication.md).
>
> Document Intelligence now supports AAD token authentication additional to local (key-based) authentication when accessing the Document Intelligence resources and storage accounts. Be sure to follow below instructions to setup correct access roles, especially if your resources are applied with `DisableLocalAuth` policy.

* **Properly scoped Azure role assignments** For document analysis and prebuilt models, following role assignments are required for different scenarios.

  * Basic
    ✔️ **Cognitive Services User**: you need this role to Document Intelligence or Azure AI services resource to enter the analyze page.

  * Advanced
    ✔️ **Contributor**: you need this role to create resource group, Document Intelligence service, or Azure AI services resource.

    For more information on authorization, *see* [Document Intelligence Studio authorization policies](../studio-overview.md#authorization-policies).

    > [!NOTE]
    > If local (key-based) authentication is disabled for your Document Intelligence service resource, be sure to obtain **Cognitive Services User** role and your AAD token will be used to authenticate requests on Document Intelligence Studio.  The **Contributor** role only allows you to list keys but does not give you permission to use the resource when key-access is disabled.

* Once your resource is configured, you can try the different models offered by Document Intelligence Studio. From the front page, select any Document Intelligence model to try using with a no-code approach.

* To test any of the document analysis or prebuilt models, select the model and use one of the sample documents or upload your own document to analyze. The analysis result is displayed at the right in the content-result-code window.

* Custom models need to be trained on your documents. See [custom models overview](../concept-custom.md) for an overview of custom models.

## Authentication

Navigate to the [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/). If it's your first time logging in, a popup window appears prompting you to configure your service resource. In accordance with your organization's policy, you have one or two options:

* **Microsoft Entra authentication: access by Resource (recommended)**.

  * Choose your existing subscription.
  * Select an existing resource group within your subscription or create a new one.
  * Select your existing Document Intelligence or Azure AI services resource.

    :::image type="content" source="../media/studio/configure-service-resource.png" alt-text="Screenshot of configure service resource form from the Document Intelligence Studio.":::

* **Local authentication: access by API endpoint and key**.

  * Retrieve your endpoint and key from the Azure portal.
  * Go to the overview page for your resource and select **Keys and Endpoint** from the left navigation bar.
  * Enter the values in the appropriate fields.

      :::image type="content" source="../media/studio/keys-and-endpoint.png" alt-text="Screenshot of the keys and endpoint page in the Azure portal.":::

* After validating the scenario in the Document Intelligence Studio, use the [**C#**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true), [**Java**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true), [**JavaScript**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true), or [**Python**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) client libraries or the [**REST API**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) to get started incorporating Document Intelligence models into your own applications.

To learn more about each model, *see* our concept pages.

### View resource details

 To view resource details such as name and pricing tier, select the **Settings** icon in the top-right corner of the Document Intelligence Studio home page and select the **Resource** tab. If you have access to other resources, you can switch resources as well.

## Models

Prebuilt models help you add Document Intelligence features to your apps without having to build, train, and publish your own models. You can choose from several prebuilt models, each of which has its own set of supported data fields. The choice of model to use for the `analyze` operation depends on the type of document to be analyzed. Document Intelligence currently supports the following prebuilt models:

#### Document analysis

* [**Layout**](https://formrecognizer.appliedai.azure.com/studio/layout): extract text, tables, selection marks, and structure information from documents (PDF, TIFF) and images (JPG, PNG, BMP).
* [**Read**](https://formrecognizer.appliedai.azure.com/studio/read): extract text lines, words, their locations, detected languages, and handwritten style if detected from documents (PDF, TIFF) and images (JPG, PNG, BMP).

#### Prebuilt

* [**Invoice**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=invoice): extract text, selection marks, tables, key-value pairs, and key information from invoices.
* [**Receipt**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt): extract text and key information from receipts.
* [**Health insurance card**](https://formrecognizer.appliedai.azure.com/studio): extract insurer, member, prescription, group number, and other key information from US health insurance cards.
* [**W-2**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=tax.us.w2): extract text and key information from W-2 tax forms.
* [**ID document**](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument): extract text and key information from driver licenses and international passports.

#### Custom

* [**Custom extraction models**](https://formrecognizer.appliedai.azure.com/studio): extract information from forms and documents with custom extraction models. Quickly train a model by labeling as few as five sample documents.
* [**Custom classification model**](https://formrecognizer.appliedai.azure.com/studio): train a custom classifier to distinguish between the different document types within your applications. Quickly train a model with as few as two classes and five samples per class.

After you completed the prerequisites, navigate to [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio/).

1. Select a Document Intelligence service feature from the Studio home page. This step is a one-time process unless you already selected the service resource from prior use. Select your Azure subscription, resource group, and resource. (You can change the resources anytime in "Settings" in the top menu.) Review and confirm your selections.

1. Select the Analyze button to run analysis on the sample document or try your document by using the Add command.

1. Zoom in and out, rotate the document view, and use the controls at the bottom of the screen.

1. Observe the highlighted extracted content in the document view. To see details hover your mouse over the keys and values.

1. Format the output section's result tab and browse the JSON output for more understanding of the service response.

1. Select the Code tab and browse the sample code for integration. Copy and download to get started.

## Added prerequisites for custom projects

In addition to the Azure account and a Document Intelligence or Azure AI services resource, you need:

### Azure Blob Storage container

A **standard performance** [**Azure Blob Storage account**](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). You create containers to store and organize your training documents within your storage account. If you don't know how to create an Azure storage account with a container, following these quickstarts:

* [**Create a storage account**](../../../storage/common/storage-account-create.md). When creating your storage account, make sure to select **Standard** performance in the **Instance details → Performance** field.
* [**Create a container**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). When creating your container, set the **Public access level** field to **Container** (anonymous read access for containers and blobs) in the **New Container** window.

### Azure role assignments

For custom projects, the following role assignments are required for different scenarios.

* Basic
  * **Cognitive Services User**: You need this role for Document Intelligence or Azure AI services resource to train the custom model or do analysis with trained models.
  * **Storage Blob Data Contributor**: You need this role for the Storage Account to create a project and label data.
* Advanced
  * **Storage Account Contributor**: You need this role for the Storage Account to set up CORS settings (this action is a one-time effort if the same storage account is reused).
  * **Contributor**: You need this role to create a resource group and resources.

  > [!NOTE]
  > If local (key-based) authentication is disabled for your Document Intelligence service resource and storage account, be sure to obtain **Cognitive Services User** and **Storage Blob Data Contributor** roles respectively, so you have enough permissions to use Document Intelligence Studio.  The **Storage Account Contributor** and **Contributor** roles only allow you to list keys but does not give you permission to use the resources when key-access is disabled.

### Configure CORS

[CORS (Cross Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) needs to be configured on your Azure storage account for it to be accessible from the Document Intelligence Studio. To configure CORS in the Azure portal, you need access to the CORS tab of your storage account.

1. Select the CORS tab for the storage account.

   :::image type="content" source="../media/quickstarts/cors-setting-menu.png" alt-text="Screenshot of the CORS setting menu in the Azure portal.":::

1. Start by creating a new CORS entry in the Blob service.

1. Set the **Allowed origins** to `https://documentintelligence.ai.azure.com`.

   :::image type="content" source="../media/quickstarts/cors-updated-image.png" alt-text="Screenshot that shows CORS configuration for a storage account.":::

    > [!TIP]
    > You can use the wildcard character '*' rather than a specified domain to allow all origin domains to make requests via CORS.

1. Select all the available 8 options for **Allowed methods**.

1. Approve all **Allowed headers** and **Exposed headers** by entering an * in each field.

1. Set the **Max Age** to 120 seconds or any acceptable value.

1. To save the changes, select the save button at the top of the page.

CORS should now be configured to use the storage account from Document Intelligence Studio.

### Sample documents set

1. Sign in to the [Azure portal](https://portal.azure.com) and navigate to **Your storage account** > **Data storage** > **Containers**.

   :::image border="true" type="content" source="../media/sas-tokens/data-storage-menu.png" alt-text="Screenshot of Data storage menu in the Azure portal.":::

1. Select a **container** from the list.

1. Select **Upload** from the menu at the top of the page.

    :::image border="true" type="content" source="../media/sas-tokens/container-upload-button.png" alt-text="Screenshot of container upload button in the Azure portal.":::

1. The **Upload blob** window appears.

1. Select your files to upload.

    :::image border="true" type="content" source="../media/sas-tokens/upload-blob-window.png" alt-text="Screenshot of upload blob window in the Azure portal.":::

> [!NOTE]
> By default, the Studio will use documents that are located at the root of your container. However, you can use data organized in folders by specifying the folder path in the Custom form project creation steps. *See* [**Organize your data in subfolders**](../how-to-guides/build-a-custom-model.md?view=doc-intel-2.1.0&preserve-view=true#organize-your-data-in-subfolders-optional)

## Custom models

To create custom models, you start with configuring your project:

1. Select the Custom model card from the Studio home and open the Custom models page.

1. Use the "Create a project" command and start the new project configuration wizard.

1. Enter project details, select the Azure subscription and resource, and the Azure Blob storage container that contains your data.

1. Review your settings, submit, and create the project.

1. Use the auto label feature to label using already trained model or one of our prebuilt models.

1. Define the labels and their types for extraction by using manual labeling.

1. Select the text in the document and select the label from the drop-down list or the labels pane.

1. Label four more documents to get at least five documents labeled.

1. Select the Train command and enter model name, select whether you want the neural (recommended) or template model to start training your custom model.

1. Use the Test command once the model is ready and validate with your test documents and observe the results.

:::image border="true" type="content" source="../media/quickstarts/form-recognizer-custom-model-demo-v3p2.gif" alt-text="Document Intelligence Custom model demo":::

### Labeling as tables

> [!NOTE]
>
> * With the release of API versions 2022-06-30-preview and later, custom template models will add support for [cross page tabular fields (tables)](../concept-custom-template.md#tabular-fields).
> * With the release of API versions 2022-06-30-preview and later, custom neural models will support [tabular fields (tables)](../concept-custom-template.md#tabular-fields) and models trained with API version 2022-08-31, or later will accept tabular field labels.

1. Use the Delete command to delete models that aren't required.

1. Download model details for offline viewing.

1. Select multiple models and compose them into a new model to be used in your applications.

Using tables as the visual pattern:

For custom form models, while creating your custom models, you may need to extract data collections from your documents. Data collections may appear in a couple of formats. Using tables as the visual pattern:

* Dynamic or variable count of values (rows) for a given set of fields (columns)

* Specific collection of values for a given set of fields (columns and/or rows)

##### Label as dynamic table

Use dynamic tables to extract variable count of values (rows) for a given set of fields (columns):

1. Add a new "Table" type label, select "Dynamic table" type, and name your label.

1. Add the number of columns (fields) and rows (for data) that you need.

1. Select the text in your page and then choose the cell and assign it to the text. Repeat for all rows and columns in all pages in all documents.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-dynamic.gif" alt-text="Document Intelligence labeling as dynamic table example":::

##### Label as fixed table

Use fixed tables to extract specific collection of values for a given set of fields (columns and/or rows):

1. Create a new "Table" type label, select "Fixed table" type, and name it.

1. Add the number of columns and rows that you need corresponding to the two sets of fields.

1. Select the text in your page and then choose the cell and assign it to the text. Repeat for other documents.

:::image border="true" type="content" source="../media/quickstarts/custom-tables-fixed.gif" alt-text="Document Intelligence Labeling as fixed table example":::

### Signature detection

>[!NOTE]
> Signature fields are currently only supported for custom template models. When training a custom neural model, labeled signature fields are ignored.

To label for signature detection: (Custom form only)

1. Create a new "Signature" type label and name it using the labeling view.

1. Use the Region command to create a rectangular region at the expected location of the signature.

1. Select the drawn region and choose the Signature type label and assign it to your drawn region. Repeat for other documents.

:::image border="true" type="content" source="../media/quickstarts/custom-signature.gif" alt-text="Document Intelligence labeling for signature detection example":::

## Next steps

* Follow our [**Document Intelligence v3.1 migration guide**](../v3-1-migration-guide.md) to learn the differences from the previous version of the REST API.
* Explore our [**v3.0 SDK quickstarts**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) to try the v3.0 features in your applications using the new client libraries.
* Refer to our [**v3.0 REST API quickstarts**](get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) to try the v3.0 features using the new REST API.

[Get started with the Document Intelligence Studio](https://formrecognizer.appliedai.azure.com).
