---
title: Use Application Configuration Service for Tanzu with Azure Spring Apps Enterprise Tier
titleSuffix: Azure Spring Apps Enterprise Tier
description: How to use Application Configuration Service for Tanzu with Azure Spring Apps Enterprise Tier.
author: karlerickson
ms.author: xiading
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/09/2022
ms.custom: devx-track-java, event-tier1-build-2022
---

# Use Application Configuration Service for Tanzu

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ❌ Basic/Standard tier ✔️ Enterprise tier

This article shows you how to use Application Configuration Service for VMware Tanzu® with Azure Spring Apps Enterprise Tier.

[Application Configuration Service for Tanzu](https://docs.pivotal.io/tcs-k8s/0-1/) is one of the commercial VMware Tanzu components. It enables the management of Kubernetes-native ConfigMap resources that are populated from properties defined in one or more Git repositories.

With Application Configuration Service for Tanzu, you have a central place to manage external properties for applications across all environments.

## Prerequisites

- An already provisioned Azure Spring Apps Enterprise tier instance with Application Configuration Service for Tanzu enabled. For more information, see [Quickstart: Provision an Azure Spring Apps service instance using the Enterprise tier](quickstart-provision-service-instance-enterprise.md).

  > [!NOTE]
  > To use Application Configuration Service for Tanzu, you must enable it when you provision your Azure Spring Apps service instance. You cannot enable it after provisioning at this time.

## Manage Application Configuration Service for Tanzu settings

Application Configuration Service for Tanzu supports Azure DevOps, GitHub, GitLab, and Bitbucket for storing your configuration files.

To manage the service settings, open the **Settings** section and add a new entry under the **Repositories** section.

:::image type="content" source="media/enterprise/how-to-enterprise-application-configuration-service/config-service-settings.png" alt-text="Screenshot of where to add a repository." lightbox="media/enterprise/how-to-enterprise-application-configuration-service/config-service-settings.png":::

The properties for each entry are described in the following table.

| Property    | Required? | Description                                                                                                                                                                                                                                                                                                                            |
|-------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name        | Yes       | A unique name to label each Git repository.                                                                                                                                                                                                                                                                                            |
| Patterns    | Yes       | Patterns to search in Git repositories. For each pattern, use a format like *{application}* or *{application}/{profile}* instead of *{application}-{profile}.yml*, and separate the patterns with commas. For more information, see the [Pattern](./how-to-enterprise-application-configuration-service.md#pattern) section. |
| URI         | Yes       | A Git URI (for example, `https://github.com/Azure-Samples/piggymetrics-config` or `git@github.com:Azure-Samples/piggymetrics-config`)                                                                                                                                                                                                  |
| Label       | Yes       | The branch name to search in the Git repository.                                                                                                                                                                                                                                                                                       |
| Search path | No        | Optional search paths, separated by commas, for searching subdirectories of the Git repository.                                                                                                                                                                                                                                        |

### Pattern

Configuration will be pulled from Git backends using what is defined in a pattern. A pattern is a combination of *{application}/{profile}* as described in the following list.

- *{application}* - The name of an application for which the configuration is being retrieved. The value `application` is considered the default application and includes configuration shared across multiple applications. Any other value specifies a specific application and will include properties for both the specified application and shared properties for the default application.
- *{profile}* - Optional. The name of a profile for which properties may be retrieved. An empty value, or the value `default`, includes properties that are shared across any and all profiles. Non-default values include properties for the specified profile and properties for the default profile.

### Authentication

The following image shows the three types of repository authentication supported by Application Configuration Service for Tanzu.

:::image type="content" source="media/enterprise/how-to-enterprise-application-configuration-service/config-service-auth.png" alt-text="Screenshot of where to edit authentication types.":::

- Public repository.

   You don't need extra Authentication configuration when using a public repository. Just select **Public** in the **Authentication** form.

- Private repository with basic authentication.

   The following table shows all the configurable properties used to set up a private Git repository with basic authentication.

   | Property | Required? | Description                                 |
   |----------|-----------|---------------------------------------------|
   | username | Yes       | The username used to access the repository. |
   | password | Yes       | The password used to access the repository. |

- Private repository with SSH authentication.

   The following table shows all configurable properties used to set up a private Git repository with SSH.

   | Property                 | Required? | Description                                                                                                                                                                                                                         |
   |--------------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | Private key              | Yes       | The private key that identifies the Git user. Passphrase-encrypted private keys aren't supported.                                                                                                                                   |
   | Host key                 | No        | The host key of the Git server. If you've connected to the server via Git on the command line, the host key is in your *.ssh/known_hosts* file. Don't include the algorithm prefix, because it's specified in `Host key algorithm`. |
   | Host key algorithm       | No        | The algorithm for `hostKey`: one of `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`, and `ecdsa-sha2-nistp521`. (Required if supplying `Host key`).                                                              |
   | Strict host key checking | No        | Optional value that indicates whether the backend should be ignored if it encounters an error when using the provided `Host key`. Valid values are `true` and `false`. The default value is `true`.                                 |

To validate access to the target URI, select **Validate**. After validation completes successfully, select **Apply** to update the configuration settings.

## Refresh strategies

Use the following steps to refresh your application configuration after you update the configuration file in the Git repository.

1. Load the configuration to Application Configuration Service for Tanzu.

   The refresh frequency is managed by Azure Spring Apps and fixed to 60 seconds.

1. Load the configuration to your application.

A Spring application holds the properties as the beans of the Spring Application Context via the Environment interface. The following list shows several ways to load the new configurations:

- Restart the application. After restarting, the application will always load the new configuration.

- Call the */actuator/refresh* endpoint exposed on the config client via the Spring Actuator.

   To use this method, add the following dependency to your configuration client’s *pom.xml* file.

   ``` xml
   <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

   You can also enable the actuator endpoint by adding the following configurations:

   ```properties
   management.endpoints.web.exposure.include=refresh, bus-refresh, beans, env
   ```

   After reloading the property sources by calling the */actuator/refresh* endpoint, the attributes bound with `@Value` in the beans having the annotation `@RefreshScope` are refreshed.

   ``` java
   @Service
   @Getter @Setter
   @RefreshScope
   public class MyService {
      @Value
      private Boolean activated;
   }
   ```

   Next, use curl with the application endpoint to refresh the new configuration.

   ``` bash
   curl -X POST http://{app-endpoint}/actuator/refresh
   ```

## Configure Application Configuration Service for Tanzu settings using the portal

You can configure Application Configuration Service for Tanzu using the portal by following these steps:

1. Select **Application Configuration Service**.
1. Select **Overview** to view the running state and resources allocated to Application Configuration Service for Tanzu.

   ![Application Configuration Service Overview screen](./media/enterprise/getting-started-enterprise/config-service-overview.png)

1. Select **Settings** and add a new entry in the **Repositories** section with the Git backend information.

1. Select **Validate** to validate access to the target URI. After validation completes successfully, select **Apply** to update the configuration settings.

   ![Application Configuration Service Settings overview](./media/enterprise/getting-started-enterprise/config-service-settings.png)

## Configure Application Configuration Service for Tanzu settings using the CLI

You can configure Application Configuration Service for Tanzu using the CLI, by following these steps:

```azurecli
az spring application-configuration-service git repo add \
    --name <entry-name> \
    --patterns <patterns> \
    --uri <git-backend-uri> \
    --label <git-branch-name>
```

## Use Application Configuration Service for Tanzu with applications using the portal

When you use Application Configuration Service for Tanzu with a Git back end, keep the following items in mind.

To use the centralized configurations, you must bind the app to Application Configuration Service for Tanzu. After binding the app, you'll need to configure which pattern to be used by the app by following these steps:

1. Open the **App binding** tab.

1. Select **Bind app** and choose one app in the dropdown. Select **Apply** to bind.

   :::image type="content" source="media/enterprise/how-to-enterprise-application-configuration-service/config-service-app-bind-dropdown.png" alt-text="Screenshot of where to select the application to bind.":::

   > [!NOTE]
   > When you change the bind/unbind status, you must restart or redeploy the app to for the binding to take effect.

1. Select **Apps**, then select the [pattern(s)](./how-to-enterprise-application-configuration-service.md#pattern) to be used by the apps.

   a. In the left navigation menu, select **Apps** to view the list all the apps.

   b. Select the target app to configure patterns for from the `name` column.

   c. In the left navigation pane, select **Configuration**, then select **General settings**.

   d. In the **Config file patterns** dropdown, choose one or more patterns from the list.

      :::image type="content" source="media/enterprise/how-to-enterprise-application-configuration-service/config-service-pattern.png" alt-text="Screenshot of the pattern selection screen.":::

   e. Select **Save**

## Use Application Configuration Service for Tanzu with applications using the CLI

You can use Application Configuration Service for Tanzu with applications, by using this command:

```azurecli
az spring application-configuration-service bind --app <app-name>
az spring app deploy \
    --name <app-name> \
    --artifact-path <path-to-your-JAR-file> \
    --config-file-pattern <config-file-pattern>
```

## Next steps

- [Azure Spring Apps](index.yml)
