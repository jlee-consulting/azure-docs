---
title: Configure Azure DevOps Services for SAP Deployment Automation Framework
description: Configure your Azure DevOps Services for the SAP Deployment Automation Framework on Azure.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 12/16/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
---

# Use SAP Deployment Automation Framework from Azure DevOps Services

Using Azure DevOps will streamline the deployment process by providing pipelines that can be executed to perform both the infrastructure deployment and the configuration and SAP installation activities.
You can use Azure Repos to store your configuration files and Azure Pipelines to deploy and configure the infrastructure and the SAP application. 

## Sign up for Azure DevOps Services

To use Azure DevOps Services, you'll need an Azure DevOps organization. An organization is used to connect groups of related projects. Use your work or school account to automatically connect your organization to your Azure Active Directory (Azure AD). To create an account, open [Azure DevOps](https://azure.microsoft.com/services/devops/) and either _sign-in_ or create a new account. 

## Create a new project

You can use Azure Repos to store both the code from the sap-automation GitHub repository and the environment configuration files.

Open (https://dev.azure.com) and create a new project by clicking on the _New Project_ button and enter the project details. The project will contain both the Azure Repos source control repository and Azure Pipelines for performing deployment activities.

> [!NOTE]
> If you are unable to see _New Project_ ensure that you have permissions to create new projects in the organization.

Record the URL of the project.
### Import the repository

Start by importing the SAP Deployment Automation Framework GitHub repository into Azure Repos. 

Navigate to the Repositories section and choose Import a repository, import the 'https://github.com/Azure/sap-automation.git' repository into Azure DevOps. For more info, see [Import a repository](/azure/devops/repos/git/import-git-repository?view=azure-devops&preserve-view=true)

If you're unable to import a repository, you can create the 'sap-automation' repository and manually import the content from the SAP Deployment Automation Framework GitHub repository to it.

### Create the repository for manual import

> [!NOTE]
> Only do this step if you are unable to import the repository directly. 

Create the 'sap-automation' repository by navigating to the 'Repositories' section in 'Project Settings' and clicking the _Create_ button. 

Choose the repository type 'Git' and provide a name for the repository, for example 'sap-automation'.
### Cloning the repository

In order to provide a more comprehensive editing capability of the content, you can clone the repository to a local folder and edit the contents locally. 
Clone the repository to a local folder by clicking the  _Clone_ button in the Files view in the Repos section of the portal. For more info, see [Cloning a repository](/azure/devops/repos/git/clone?view=azure-devops#clone-an-azure-repos-git-repo&preserve-view=true)

:::image type="content" source="./media/automation-devops/automation-repo-clone.png" alt-text="Picture showing how to clone the repository":::

### Manually importing the repository content using a local clone

You can also download the content from the SAP Deployment Automation Framework repository manually and add it to your local clone of the Azure DevOps repository.

Navigate to 'https://github.com/Azure/SAP-automation' repository and download the repository content as a ZIP file by clicking the _Code_ button and choosing _Download ZIP_. 

Copy the content from the zip file to the root folder of your local clone.

Open the local folder in Visual Studio code, you should see that there are changes that need to be synchronized by the indicator by the source control icon as is shown in the picture below.

:::image type="content" source="./media/automation-devops/automation-vscode-changes.png" alt-text="Picture showing that source code has changed":::

Select the source control icon and provide a message about the change, for example: "Import from GitHub" and press Cntr-Enter to commit the changes. Next select the _Sync Changes_ button to synchronize the changes back to the repository.

### Create configuration root folder

> [!IMPORTANT]
   > In order to ensure that your configuration files are not overwritten by changes in the SAP Deployment Automation Framework, store them in a separate folder hierarchy.


Create a top level folder called 'WORKSPACES', this folder will be the root folder for all the SAP deployment configuration files. Create the following folders in the 'WORKSPACES' folder: 'DEPLOYER', 'LIBRARY', 'LANDSCAPE' and 'SYSTEM'. These will contain the configuration files for the different components of the SAP Deployment Automation Framework. 

Optionally you may copy the sample configuration files from the 'samples/WORKSPACES' folders to the WORKSPACES folder you created, this will allow you to experiment with sample deployments.

Push the changes back to the repository by selecting the source control icon and providing a message about the change, for example: "Import of sample configurations" and press Cntr-Enter to commit the changes. Next select the _Sync Changes_ button to synchronize the changes back to the repository.

## Create Azure Pipelines

Azure Pipelines are implemented as YAML files and they're stored in the 'deploy/pipelines' folder in the repository. 
## Control plane deployment pipeline

Create the control plane deployment pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                           |
| ------- | ----------------------------------------------- |
| Branch  | main                                            |
| Path    | `deploy/pipelines/01-deploy-control-plane.yaml` |
| Name    | Control plane deployment                        |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'Control plane deployment' by choosing 'Rename/Move' from the three-dot menu on the right.

## SAP workload zone deployment pipeline

Create the SAP workload zone pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                        |
| ------- | -------------------------------------------- |
| Branch  | main                                         |
| Path    | `deploy/pipelines/02-sap-workload-zone.yaml` |
| Name    | SAP workload zone deployment                 |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'SAP workload zone deployment' by choosing 'Rename/Move' from the three-dot menu on the right.

## SAP system deployment pipeline

Create the SAP system deployment pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                            |
| ------- | ------------------------------------------------ |
| Branch  | main                                             |
| Path    | `deploy/pipelines/03-sap-system-deployment.yaml` |
| Name    | SAP system deployment (infrastructure)           |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'SAP system deployment (infrastructure)' by choosing 'Rename/Move' from the three-dot menu on the right.

## SAP software acquisition pipeline

Create the SAP software acquisition pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                            |
| ------- | ------------------------------------------------ |
| Branch  | main                                             |
| Path    | `deploy/pipelines/04-sap-software-download.yaml` |
| Name    | SAP software acquisition                         |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'SAP software acquisition' by choosing 'Rename/Move' from the three-dot menu on the right.

## SAP configuration and software installation pipeline

Create the SAP configuration and software installation pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                              |
| ------- | -------------------------------------------------- |
| Branch  | main                                               |
| Path    | `deploy/pipelines/05-DB-and-SAP-installation.yaml` |
| Name    | Configuration and SAP installation                 |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'SAP configuration and software installation' by choosing 'Rename/Move' from the three-dot menu on the right.

## Deployment removal pipeline

Create the deployment removal pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                        |
| ------- | -------------------------------------------- |
| Branch  | main                                         |
| Path    | `deploy/pipelines/10-remover-terraform.yaml` |
| Name    | Deployment removal                           |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'Deployment removal' by choosing 'Rename/Move' from the three-dot menu on the right.

## Deployment removal pipeline using Azure Resource Manager

Create the deployment removal ARM pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                           |
| ------- | ----------------------------------------------- |
| Branch  | main                                            |
| Path    | `deploy/pipelines/11-remover-arm-fallback.yaml` |
| Name    | Deployment removal using ARM                    |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'Deployment removal using ARM' by choosing 'Rename/Move' from the three-dot menu on the right.

> [!NOTE]
> Only use this pipeline as last resort, removing just the resource groups will leave remnants that may complicate re-deployments.

## Repository updater pipeline

Create the Repository updater pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipeline YAML File. Specify the pipeline with the following settings:

| Setting | Value                                           |
| ------- | ----------------------------------------------- |
| Branch  | main                                            |
| Path    | `deploy/pipelines/20-update-ado-repository.yaml`|
| Name    | Repository updater                              |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'Repository updater' by choosing 'Rename/Move' from the three-dot menu on the right.

This pipeline should be used when there's an update in the sap-automation repository that you want to use.

## Import Ansible task from Visual Studio Marketplace

The pipelines use a custom task to run Ansible. The custom task can be installed from [Ansible](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.vss-services-ansible). Install it to your Azure DevOps organization before running the _Configuration and SAP installation_ or _SAP software acquisition_  pipelines.

## Import Cleanup task from Visual Studio Marketplace

The pipelines use a custom task to perform cleanup activities post deployment. The custom task can be installed from [Post Build Cleanup](https://marketplace.visualstudio.com/items?itemName=mspremier.PostBuildCleanup). Install it to your Azure DevOps organization before running the pipelines.


## Preparations for self-hosted agent    


1. Create an Agent Pool by navigating to the Organizational Settings and selecting _Agent Pools_ from the Pipelines section. Click the _Add Pool_ button and choose Self-hosted as the pool type. Name the pool to align with the workload zone environment, for example `DEV-WEEU-POOL`. Ensure _Grant access permission to all pipelines_ is selected and create the pool using the _Create_ button.

1. Sign in with the user account you plan to use in your Azure DevOps organization (https://dev.azure.com).

1. From your home page, open your user settings, and then select _Personal access tokens_.

   :::image type="content" source="./media/automation-devops/automation-select-personal-access-tokens.jpg" alt-text="Diagram showing the creation of the Personal Access Token (PAT).":::

1. Create a personal access token. Ensure that _Read & manage_ is selected for _Agent Pools_ and _Read & write_ is selected for _Code_. Write down the created token value.

   :::image type="content" source="./media/automation-devops/automation-new-pat.png" alt-text="Diagram showing the attributes of the Personal Access Token (PAT).":::

## Variable definitions

The deployment pipelines are configured to use a set of predefined parameter values. In Azure DevOps the variables are defined using variable groups.


### Common variables

There's a set of common variables that are used by all the deployment pipelines. These variables are stored in a variable group called 'SDAF-General'.

Create a new variable group 'SDAF-General' using the Library page in the Pipelines section. Add the following variables:

| Variable                           | Value                                   | Notes                                                                                       |
| ---------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------- |
| `ANSIBLE_HOST_KEY_CHECKING`        | false                                   |                                                                                             |
| Deployment_Configuration_Path      | WORKSPACES                              | For testing the sample configuration use 'samples/WORKSPACES' instead of WORKSPACES.        |
| Branch                             | main                                    |                                                                                             |
| S-Username                         | `<SAP Support user account name>`       |                                                                                             |
| S-Password                         | `<SAP Support user password>`           | Change variable type to secret by clicking the lock icon.                                   |
| `PAT`                              | `<Personal Access Token>`               | Use the Personal Token defined in the previous step.                                        |
| `POOL`                             | `<Agent Pool name>`                     | Use the Agent pool defined in the previous step.                                            |
| `advice.detachedHead`              | false                                   |                                                                                             |
| `skipComponentGovernanceDetection` | true                                    |                                                                                             |
| `tf_version`                       | 1.1.7                                   | The Terraform version to use, see [Terraform download](https://www.terraform.io/downloads)  |

Save the variables.

> [!NOTE]
> Remember to assign permissions for all pipelines using _Pipeline permissions_.

### Environment specific variables

As each environment may have different deployment credentials you'll need to create a variable group per environment, for example 'SDAF-MGMT','SDAF-DEV', 'SDAF-QA'. 

Create a new variable group 'SDAF-MGMT' for the control plane environment using the Library page in the Pipelines section. Add the following variables:

| Variable              | Value                                                              | Notes                                                    |
| --------------------- | ------------------------------------------------------------------ | -------------------------------------------------------- |
| Agent                 | 'Azure Pipelines' or the name of the agent pool                    | Note, this pool will be created in a later step.         |
| ARM_CLIENT_ID         | Enter the Service principal application ID.                        |                                                          |
| ARM_CLIENT_SECRET     | Enter the Service principal password.                              | Change variable type to secret by clicking the lock icon |
| ARM_SUBSCRIPTION_ID   | Enter the target subscription ID.                                  |                                                          |
| ARM_TENANT_ID         | Enter the Tenant ID for the service principal.                     |                                                          |
| AZURE_CONNECTION_NAME | Previously created connection name.                                |                                                          |
| sap_fqdn              | SAP Fully Qualified Domain Name, for example 'sap.contoso.net'.    | Only needed if Private DNS isn't used.                   |
| FENCING_SPN_ID        | Enter the service principal application ID for the fencing agent.  | Required for highly available deployments.               |
| FENCING_SPN_PWD       | Enter the service principal password for the fencing agent.        | Required for highly available deployments.               |
| FENCING_SPN_TENANT    | Enter the service principal tenant ID for the fencing agent.       | Required for highly available deployments.               |

Save the variables.

> [!NOTE]
> Remember to assign permissions for all pipelines using _Pipeline permissions_.
>
> You can use the clone functionality to create the next environment variable group.

## Create a service connection

To remove the Azure resources, you need an Azure Resource Manager service connection. For more information, see [Manage service connections](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&preserve-view=true)

To create the service connection, go to Project settings and navigate to the Service connections setting in the Pipelines section.

:::image type="content" source="./media/automation-devops/automation-create-service-connection.png" alt-text="Picture showing how to create a Service connection":::

Choose _Azure Resource Manager_ as the service connection type and _Service principal (manual)_ as the authentication method. Enter the target subscription, typically the control plane subscription, and provide the service principal details. Validate the credentials using the _Verify_ button. For more information on how to create a service principal, see [Creating a Service Principal](automation-deploy-control-plane.md#prepare-the-deployment-credentials).

Enter a Service connection name, for instance 'Connection to MGMT subscription' and ensure that the _Grant access permission to all pipelines_ checkbox is checked. Select _Verify and save_ to save the service connection.

## Permissions

> [!NOTE]
> Most of the pipelines will add files to the Azure Repos and therefore require pull permissions. Assign "Contribute" permissions to the 'Build Service' using the Security tab of the source code repository in the Repositories section in Project settings. 

:::image type="content" source="./media/automation-devops/automation-repo-permissions.png" alt-text="Picture showing repository permissions":::

## Register the Deployer as a self-hosted agent for Azure DevOps

You must use the Deployer as a [self-hosted agent for Azure DevOps](/azure/devops/pipelines/agents/v2-linux) to perform the Ansible configuration activities. As a one-time step, you must register the Deployer as a self-hosted agent.


## Deploy the Control Plane

Newly created pipelines might not be visible in the default view. Select on recent tab and go back to All tab to view the new pipelines.

Select the _Control plane deployment_ pipeline, provide the configuration names for the deployer and the SAP library and choose "Run" to deploy the control plane. 

Wait for the deployment to finish.

## Configure the Azure DevOps Services self-hosted agent

Connect to the deployer by following these steps:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Navigate to the resource group containing the deployer virtual machine.

1. Connect to the virtual machine using Azure Bastion.

1. The default username is *azureadm*

1. Choose *SSH Private Key from Azure Key Vault* 

1. Select the subscription containing the control plane.

1. Select the deployer key vault.

1. From the list of secrets choose the secret ending with *-sshkey*.

1. Connect to the virtual machine.

Run the following script to configure the deployer.

```bash
mkdir -p ~/Azure_SAP_Automated_Deployment

cd ~/Azure_SAP_Automated_Deployment

git clone https://github.com/Azure/sap-automation.git

cd sap-automation/deploy/scripts

./configure_deployer.sh
```

Reboot the deployer and reconnect and run the following script to set up the Azure DevOps agent.

```bash
cd ~/Azure_SAP_Automated_Deployment/

$DEPLOYMENT_REPO_PATH/deploy/scripts/setup_ado.sh
```

Accept the license and when prompted for server URL, enter the URL you captured when you created the Azure DevOps Project. For authentication, choose PAT and enter the token value from the previous step.

When prompted enter the application pool name, you created in the previous step. Accept the default agent name and the default work folder name.
The agent will now be configured and started.

## Next step

> [!div class="nextstepaction"]
> [DevOps hands on lab](automation-devops-tutorial.md)
