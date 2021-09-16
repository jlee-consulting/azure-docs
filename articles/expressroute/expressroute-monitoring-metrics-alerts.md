---
title: 'Azure ExpressRoute: Monitoring, Metrics, and Alerts'
description: Learn about Azure ExpressRoute monitoring, metrics, and alerts using Azure Monitor, the one stop shop for all metrics, alerting, diagnostic logs across Azure.
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/14/2021
ms.author: duau
---
# ExpressRoute monitoring, metrics, and alerts

This article helps you understand ExpressRoute monitoring, metrics, and alerts using Azure Monitor. Azure Monitor is one stop shop for all metrics, alerting, diagnostic logs across all of Azure.
 
>[!NOTE]
>Using **Classic Metrics** is not recommended.
>

## ExpressRoute metrics

To view **Metrics**, navigate to the *Azure Monitor* page and select *Metrics*. To view **ExpressRoute** metrics, filter by Resource Type *ExpressRoute circuits*. To view **Global Reach** metrics, filter by Resource Type *ExpressRoute circuits* and select an ExpressRoute circuit resource that has Global Reach enabled. To view **ExpressRoute Direct** metrics, filter Resource Type by *ExpressRoute Ports*. 

Once a metric is selected, the default aggregation will be applied. Optionally, you can apply splitting, which will show the metric with different dimensions.

> [!IMPORTANT]
> When viewing ExpressRoute metrics in the Azure portal, select a time granularity of **5 minutes or greater** for best possible results.
> 
> :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/metric-granularity.png" alt-text="Screenshot of time granularity options.":::

### Aggregation Types:

Metrics explorer supports SUM, MAX, MIN, AVG and COUNT as [aggregation types](../azure-monitor/essentials/metrics-charts.md#aggregation). You should use the recommended Aggregation type when reviewing the insights for each ExpressRoute metric.

* Sum: The sum of all values captured during the aggregation interval. 
* Count: The number of measurements captured during the aggregation interval. 
* Average: The average of the metric values captured during the aggregation interval. 
* Min: The smallest value captured during the aggregation interval. 
* Max: The largest value captured during the aggregation interval. 

### ExpressRoute circuit

| Metric | Category | Unit | Aggregation Type | Description | Dimensions |  Exportable via Diagnostic Settings? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [Arp Availability](#arp) | Availability | Percent | Average | ARP Availability from MSEE towards all peers. | PeeringType, Peer |  Yes | 
| [Bgp Availability](#bgp) | Availability | Percent | Average | BGP Availability from MSEE towards all peers. | PeeringType, Peer |  Yes | 
| [BitsInPerSecond](#circuitbandwidth) | Traffic | BitsPerSecond | Average | Bits ingressing Azure per second | PeeringType | No | 
| [BitsOutPerSecond](#circuitbandwidth) | Traffic | BitsPerSecond | Average | Bits egressing Azure per second | PeeringType | No | 
| DroppedInBitsPerSecond | Traffic | BitsPerSecond | Average | Ingress bits of data dropped per second | Peering Type | Yes | 
| DroppedOutBitsPerSecond | Traffic | BitPerSecond | Average | Egress bits of data dropped per second | Peering Type | Yes | 
| GlobalReachBitsInPerSecond | Traffic | BitsPerSecond | Average | Bits ingressing Azure per second | PeeredCircuitSKey | No | 
| GlobalReachBitsOutPerSecond | Traffic | BitsPerSecond | Average | Bits egressing Azure per second | PeeredCircuitSKey | No | 

>[!NOTE]
>Using *GlobalGlobalReachBitsInPerSecond* and *GlobalGlobalReachBitsOutPerSecond* will only be visible if at least one Global Reach connection is established.
>

### ExpressRoute gateways

| Metric | Category | Unit | Aggregation Type | Description | Dimensions | Exportable via Diagnostic Settings? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [CPU utilization](#cpu) | Performance | Count | Average | CPU Utilization of the ExpressRoute Gateway | roleInstance | Yes | 
| [Packets per second](#packets) | Performance | CountPerSecond | Average | Packet count of ExpressRoute Gateway | roleInstance | No | 
| [Count of routes advertised to peer](#advertisedroutes) | Availability | Count | Maximum | Count Of Routes Advertised To Peer by ExpressRouteGateway | roleInstance | Yes | 
| [Count of routes learned from peer](#learnedroutes)| Availability | Count | Maximum | Count Of Routes Learned From Peer by ExpressRouteGateway | roleInstance | Yes | 
| [Frequency of routes changed](#frequency) | Availability | Count | Total | Frequency of Routes change in ExpressRoute Gateway | roleInstance | No | 
| [Number of VMs in virtual network](#vm) | Availability | Count | Maximum | Number of VMs in the Virtual Network | No Dimensions | No | 

### ExpressRoute Gateway connections

| Metric | Category | Unit | Aggregation Type | Description | Dimensions | Exportable via Diagnostic Settings? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [BitsInPerSecond](#connectionbandwidth) | Traffic | BitsPerSecond | Average | Bits ingressing Azure per second | ConnectionName | No | 
| [BitsOutPerSecond](#connectionbandwidth) | Traffic | BitsPerSecond | Average | Bits egressing Azure per second | ConnectionName | No | 
| DroppedInBitsPerSecond | Traffic | BitsPerSecond | Average | Ingress bits of data dropped per second | ConnectionName | Yes | 
| DroppedOutBitsPerSecond | Traffic | BitPerSecond | Average | Egress bits of data dropped per second | ConnectionName | Yes | 

### ExpressRoute Direct

| Metric | Category | Unit | Aggregation Type | Description | Dimensions | Exportable via Diagnostic Settings? | 
| --- | --- | --- | --- | --- | --- | --- | 
| [BitsInPerSecond](#directin) | Traffic | BitsPerSecond | Average | Bits ingressing Azure per second | Link | Yes | 
| [BitsOutPerSecond](#directout) | Traffic | BitsPerSecond | Average | Bits egressing Azure per second | Link | Yes | 
| DroppedInBitsPerSecond | Traffic | BitsPerSecond | Average | Ingress bits of data dropped per second | Link | Yes | 
| DroppedOutBitsPerSecond | Traffic | BitPerSecond | Average | Egress bits of data dropped per second | Link  | Yes | 
| [AdminState](#admin) | Physical Connectivity | Count | Average | Admin state of the port | Link | Yes | 
| [LineProtocol](#line) | Physical Connectivity | Count | Average | Line protocol status of the port | Link | Yes | 
| [RxLightLevel](#rxlight) | Physical Connectivity | Count | Average | Rx Light level in dBm | Link, Lane | Yes | 
| [TxLightLevel](#txlight) | Physical Connectivity | Count | Average | Tx light level in dBm | Link, Lane | Yes |

## Circuits metrics

### <a name = "circuitbandwidth"></a>Bits In and Out - Metrics across all peerings

Aggregation type: *Avg*

You can view metrics across all peerings on a given ExpressRoute circuit.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="circuit metrics":::

### Bits In and Out - Metrics per peering

Aggregation type: *Avg*

You can view metrics for private, public, and Microsoft peering in bits/second.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metrics per peering":::

### <a name = "bgp"></a>BGP Availability - Split by Peer  

Aggregation type: *Avg*

You can view near to real-time availability of BGP (Layer-3 connectivity) across peerings and peers (Primary and Secondary ExpressRoute routers). This dashboard shows the Primary BGP session status is up for private peering and the Second BGP session status is down for private peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP availability per peer":::

### <a name = "arp"></a>ARP Availability - Split by Peering  

Aggregation type: *Avg*

You can view near to real-time availability of [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (Layer-3 connectivity) across peerings and peers (Primary and Secondary ExpressRoute routers). This dashboard shows the Private Peering ARP session status is up across both peers, but down for Microsoft peering for both peers. The default aggregation (Average) was utilized across both peers.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP availability per peer":::

## ExpressRoute Direct Metrics

### <a name = "admin"></a>Admin State - Split by link

Aggregation type: *Avg*

You can view the Admin state for each link of the ExpressRoute Direct port pair. The Admin state represents if the physical port is on or off. This state is required to pass traffic across the ExpressRoute Direct connection.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct admin state":::

### <a name = "directin"></a>Bits In Per Second - Split by link

Aggregation type: *Avg*

You can view the bits in per second across both links of the ExpressRoute Direct port pair. Monitor this dashboard to compare inbound bandwidth for both links.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct bits in per second":::

### <a name = "directout"></a>Bits Out Per Second - Split by link

Aggregation type: *Avg*

You can also view the bits out per second across both links of the ExpressRoute Direct port pair. Monitor this dashboard to compare outbound bandwidth for both links.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct bits out per second":::

### <a name = "line"></a>Line Protocol - Split by link

Aggregation type: *Avg*

You can view the line protocol across each link of the ExpressRoute Direct port pair. The Line Protocol indicates if the physical link is up and running over ExpressRoute Direct. Monitor this dashboard and set alerts to know when the physical connection has gone down.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct line protocol":::

### <a name = "rxlight"></a>Rx Light Level - Split by link

Aggregation type: *Avg*

You can view the Rx light level (the light level that the ExpressRoute Direct port is **receiving**) for each port. Healthy Rx light levels generally fall within a range of -10 dBm to 0 dBm. Set alerts to be notified if the Rx light level falls outside of the healthy range.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light Level":::

### <a name = "txlight"></a>Tx Light Level - Split by link

Aggregation type: *Avg*

You can view the Tx light level (the light level that the ExpressRoute Direct port is **transmitting**) for each port. Healthy Tx light levels generally fall within a range of -10 dBm to 0 dBm. Set alerts to be notified if the Tx light level falls outside of the healthy range.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light Level":::

## ExpressRoute Virtual Network Gateway Metrics

Aggregation type: *Avg*

When you deploy an ExpressRoute gateway, Azure manages the compute and functions of your gateway. There are six gateway metrics available to you to better understand the performance of your gateway:

* CPU Utilization
* Packets per seconds
* Count of routes advertised to peers
* Count of routes learned from peers
* Frequency of routes changed
* Number of VMs in the virtual network  

It's highly recommended you set alerts for each of these metrics so that you are aware of when your gateway could be seeing performance issues.

### <a name = "cpu"></a>CPU Utilization - Split Instance

Aggregation type: *Avg*

You can view the CPU utilization of each gateway instance. The CPU utilization may spike briefly during routine host maintenance but prolong high CPU utilization could indicate your gateway is reaching a performance bottleneck. Increasing the size of the ExpressRoute gateway may resolve this issue. Set an alert for how frequent the CPU utilization exceeds a certain threshold.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Screenshot of CPU utilization - split metrics.":::

### <a name = "packets"></a>Packets Per Second - Split by Instance

Aggregation type: *Avg*

This metric captures the number of inbound packets traversing the ExpressRoute gateway. You should expect to see a consistent stream of data here if your gateway is receiving traffic from your on-premises network. Set an alert for when the number of packets per second drops below a threshold indicating that your gateway is no longer receiving traffic.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Screenshot of packets per second - split metrics.":::

### <a name = "advertisedroutes"></a>Count of Routes Advertised to Peer - Split by Instance

Aggregation type: *Count*

This metric is the count for the number of routes the ExpressRoute gateway is advertising to the circuit. The address spaces may include virtual networks that are connected using VNet peering and uses remote ExpressRoute gateway. You should expect the number of routes to remain consistent unless there are frequent changes to the virtual network address spaces. Set an alert for when the number of advertised routes drop below the threshold for the number of virtual network address spaces you're aware of.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Screenshot of count of routes advertised to peer.":::

### <a name = "learnedroutes"></a>Count of Routes Learned from Peer - Split by Instance

Aggregation type: *Max*

This metric shows the number of routes the ExpressRoute gateway is learning from peers connected to the ExpressRoute circuit. These routes can be either from another virtual network connected to the same circuit or learned from on-premises. Set an alert for when the number of learned routes drop below a certain threshold. This could indicate either the gateway is seeing a performance problem or remote peers are no longer advertising routes to the ExpressRoute circuit. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Screenshot of count of routes learned from peer.":::

### <a name = "frequency"></a>Frequency of Routes change - Split by Instance

Aggregation type: *Sum*

This metric shows the frequency of routes being learned from or advertised to remote peers. You should first investigate your on-premises devices to understand why the network is changing so frequently. A high frequency in routes change could indicate a performance problem on the ExpressRoute gateway where scaling the gateway SKU up may resolve the problem. Set an alert for a frequency threshold to be aware of when your ExpressRoute gateway is seeing abnormal route changes.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Screenshot of frequency of routes changed metric.":::

### <a name = "vm"></a>Number of VMs in the Virtual Network

Aggregation type: *Max*

This metric shows the number of virtual machines that are using the ExpressRoute gateway. The number of virtual machines may include VMs from peered virtual networks that use the same ExpressRoute gateway. Set an alert for this metric if the number of VMs goes above a certain threshold that could affect the gateway performance. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Screenshot of number of virtual machines in the virtual network metric.":::

## <a name = "connectionbandwidth"></a>ExpressRoute gateway connections in bits/seconds

Aggregation type: *Avg*

This metric shows the bandwidth usage for a specific connection to an ExpressRoute circuit.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Screenshot of gateway connection bandwidth usage metric.":::

## Alerts for ExpressRoute gateway connections

1. To configure alerts, navigate to **Azure Monitor**, then select **Alerts**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. Select **+Select Target** and select the ExpressRoute gateway connection resource.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="target":::
3. Define the alert details.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="action group":::
4. Define and add the action group.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="add action group":::

## Alerts based on each peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="each peering":::

## Configure alerts for activity logs on circuits

In the **Alert Criteria**, you can select **Activity Log** for the Signal Type and select the Signal.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="activity logs":::

## More metrics in Log Analytics

You can also view ExpressRoute metrics by navigating to your ExpressRoute circuit resource and selecting the *Logs* tab. For any metrics you query, the output will contain the columns below.

| **Column** | **Type** | **Description** | 
|  ---  |  ---  |  ---  | 
| TimeGrain | string | PT1M (metric values are pushed every minute) | 
| Count | real | Usually equal to 2 (each MSEE pushes a single metric value every minute) | 
| Minimum | real | The minimum of the two metric values pushed by the two MSEEs | 
| Maximum | real | The maximum of the two metric values pushed by the two MSEEs | 
| Average | real | Equal to (Minimum + Maximum)/2 | 
| Total | real | Sum of the two metric values from both MSEEs (the main value to focus on for the metric queried) | 
  
## Next steps

Configure your ExpressRoute connection.
  
* [Create and modify a circuit](expressroute-howto-circuit-arm.md)
* [Create and modify peering configuration](expressroute-howto-routing-arm.md)
* [Link a VNet to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md)
