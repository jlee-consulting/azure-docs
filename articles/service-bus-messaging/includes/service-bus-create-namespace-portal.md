---
 title: include file
 description: include file
 services: service-bus-messaging
 author: spelluru
 ms.service: service-bus-messaging
 ms.topic: include
 ms.date: 09/01/2021
 ms.author: spelluru
 ms.custom: include file
---

## Create a namespace in the Azure portal
To begin using Service Bus messaging entities in Azure, you must first create a namespace with a name that is unique across Azure. A namespace provides a scoping container for addressing Service Bus resources within your application.

To create a namespace:

1. Sign in to the [Azure portal](https://portal.azure.com)
2. In the left navigation pane of the portal, select **+ Create a resource**, select **Integration**, and then select **Service Bus**.

    :::image type="content" source="./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png" alt-text="Image showing selection of Create a resource, Integration, and then Service Bus in the menu.":::
3. In the **Basics** tag of the **Create namespace** page, follow these steps: 
    1. For **Subscription**, choose an Azure subscription in which to create the namespace.
    1. For **Resource group**, choose an existing resource group in which the namespace will live, or create a new one.      
    1. Enter a **name for the namespace**. The system immediately checks to see if the name is available. For a list of rules for naming namespaces, see [Create Namespace REST API](/rest/api/servicebus/create-namespace).
    1. For **Location**, choose the region in which your namespace should be hosted.1. 
    1. For **Pricing tier**, select the pricing tier (Basic, Standard, or Premium) for the namespace. If you want to use [topics and subscriptions](../service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), choose either Standard or Premium. Topics/subscriptions are not supported in the Basic pricing tier. If you selected the **Premium** pricing tier, specify the number of **messaging units**. The premium tier provides resource isolation at the CPU and memory level so that each workload runs in isolation. This resource container is called a messaging unit. A premium namespace has at least one messaging unit. You can select 1, 2, or 4 messaging units for each Service Bus Premium namespace. For more information, see [Service Bus Premium Messaging](../service-bus-premium-messaging.md).
    7. Select **Review + create**. The system now creates your namespace and enables it. You might have to wait several minutes as the system provisions resources for your account.
   
        :::image type="content" source="./media/service-bus-create-namespace-portal/create-namespace.png" alt-text="Image showing the Create a namespace page":::
    1. On the **Review + create** page, review settings, and select **Create**. 
4. Select **Go to resource** on the deployment page. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/deployment-alert.png" alt-text="Image showing the deployment succeeded page with the Go to resource link.":::
6. You see the home page for your service bus namespace. 

    :::image type="content" source="./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png" alt-text="Image showing the home page of the Service Bus namespace created." :::

## Get the connection string 
Creating a new namespace automatically generates an initial Shared Access Signature (SAS) rule with an associated pair of primary and secondary keys that each grant full control over all aspects of the namespace. See [Service Bus authentication and authorization](../service-bus-authentication-and-authorization.md) for information about how to create rules with more constrained rights for regular senders and receivers. To copy the primary and secondary keys for your namespace, follow these steps: 

1. Click **All resources**, then click the newly created namespace name.
2. In the namespace window, click **Shared access policies**.
3. In the **Shared access policies** screen, click **RootManageSharedAccessKey**.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-info.png" alt-text="Screenshot shows the Shared access policies window with a policy highlighted.":::
4. In the **Policy: RootManageSharedAccessKey** window, click the copy button next to **Primary Connection String**, to copy the connection string to your clipboard for later use. Paste this value into Notepad or some other temporary location.
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-string.png" alt-text="Screenshot shows an S A S policy called RootManageSharedAccessKey, which includes keys and connection strings.":::
5. Repeat the previous step, copying and pasting the value of **Primary key** to a temporary location for later use.

<!--Image references-->

