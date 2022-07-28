---
author: karlerickson
ms.author: caiqing
ms.service: spring-cloud
ms.custom: event-tier1-build-2022
ms.topic: include
ms.date: 02/09/2022
---

<!-- 
Use the following line at the end of the Prerequisites section, where relevant. Note that the bullet point is NOT included in the include itself, but should be included on the line you paste in, exactly as shown below. The Prerequisites list should not have any line breaks between bullet points, including this one. These specific instructions are necessary so that the Prerequisites list will build as a single-spaced list, without extra blank spaces.

- [!INCLUDE [install-enterprise-extension](includes/install-enterprise-extension.md)]

-->

The Azure Spring Apps Enterprise tier extension (3.0.0 or later). Use the following command to remove previous versions and install the latest Enterprise tier extension. If you previously installed the `spring-cloud` extension, uninstall it to avoid configuration and version mismatches.

   ```azurecli
   az extension remove --name spring
   az extension add --name spring
   az extension remove --name spring-cloud
   ```
