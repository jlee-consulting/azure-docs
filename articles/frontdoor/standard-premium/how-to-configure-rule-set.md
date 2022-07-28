---
title: 'Configure a Rule set - Azure Front Door'
description: This article provides guidance on how to configure a Rule set you can use in an Azure Front Door profile. 
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/17/2022
ms.author: duau
---

# Configure a Rule set with Azure Front Door

This article shows how to create a Rule Set and your first set of rules using the Azure portal. You'll then learn how to associate the Rule set to a route from the Rule set page or from Endpoint Manager.

## Prerequisites

* Before you can configure a Rule Set, you must first create an Azure Front Door Standard/Premium. For more information, see [Quickstart: Create an Azure Front Door Standard/Premium profile](../create-front-door-portal.md).

## Configure Rule set in Azure portal

1. Within your Azure Front Door profile, select **Rule set** located under **Settings**. Select **+ Add**, then give rule set a name.

   :::image type="content" source="../media/how-to-configure-rule-set/create-rule-set.png" alt-text="Screenshot of rule set landing page.":::
    
1. To create your first rule, give it rule name. Then select **+ Add condition** and **+ Add action** to define your rule. You can add up to 10 conditions and 5 actions for one rule. In this example, we use server variable to append "Device type" to the response header for requests that are coming in from a "Mobile" device type.

   :::image type="content" source="../media/how-to-configure-rule-set/rule-set-configurations.png" alt-text="Screenshot of rule set configuration page.":::
    
    > [!NOTE]
    > * To delete a condition or action from a rule, use the trash can on the right-hand side of the specific condition or action.
    > * To create a rule that applies to all incoming traffic, do not specify any conditions.
    > * To stop evaluating remaining rules if a specific rule is met, check **Stop evaluating remaining rule**. If this option is checked and all remaining rules in the Rule Set will not be executed regardless if the matching conditions were met.
    > * All paths in Rules Engine are case sensitive.
    > * Header names should adhere to [RFC 7230](https://datatracker.ietf.org/doc/html/rfc7230#section-3.2.6).

1. You can determine the priority of the rules within your Rule Set by using the arrow buttons to move the rules higher or lower in priority. The list is in ascending order, so the most important rule is listed first.

   :::image type="content" source="../media/how-to-configure-rule-set/rule-set-change-orders.png" alt-text="Screenshot of rule set priority." lightbox="../media/how-to-configure-rule-set/rule-set-change-orders-expanded.png":::

    > [!TIP]
    > If you like to verify when the changes are propagated to Azure Front Door, you can create a custom response header in the rule using the example below. You can add a response header `_X-<RuleName>-Version_`  and change the value each time rule is updated.
    >  
    > :::image type="content" source="./../media/front-door-rules-engine/rules-version.png" alt-text="Screenshot of custom version header rule." lightbox="./../media/front-door-rules-engine/rules-version-expanded.png":::
    > After the changes are updated, you can go to the URL to confirm the rule version being invoked: 
    > :::image type="content" source="./../media/front-door-rules-engine/version-output.png" alt-text="Screenshot of custom header version output.":::

1. Once you've created all the rules you need,  select **Save** to complete the creation of your Rule set.

1. Now you can associate the Rule Set to a route so it can take effect. You can associate the Rules set on the Rule Set page or you can do so from the Front Door manager.
 
    **Rule set page**: 
    
    1. From the *Rule set page*, select the **Unassociated** link to associate the Rule set to a route.
    
        :::image type="content" source="../media/how-to-configure-rule-set/associate-rule-set.png" alt-text="Screenshot of unassociated rule set on Rule set page.":::  
     
    1. Then in the **Associate a route** page, select the endpoint and route you want to associate with the Rule Set. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/associate-rule-set-route.png" alt-text="Screenshot of create a route page.":::    
        
    1. Select **Next** to change the rule set order if you have multiple rule sets for a selected route. The rule set will process in the order listed. You can change orders by selecting the rule set and selecting the buttons at the top of the page. Select *Associate* to complete the route association.
    
        > [!Note]
        > You can only associate one rule set with a single route on this page. To associate a Rule set with multiple routes, use the Front Door manager.
    
        :::image type="content" source="../media/how-to-configure-rule-set/rule-set-orders.png" alt-text="Screenshot of rule set orders.":::
    
    1. The rule set is now associated with a route. You can look at the response header and confirm that the Device Type is added.
    
        :::image type="content" source="../media/how-to-configure-rule-set/rule-set-associated.png" alt-text="Screenshot of rule associated with a route.":::

   **Front Door manager**: 
    
    1. Go to Front Door manager, select the **...** next to the route you want to configure. Then select **Edit route**.
    
        :::image type="content" source="../media/how-to-configure-rule-set/manager-edit-route.png" alt-text="Screenshot of edit route from Front Door manager." lightbox="../media/how-to-configure-rule-set/manager-edit-route-expanded.png":::
    
    1. On the **Update route** page, under *Rules*, select the Rule sets you want to associate with the route from the dropdown. Then you have the ability change the order of the rule sets. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/route-rule-set-update.png" alt-text="Screenshot of rule set on update a route page.":::
    
    1. Select **Update** to save the route configuration.

## Delete a Rule set

If you no longer want the Rule set in your Azure Front Door profile, you can remove the Rule set by completing the following steps:

1. Go to the **Rule set** page under **Settings**.

1. Select the **...** next to the rule set you want to remove and then select **Disassociate from all routes**.

   :::image type="content" source="../media/how-to-configure-rule-set/disassociate-rule-set.png" alt-text="Screenshot of disassociate all routes button.":::

1. Once the rule set has been disassociated, you can select the **...** again. Select **Delete** and then select **Yes** to confirm you want to delete the rule set.

   :::image type="content" source="../media/how-to-configure-rule-set/remove-rule-set.png" alt-text="Screenshot of delete a rule set from Rule set page.":::

1. Repeat steps 2 and 3 to remove any other rule set you have in the Azure Front Door profile.

## Next steps

Learn how to add [Security headers with Rules Set](how-to-add-security-headers.md).
