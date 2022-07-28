---
title: "Quickstart - Build and deploy apps to Azure Spring Apps"
description: Describes app deployment to Azure Spring Apps.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/15/2021
ms.custom: devx-track-java, devx-track-azurecli, mode-other, event-tier1-build-2022
zone_pivot_groups: programming-languages-spring-cloud
---

# Quickstart: Build and deploy apps to Azure Spring Apps

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Basic/Standard tier ❌ Enterprise tier

::: zone pivot="programming-language-csharp"
In this quickstart, you build and deploy Spring applications to Azure Spring Apps using the Azure CLI.

## Prerequisites

* Complete the previous quickstarts in this series:

  * [Provision Azure Spring Apps service](./quickstart-provision-service-instance.md).
  * [Set up Azure Spring Apps configuration server](./quickstart-setup-config-server.md).

## Download the sample app

If you've been using the Azure Cloud Shell up to this point, switch to a local command prompt for the following steps.

1. Create a new folder and clone the sample app repository.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Navigate into the repository directory.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## Deploy PlanetWeatherProvider

1. Create an app for the PlanetWeatherProvider project in your Azure Spring Apps instance.

   ```azurecli
   az spring app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   To enable automatic service registration, you have given the app the same name as the value of `spring.application.name` in the project's *appsettings.json* file:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   This command may take several minutes to run.

1. Change directory to the `PlanetWeatherProvider` project folder.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Create the binaries and the *.zip* file to be deployed.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > The project file contains the following XML to package the binaries in a *.zip* file after writing them to the *./publish* folder:
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Deploy to Azure.

   Make sure that the command prompt is in the project folder before running the following command.

   ```azurecli
   az spring app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   The `--main-entry` option specifies the relative path from the *.zip* file's root folder to the *.dll* file that contains the application's entry point. After the service uploads the *.zip* file, it extracts all the files and folders and tries to execute the entry point in the specified *.dll* file.

   This command may take several minutes to run.

## Deploy SolarSystemWeather

1. Create another app in your Azure Spring Apps instance, this time for the SolarSystemWeather project:

   ```azurecli
   az spring app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` is the name that is specified in the `SolarSystemWeather` project's *appsettings.json* file.

   This command may take several minutes to run.

1. Change directory to the `SolarSystemWeather` project.

   ```console
   cd ../solar-system-weather
   ```

1. Create the binaries and *.zip* file to be deployed.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Deploy to Azure.

   ```azurecli
   az spring app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```

   This command may take several minutes to run.

## Assign public endpoint

To test the application, send an HTTP GET request to the `solar-system-weather` application from a browser.  To do that, you need a public endpoint for the request.

1. To assign the endpoint, run the following command.

   ```azurecli
   az spring app update -n solar-system-weather --assign-endpoint true
   ```

1. To get the URL of the endpoint, run the following command.

   Windows:

   ```azurecli
   az spring app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring app show --name solar-system-weather | grep url
   ```

## Test the application

Send a GET request to the `solar-system-weather` app. In a browser, navigate to the public URL with `/weatherforecast` appended to the end of it. For example: `https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast`

The output is JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

This response shows that both Spring apps are working. The `SolarSystemWeather` app returns data that it retrieved from the `PlanetWeatherProvider` app.
::: zone-end
---
::: zone pivot="programming-language-java"
This document explains how to build and deploy Spring applications to Azure Spring Apps using:
* Azure CLI
* Maven Plugin
* Intellij

Before deployment using Azure CLI or Maven, complete the examples that [provision an instance of Azure Spring Apps](./quickstart-provision-service-instance.md) and [set up the config server](./quickstart-setup-config-server.md). For enterprise tier, please follow [set up Application Configuration Service](./how-to-enterprise-application-configuration-service.md).

## Prerequisites

* [Install JDK 8 or JDK 11](/azure/developer/java/fundamentals/java-jdk-install)
* [Sign up for an Azure subscription](https://azure.microsoft.com/free/)
* (Optional) [Install the Azure CLI version 2.0.67 or higher](/cli/azure/install-azure-cli) and install the Azure Spring Apps extension with command: `az extension add --name spring`
* (Optional) [Install the Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) and [sign in](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## Deployment procedures

#### [CLI](#tab/Azure-CLI)

## Build the Spring applications locally

1. Clone the sample app repository to your Azure Cloud account.  Change the directory, and build the project.

   ```azurecli
   git clone https://github.com/azure-samples/spring-petclinic-microservices
   cd spring-petclinic-microservices
   mvn clean package -DskipTests -Denv=cloud
   ```

Compiling the project takes 5-10 minutes. Once completed, you should have individual JAR files for each service in their respective folders.

## Create and deploy apps on Azure Spring Apps

1. If you didn't run the following commands in the previous quickstarts, set the CLI defaults.

   ```azurecli
   az configure --defaults group=<resource-group-name> spring=<service-name>
   ```

1. Create the 2 core Spring applications for PetClinic: API gateway and customers-service.

   ```azurecli
   az spring app create --name api-gateway --instance-count 1 --memory 2Gi --assign-endpoint
   az spring app create --name customers-service --instance-count 1 --memory 2Gi
   ```

1. Deploy the JAR files built in the previous step.

   ```azurecli
   az spring app deploy \
       --name api-gateway \
       --jar-path spring-petclinic-api-gateway/target/spring-petclinic-api-gateway-2.5.1.jar \
       --jvm-options="-Xms2048m -Xmx2048m"
   az spring app deploy \
       --name customers-service \
       --jar-path spring-petclinic-customers-service/target/spring-petclinic-customers-service-2.5.1.jar \
       --jvm-options="-Xms2048m -Xmx2048m"
   ```

1. Query app status after deployments with the following command.

   ```azurecli
   az spring app list --output table
   ```

   This command produces output similar to the following example:

   ```output
   Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
   -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
   api-gateway        eastus      xxxxxx-sp         default                  https://<service name>-api-gateway.azuremicroservices.io   Succeeded              1      2         1/1                 1/1                    -
   customers-service  eastus      <service name>         default                                                                       Succeeded              1      2         1/1                 1/1                    -
   ```

## Verify the services

Access the app gateway and customers service from browser with the **Public Url** shown above, in the format of `https://<service name>-api-gateway.azuremicroservices.io`.

![Access petclinic customers service](media/quickstart-deploy-apps/access-customers-service.png)

> [!TIP]
> To troubleshot deployments, you can use the following command to get logs streaming in real time whenever the app is running `az spring app logs --name <app name> -f`.

## Deploy extra apps

To get the PetClinic app functioning with all features like Admin Server, Visits and Veterinarians, you can deploy the other apps with following commands:

```azurecli
az spring app create --name admin-server --instance-count 1 --memory 2Gi --assign-endpoint
az spring app create --name vets-service --instance-count 1 --memory 2Gi
az spring app create --name visits-service --instance-count 1 --memory 2Gi
az spring app deploy --name admin-server --jar-path spring-petclinic-admin-server/target/spring-petclinic-admin-server-2.5.1.jar --jvm-options="-Xms2048m -Xmx2048m"
az spring app deploy --name vets-service --jar-path spring-petclinic-vets-service/target/spring-petclinic-vets-service-2.5.1.jar --jvm-options="-Xms2048m -Xmx2048m"
az spring app deploy --name visits-service --jar-path spring-petclinic-visits-service/target/spring-petclinic-visits-service-2.5.1.jar --jvm-options="-Xms2048m -Xmx2048m"
```

#### [Maven](#tab/Maven)

## Build the Spring applications locally

1. Clone the sample app repository to your Azure Cloud account.  Change the directory, and build the project.

   ```azurecli
   git clone https://github.com/azure-samples/spring-petclinic-microservices
   cd spring-petclinic-microservices
   mvn clean package -DskipTests -Denv=cloud
   ```

Compiling the project takes 5 -10 minutes. Once completed, you should have individual JAR files for each service in their respective folders.

## Generate configurations and deploy to the Azure Spring Apps

1. Generate configurations by running the following command in the root folder of Pet Clinic containing the parent POM. If you have already signed-in with Azure CLI, the command will automatically pick up the credentials. Otherwise, it will sign you in with prompt instructions. For more information, see our [wiki page](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

   ```azurecli
   mvn com.microsoft.azure:azure-spring-apps-maven-plugin:1.10.0:config
   ```

   You will be asked to select:

   * **Modules:** Select `api-gateway` and `customers-service`.
   * **Subscription:** This is your subscription used to create an Azure Spring Apps instance.
   * **Service Instance:** This is the name of your Azure Spring Apps instance.
   * **Public endpoint:** In the list of provided projects, enter the number that corresponds with `api-gateway`.  This gives it public access.

1. Verify the `appName` elements in the POM files are correct:

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>com.microsoft.azure</groupId>
               <artifactId>azure-spring-apps-maven-plugin</artifactId>
               <version>1.10.0</version>
               <configuration>
                   <subscriptionId>xxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx</subscriptionId>
                   <clusterName>v-spr-cld</clusterName>
                   <appName>customers-service</appName>
   ```

   Please make sure `appName` texts match the following, remove any prefix if needed and save the file:
   * api-gateway
   * customers-service

1. The POM now contains the plugin dependencies and configurations. Deploy the apps using the following command.

   ```azurecli
   mvn azure-spring-apps:deploy
   ```

## Verify the services

A successful deployment command will return a URL in the form: `https://<service name>-spring-petclinic-api-gateway.azuremicroservices.io`. Use it to navigate to the running service.

![Access Pet Clinic](media/quickstart-deploy-apps/access-customers-service.png)

You can also navigate the Azure portal to find the URL.

1. Navigate to the service.
2. Select **Apps**.
3. Select **api-gateway**.
4. Find the URL on the **api-gateway | Overview** page.

## Deploy extra apps

To get the PetClinic app functioning with all features like Admin Server, Visits and Veterinarians, you can deploy the other Spring applications. Rerun the configuration command and select the following applications.

* admin-server
* vets-service
* visits-service

Correct app names in each `pom.xml` for above modules and then run the `deploy` command again.

#### [IntelliJ](#tab/IntelliJ)

## Import sample project in IntelliJ

1. Download and unzip the source repository for this tutorial, or clone it using Git: `git clone https://github.com/azure-samples/spring-petclinic-microservices`

1. Open IntelliJ **Welcome** dialog, select **Import Project** to open the import wizard.

1. Select `spring-petclinic-microservices` folder.

   ![Import Project](media/quickstart-deploy-apps/import-project-1-pet-clinic.png)

### Deploy api-gateway app to Azure Spring Apps

In order to deploy to Azure you must sign in with your Azure account with Azure Toolkit for IntelliJ, and choose your subscription. For sign-in details, see [Installation and sign-in](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Right-click your project in IntelliJ project explorer, and select **Azure** -> **Deploy to Azure Spring Apps**.

   ![Deploy to Azure 1](media/quickstart-deploy-apps/deploy-to-azure-1-pet-clinic.png)

1. In the **Name** field, append *:api-gateway* to the existing **Name**.
1. In the **Artifact** textbox, select *spring-petclinic-api-gateway-2.5.1*.
1. In the **Subscription** textbox, verify your subscription.
1. In the **Spring Cloud** textbox, select the instance of Azure Spring Apps that you created in [Provision Azure Spring Apps instance](./quickstart-provision-service-instance.md).
1. Set **Public Endpoint** to *Enable*.
1. In the **App:** textbox, select **Create app...**.
1. Enter *api-gateway*, then select **OK**.
1. Specify the memory to 2 GB and JVM options: `-Xms2048m -Xmx2048m`.

   ![Memory JVM options](media/quickstart-deploy-apps/memory-jvm-options.png)

1. In the **Before launch** section of the dialog, double-click *Run Maven Goal*.
1. In the **Working directory** textbox, navigate to the *spring-petclinic-microservices/gateway* folder.
1. In the **Command line** textbox, enter *package -DskipTests*. Select **OK**.

   ![Deploy to Azure OK](media/quickstart-deploy-apps/deploy-to-azure-spring-cloud-2-pet-clinic.png)

1. Start the deployment by selecting the **Run** button at the bottom of the **Deploy Azure Spring Apps app** dialog. The plug-in will run the command `mvn package` on the `api-gateway` app and deploy the jar generated by the `package` command.

### Deploy customers-service and other apps to Azure Spring Apps

Repeat the steps above to deploy `customers-service` and other Pet Clinic apps to Azure Spring Apps:

1. Modify the **Name** and **Artifact** to identify the `customers-service` app.
1. In the **App:** textbox, select **Create app...** to create `customers-service` app.
1. Verify that the **Public Endpoint** option is set to *Disabled*.
1. In the **Before launch** section of the dialog, switch the **Working directory** to the *petclinic/customers-service* folder.
1. Start the deployment by selecting the **Run** button at the bottom of the **Deploy Azure Spring Apps app** dialog.

## Verify the services

Navigate to the URL of the form: `https://<service name>-spring-petclinic-api-gateway.azuremicroservices.io`

![Access Pet Clinic](media/quickstart-deploy-apps/access-customers-service.png)

You can also navigate the Azure portal to find the URL.

1. Navigate to the service
2. Select **Apps**
3. Select **api-gateway**
4. Find the URL on the **api-gateway | Overview** page

## Deploy extra apps

Other Spring applications included in this sample can be deployed similarly.

* admin-server
* vets-service
* visits-service

::: zone-end

## Clean up resources

If you plan to continue working with subsequent quickstarts and tutorials, you might want to leave these resources in place. When no longer needed, delete the resource group, which deletes the resources in the resource group. To delete the resource group by using Azure CLI, use the following commands:

```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## Next steps

> [!div class="nextstepaction"]
> [Quickstart: Set up a Log Analytics workspace](quickstart-setup-log-analytics.md)
