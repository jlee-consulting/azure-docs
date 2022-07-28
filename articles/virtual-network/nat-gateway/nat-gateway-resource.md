---
title: Design virtual networks with NAT gateway
titleSuffix: Azure Virtual Network NAT
description: Learn how to design virtual networks that use Network Address Translation (NAT) gateway resources.
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/25/2022
ms.author: allensu
---

# Design virtual networks with NAT gateway

NAT gateway provides outbound internet connectivity for one or more subnets of a virtual network. Once NAT gateway is associated to a subnet, NAT provides source network address translation (SNAT) for that subnet. NAT gateway specifies which static IP addresses virtual machines use when creating outbound flows. Static IP addresses come from public IP addresses, public IP prefixes, or both. If a public IP prefix is used, all IP addresses of the entire public IP prefix are consumed by a NAT gateway. A NAT gateway can use up to 16 static IP addresses from either.

:::image type="content" source="./media/nat-overview/flow-direction1.png" alt-text="Diagram depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs traffic to and from two subnets of VMs and a virtual machine scale set.":::

*Figure: Virtual Network NAT for outbound to internet*

## How to deploy NAT

Configuring and using NAT gateway is intentionally made simple:

NAT gateway:

- Create a non-zonal or zonal NAT gateway.
- Assign a public IP address or public IP prefix.
- If necessary, modify TCP idle timeout (optional). Review [timers](#timers) before you change the default.

Virtual network:

- Configure virtual network subnet to use a NAT gateway.

User-defined routes aren't necessary.

## Design guidance

Review this section to familiarize yourself with considerations for designing virtual networks with NAT.

### Connect to Azure services with Private Link

When you connect your private network to Azure services such as Storage, SQL, Cosmos DB, or any other [Azure service listed here](../../private-link/availability.md), the recommended approach is to use [Private Link](../../private-link/private-link-overview.md). 

Private Link uses the private IP addresses of your virtual machines or other compute resources from your Azure network to connect privately and securely to Azure PaaS services over the Azure backbone network instead of over the internet. Private Link should be used when possible to connect to Azure services since it frees up SNAT ports for making outbound connections to the internet. To learn more about how NAT gateway uses SNAT ports, see [Source Network Address Translation](#source-network-address-translation).

### Connect to the internet with NAT gateway

NAT gateway is recommended for outbound scenarios for all production workloads where you need to connect to a public endpoint. When NAT gateway is configured to subnets, all previous outbound configurations, such as Load balancer or instance-level public IPs (IL PIPs) are superseded and NAT gateway directs all outbound traffic to the internet. Return traffic in response to an outbound initiated flow will also go through NAT gateway. Inbound initiated traffic is not affected by the addition of NAT gateway. Inbound traffic through Load balancer or IL PIPs are translated separately from outbound traffic through NAT gateway. This separation allows inbound and outbound services to coexist seamlessly.  

The following scenarios are examples of how to ensure coexistence of Load balancer or instance level public IPs for inbound with NAT gateway for outbound.

#### NAT and VM with an instance-level public IP

:::image type="content" source="./media/nat-overview/flow-direction2.png" alt-text="Diagram that depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of VMs and a virtual machine scale set.":::

*Figure: Virtual Network NAT and VM with an instance level public IP*

| Direction | Resource |
|:---:|:---:|
| Inbound | VM with instance-level public IP |
| Outbound | NAT gateway |

VM will use NAT gateway for outbound. Inbound originated isn't affected.

#### NAT and VM with a standard public load balancer

:::image type="content" source="./media/nat-overview/flow-direction3.png" alt-text="Diagram that depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer.":::

*Figure: Virtual Network NAT and VM with a standard public load balancer*

| Direction | Resource |
|:---:|:---:|
| Inbound | Standard public load balancer |
| Outbound | NAT gateway |

Any outbound configuration from a load-balancing rule or outbound rules is superseded by NAT gateway. Inbound originated isn't affected.

#### NAT and VM with an instance-level public IP and a standard public load balancer

:::image type="content" source="./media/nat-overview/flow-direction4.png" alt-text="Diagram that depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public I P and a public load balancer.":::

*Figure: Virtual Network NAT and VM with an instance-level public IP and a standard public load balancer*

| Direction | Resource |
|:---:|:---:|
| Inbound | VM with instance-level public IP and a standard public load balancer |
| Outbound | NAT gateway |

Any outbound configuration from a load-balancing rule or outbound rules is superseded by NAT gateway. The VM will also use NAT gateway for outbound. Inbound originated isn't affected.

### Scale NAT gateway

Scaling NAT gateway is primarily a function of managing the shared, available SNAT port inventory. NAT needs sufficient SNAT port inventory for expected peak outbound flows for all subnets that are attached to a NAT gateway. You can use public IP addresses, public IP prefixes, or both to create SNAT port inventory. 

> [!NOTE]
> If you assign a public IP prefix, the entire public IP prefix is used. You can't assign a public IP prefix and then break out individual IP addresses to assign to other resources. If you want to assign individual IP addresses from a public IP prefix to multiple resources, you need to create individual public IP addresses and assign them as needed instead of using the public IP prefix itself.

SNAT maps private addresses to one or more public IP addresses, rewriting the source address and source port in the process. A single NAT gateway can scale up to 16 IP addresses. If a public IP prefix is provided, each IP address within the prefix provides SNAT port inventory. Adding more public IP addresses increases the available inventory of SNAT ports. TCP and UDP are separate SNAT port inventories and are unrelated to NAT gateway.

When you scale your workload, assume that each flow requires a new SNAT port, and then scale the total number of available IP addresses for outbound traffic. Carefully consider the scale you're designing for, and then allocate IP addresses quantities accordingly.

SNAT ports sent to different destinations will most likely be reused when possible. As SNAT port exhaustion approaches, flows may not succeed.

For a SNAT example, see [SNAT fundamentals](#source-network-address-translation).

### Monitor outbound network traffic

A network security group allows you to filter inbound and outbound traffic to and from a virtual machine. To monitor outbound traffic flowing from NAT, you can enable NSG flow logs.

To learn more about NSG flow logs, see [NSG Flow Log Overview](../../network-watcher/network-watcher-nsg-flow-logging-overview.md).

For guides on how to enable NSG flow logs, see [Enabling NSG Flow Logs](../../network-watcher/network-watcher-nsg-flow-logging-overview.md#enabling-nsg-flow-logs).

## Performance

Each NAT gateway can provide up to 50 Gbps of throughput. You can split your deployments into multiple subnets and assign each subnet or group of subnets a NAT gateway to scale out.

Each NAT gateway public IP address provides 64,512 SNAT ports to make outbound connections. NAT gateway can support up to 50,000 concurrent connections per public IP address to the same destination endpoint over the internet for TCP and UDP. NAT gateway can process 1M packets per second and scale up to 5M packets per second.

Review the following section for details and the [troubleshooting article](./troubleshoot-nat.md) for specific problem resolution guidance.

## Protocols

NAT gateway interacts with IP and IP transport headers of UDP and TCP flows. NAT gateway is agnostic to application layer payloads. Other IP protocols aren't supported.

## Source Network Address Translation

### Fundamentals

Source Network Address Translation (SNAT) rewrites the source of a flow to originate from a different IP address and/or port. Typically, SNAT is used when a private network needs to connect to a public host over the internet. SNAT allows multiple VM instances within the private VNet to use the same single Public IP address or set of IP addresses (prefix) to connect to the internet.

NAT gateway SNATs the private IP address and source port of a virtual machine (or other compute resource) to a static public IP address before going outbound to the internet from a virtual network. When making connections to the same destination endpoint, a different source port is used for the connection so that connections can be distinguished from one another. SNAT port exhaustion occurs when a source endpoint has run out of available SNAT ports to differentiate between new connections.

### Example SNAT flows for NAT gateway

The following example flows explain the basic concept of SNAT and how it works with NAT gateway. 

In the table below the VM is making connections to destination IP 65.52.0.1 from the following source tuples (IPs and ports):

| Flow | Source tuple | Destination tuple |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

When NAT gateway is configured with public IP address 65.52.1.1, the source IPs are SNAT'd into NAT gateway's public IP address as shown below:

| Flow | Source tuple | Source tuple after SNAT | Destination tuple |
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

The source IP address and port of each flow is SNAT'd to the public IP address 65.52.1.1 (source tuple after SNAT) and to a different port for each new connection going to the same destination endpoint. The act of NAT gateway replacing all of the source ports and IPs with the public IP and port before connecting to the internet is known as *IP masquerading* or *port masquerading*. Multiple private sources are masqueraded behind a public IP.

### NAT gateway dynamically allocates SNAT ports

NAT gateway dynamically allocates SNAT ports across a subnet's resources (ie virtual machines). SNAT port inventory is made available by attaching public IP addresses to NAT gateway. All available SNAT ports in inventory can be used by any virtual machine on subnets configured with NAT gateway:

:::image type="content" source="./media/nat-overview/lb-vnnat-chart.png" alt-text="Diagram that depicts the inventory of all available SNAT ports used by any VM on subnets configured with NAT.":::

*Figure: Virtual Network NAT on-demand outbound SNAT*

Pre-allocation of SNAT ports to each virtual machine isn't required, which means SNAT ports aren't left unused by VMs not actively needing them.

:::image type="content" source="./media/nat-overview/exhaustion-threshold.png" alt-text="Diagram that depicts the inventory of all available SNAT ports used by any VM on subnets configured with NAT with an exhaustion threshold.":::

*Figure: Differences in exhaustion scenarios*

After a SNAT port is released, it's available for use by any VM on subnets configured with NAT. On-demand allocation allows dynamic and divergent workloads on subnets to use SNAT ports as needed. As long as SNAT ports are available, SNAT flows will succeed. 

### Source (SNAT) port reuse

NAT gateway selects a port at random out of the available inventory of ports to make new outbound connections. If NAT gateway doesn't find any available SNAT ports, then it will reuse a SNAT port. A port can be reused so long as there is no existing connection going to the same destination IP and port.

The following illustrates this concept as an additional flow to the preceding set, with a VM flowing to a new destination IP 65.52.0.2.

| Flow | Source tuple | Destination tuple |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

A NAT gateway will  translate flow 4 to a source port that may already be in use for other destinations as well (see flow 1 from table above). See [Scale NAT gateway](#scale-nat-gateway) for more discussion on correctly sizing your IP address provisioning.

| Flow | Source tuple | Source tuple after SNAT | Destination tuple |
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Don't take a dependency on the specific way source ports are assigned in the above example. The preceding is an illustration of the fundamental concept only.

## Timers

### Port Reuse Timers

Port reuse timers determine the amount of time after a connection closes that a source port is in hold down before it can be reused to go to the same destination endpoint by NAT gateway.  

The following table provides information about when a TCP port becomes available for reuse to the same destination endpoint by NAT gateway. 

| Timer | Description | Value |
|---|---|---|
| TCP FIN | After a connection is closed by a TCP FIN packet, a 65 second timer is activated that holds down the SNAT port. The SNAT port will be available for reuse after the timer ends. | 65 seconds |
| TCP RST | After a connection is closed by a TCP RST packet (reset), a 20 second timer is activated that holds down the SNAT port. When the timer ends, the port is available for reuse. | 20 seconds |
| TCP half open | During connection establishment where one connection endpoint is waiting for acknowledgment from the other endpoint, a 25 second timer is activated. If no traffic is detected, the connection will close. Once the connection has closed, the source port is available for reuse to the same destination endpoint. | 25 seconds |

For UDP traffic, after a connection has closed, the port will be in hold down for 65 seconds before it is available for reuse.

### Idle Timeout Timers

| Timer | Description | Value |
|---|---|---|
| TCP idle timeout | TCP connections can go idle when no data is transmitted between either endpoint for a prolonged period of time. A timer can be configured from 4 minutes (default) to 120 minutes (2 hours) to time out a connection that has gone idle. Traffic on the flow will reset the idle timeout timer. | Configurable; 4 minutes (default) - 120 minutes |
| UDP idle timeout | UDP connections can go idle when no data is transmitted between either endpoint for a prolonged period of time. UDP idle timeout timers are 4 minutes and are **not configurable**. Traffic on the flow will reset the idle timeout timer. | **Not configurable**; 4 minutes |

> [!NOTE]
> These timer settings are subject to change. The values are provided to help with troubleshooting and you should not take a dependency on specific timers at this time.

### Timer considerations

Design recommendations for configuring timers:

- In an idle connection scenario, NAT gateway holds onto SNAT ports until the connection idle times out. Because long idle timeout timers can unnecessarily increase the likelihood of SNAT port exhaustion, it isn't recommended to increase the TCP idle timeout duration to longer than the default time of 4 minutes. If a flow never goes idle, then it will not be impacted by the idle timer.

- TCP keepalives can be used to provide a pattern of refreshing long idle connections and endpoint liveness detection. TCP keepalives appear as duplicate ACKs to the endpoints, are low overhead, and invisible to the application layer.

- Because UDP idle timeout timers are not configurable, UDP keepalives should be used to ensure that the idle timeout value isn't reached and that the connection is maintained. Unlike TCP connections, a UDP keepalive enabled on one side of the connection only applies to traffic flow in one direction. UDP keepalives must be enabled on both sides of the traffic flow in order to keep the traffic flow alive.

## Limitations

- Basic load balancers and basic public IP addresses aren't compatible with NAT. Use standard SKU load balancers and public IPs instead.
  
  - To upgrade a basic load balancer to standard, see [Upgrade Azure Public Load Balancer](../../load-balancer/upgrade-basic-standard.md)
  
  - To upgrade a basic public IP address to standard, see [Upgrade a public IP address](../ip-services/public-ip-upgrade-portal.md)

- NAT gateway does not support ICMP 

- IP fragmentation isn't available for NAT gateway.

## Next steps

- Review [virtual network NAT](nat-overview.md).

- Learn about [metrics and alerts for NAT gateway](nat-metrics.md).

- Learn how to [troubleshoot NAT gateway](troubleshoot-nat.md).
