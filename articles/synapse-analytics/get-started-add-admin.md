---
title: 'Tutorial: Get started add an Administrator' 
description: In this tutorial, you'll learn how to add another administrative user to your workspace.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021 
---

# Add an administrator to your Synapse workspace

In this tutorial, you'll learn how to add an administrator to your Synapse workspace. This user will have full control over the workspace.

## Assign the user to the Azure RBAC Owner role at the Synapse workspace level

1. Open the Azure portal.
1. Navigate to your workspace.
1. On the left side, select **Access Control (IAM)**.
1. Click **Add > Add role assignment**.
1. For **Role**, select **Owner**.
1. Pick the user you want to assign. In this example, we will use `ryan@contoso.com`.
1. Click Save.
 
 
## Assign the user to the Synapse Administrator role in the Synapse workspace
1. Open your workspace in Synapse Studio.
1. On the left side, click **Manage** to open the Manage hub.
1. Under **Security**, click **Access control**.
1. Click **Add**.
1. Leave **Scope** set to Workspace.
1. For **Role**, choose **Synapse Administrator**.
1. Then select the user `ryan@contoso.com`.
1. Then click **Apply**.
 
## Assign storage permissions on the Workspace's default storage account
You need to grant access to the Administrator to use that filesystem

1. Open the workspace's primary storage account in the Azure portal.
1. On the left side, click **Access Control (IAM)**.
1. Add `ryan@contoso.com` to the **Owner** role. 
3. Add `ryan@contoso.com` to the **Azure Storage Blob Data Contributor** role

## Add the user to the dbowner role for all dedicated SQL pools

For all dedicated SQL pools, run the following T-SQL script against the corresponding SQL database.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## Next steps

* [Get started with Azure Synapse Analytics](get-started.md)
* [Create a workspace](quickstart-create-workspace.md)
* [Use serverless SQL pool](quickstart-sql-on-demand.md)
