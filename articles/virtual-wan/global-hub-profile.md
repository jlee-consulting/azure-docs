---
title: 'Download Azure Virtual WAN global or hub-based VPN profiles'
description: Learn how Azure Virtual WAN offers two types of connectivity for remote users and how to download a profile. 
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/30/2022
ms.author: cherylmc

---

# Download a global or hub-based profile for User VPN clients

Azure Virtual WAN offers two types of connectivity for User VPN (P2S) remote users: global and hub-based. Use the following sections to learn about profile types and how to download them.

## Global profile

The global profile associated with a User VPN configuration points to a load balancer that includes all active User VPN hubs that are using that User VPN configuration. A user connected to the global profile is directed to the hub that's closest to the user's geographic location. This type of connectivity is useful when users travel to different locations frequently.

For example, you can associate a VPN configuration with two Virtual WAN hubs, one in West US and one in Southeast Asia. If a user connects to the global profile associated with the User VPN configuration, they'll connect to the closest Virtual WAN hub based on their location.

> [!IMPORTANT]
> If a Point-to-site VPN configuration used for a global profile is configured to authenticate users using the RADIUS protocol, make sure "Use Remote/On-premises RADIUS server" is turned on for all Point-to-site VPN Gateways using that configuration. Additionally, ensure your RADIUS server is configured to accept authentication requests from the RADIUS proxy IP addresses of **all** Point-to-site VPN Gateways using this VPN configuration.

To download the global profile:

1. Go to the **Virtual WAN**.
1. In the left pane, select **User VPN configurations**.
1. Select the configuration for which you want to download the profile.
1. Select **Download virtual WAN user VPN profile**.

   :::image type="content" source="./media/global-hub-profile/global.png" alt-text="Screenshot that shows selections for downloading a global profile." lightbox="./media/global-hub-profile/global.png":::

1. On the download page, select **EAPTLS**, then **Generate and download profile**. A profile package (zip file) containing the client configuration settings is generated and downloads to your computer. The contents of the package depend on the authentication and tunnel type choices for your configuration.

### Include or exclude a hub from a global profile

By default, every hub that uses a specific User VPN configuration is included in the corresponding global VPN profile. You can choose to exclude a hub from the global VPN profile. If you do, a user won't be load balanced to connect to that hub's gateway if they're using the global VPN profile.

To check whether or not the hub is included in the global VPN profile:

1. Go to the **Virtual WAN**.
1. On the **Overview** page, select **Hubs**.
1. On the **Hubs** page, click the hub.
1. On the page for the virtual hub, in the left pane, select **User VPN (Point to site)**.
1. On the **User VPN (Point-to-site)** page, see **Gateway attachment state** to determine if this hub is included in the global VPN profile. If the state is **attached**, the hub is included. If the state is **detached**, the hub isn't included.

   :::image type="content" source="./media/global-hub-profile/attachment-state.png" alt-text="Screenshot that shows the attachment state of a gateway."lightbox="./media/global-hub-profile/attachment-state.png":::

To include or exclude a specific hub from the global VPN profile:

1. Select **Include/Exclude Gateway from Global Profile**.

   :::image type="content" source="./media/global-hub-profile/select-include-exclude.png" alt-text="Screenshot that shows the button for including or excluding a hub from a profile." lightbox="./media/global-hub-profile/select-include-exclude.png":::

1. On the **Include/Exclude** page, make one of the following choices.

   * Select **Exclude** if you want to remove this hub's gateway from the Virtual WAN global User VPN profile. Users who are using the hub-level User VPN profile will still be able to connect to this gateway. Users who are using the WAN-level profile (the global profile) won't be able to connect to this gateway.

   * Select **Include** if you want to include this hub's gateway in the Virtual WAN global User VPN profile. Users who are using this WAN-level profile (this global profile) will be able to connect to this gateway.

## Hub-based profile

The profile points to a single hub. The user can connect to only the particular hub by using this profile. To download the hub-based profile:

1. Go to the **virtual hub**.
1. In the left pane, select **User VPN (Point to site)**.
1. Select **Download virtual Hub User VPN profile**.

   :::image type="content" source="./media/global-hub-profile/hub-profile.png" alt-text="Screenshot that shows how to download a hub profile." lightbox="./media/global-hub-profile/hub-profile.png":::

1. On the download page, select **EAPTLS**, then **Generate and download profile**. A profile package (zip file) containing the client configuration settings is generated and downloads to your computer. The contents of the package depend on the authentication and tunnel type choices for your configuration.

## Next steps

For more information about User VPNs, see [Create User VPN point-to-site connections](virtual-wan-point-to-site-portal.md).
