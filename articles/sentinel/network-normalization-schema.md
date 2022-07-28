---
title: The Advanced Security Information Model (ASIM) Network Session normalization schema reference (Public preview) | Microsoft Docs
description: This article displays the Microsoft Sentinel Network Session normalization schema.
author: oshezaf
ms.topic: reference
ms.date: 11/17/2021
ms.author: ofshezaf

---

# The Advanced Security Information Model (ASIM) Network Session normalization schema reference (Public preview)

The Microsoft Sentinel Network Session normalization schema represents an IP network activity, such as network connections and network sessions. Such events are reported, for example, by operating systems, routers, firewalls, and intrusion prevention systems.

The network normalization schema can represent any type of an IP network session but is designed to provide support for common source types, such as Netflow, firewalls, and intrusion prevention systems.

For more information about normalization in Microsoft Sentinel, see [Normalization and the Advanced Security Information Model (ASIM)](normalization.md).

This article describes version 0.2.x of the network normalization schema. [Version 0.1](normalization-schema-v1.md) was released before ASIM was available and doesn't align with ASIM in several places. For more information, see [Differences between network normalization schema versions](normalization-schema-v1.md#changes).

> [!IMPORTANT]
> The network normalization schema is currently in *preview*. This feature is provided without a service level agreement. We don't recommend it for production workloads.
>
> The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
>

## Parsers

For more information about ASIM parsers, see the [ASIM parsers overview](normalization-parsers-overview.md).

### Unifying parsers

To use parsers that unify all ASIM out-of-the-box parsers, and ensure that your analysis runs across all the configured sources, use the `_Im_NetworkSession` filtering parser or the `_ASim_NetworkSession` parameter-less parser.

You can also use workspace-deployed `ImNetworkSession` and `ASimNetworkSession` parsers by deploying them from the [Microsoft Sentinel GitHub repository](https://aka.ms/DeployASIM).

For more information, see [built-in ASIM parsers and workspace-deployed parsers](normalization-parsers-overview.md#built-in-asim-parsers-and-workspace-deployed-parsers).

### Out-of-the-box, source-specific parsers

For the list of the Network Session parsers Microsoft Sentinel provides out-of-the-box refer to the [ASIM parsers list](normalization-parsers-list.md#network-session-parsers) 

### Add your own normalized parsers

When [developing custom parsers](normalization-develop-parsers.md) for the Network Session information model, name your KQL functions using the following syntax:

- `vimNetworkSession<vendor><Product>` for parametrized parsers
- `ASimNetworkSession<vendor><Product>` for regular parsers

Refer to the article [Managing ASIM parsers](normalization-manage-parsers.md) to learn how to add your custom parsers to the network session unifying parsers.

### Filtering parser parameters

The `im` and `vim*` parsers support [filtering parameters](normalization-about-parsers.md#optimizing-parsing-using-parameters). While these parsers are optional, they can improve your query performance.

The following filtering parameters are available:

| Name     | Type      | Description |
|----------|-----------|-------------|
| **starttime** | datetime | Filter only network sessions that *started* at or after this time. |
| **endtime** | datetime | Filter only network sessions that *started* running at or before this time. |
| **srcipaddr_has_any_prefix** | dynamic | Filter only network sessions for which the [source IP address field](#srcipaddr) prefix is in one of the listed values. Prefixes should end with a `.`, for example: `10.0.`. The length of the list is limited to 10,000 items.|
| **dstipaddr_has_any_prefix** | dynamic | Filter only network sessions for which the [destination IP address field](#dstipaddr) prefix is in one of the listed values. Prefixes should end with a `.`, for example: `10.0.`. The length of the list is limited to 10,000 items.|
| **ipaddr_has_any_prefix** | dynamic | Filter only network sessions for which the [destination IP address field](#dstipaddr) or [source IP address field](#srcipaddr) prefix is in one of the listed values. Prefixes should end with a `.`, for example: `10.0.`. The length of the list is limited to 10,000 items.<br><br>The field [ASimMatchingIpAddr](normalization-common-fields.md#asimmatchingipaddr) is set with the one of the values `SrcIpAddr`, `DstIpAddr`, or `Both` to reflect the matching fields or fields. |
| **dstportnum** | Int | Filter only network sessions with the specified destination port number. |
| **hostname_has_any** | dynamic | Filter only network sessions for which the [destination hostname field](#dsthostname) has any of the values listed. The length of the list is limited to 10,000 items.<br><br> The field [ASimMatchingHostname](normalization-common-fields.md#asimmatchinghostname) is set with the one of the values `SrcHostname`, `DstHostname`, or `Both` to reflect the matching fields or fields. |
| **dvcaction** | dynamic | Filter only network sessions for which the [Device Action field](#dvcaction) is any of the values listed. | 
| **eventresult** | String | Filter only network sessions with a specific **EventResult** value. |


For example, to filter only network sessions for a specified list of domain names, use:

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co"]);
_Im_NetworkSession (hostname_has_any = torProxies)
```

> [!TIP]
> To pass a literal list to parameters that expect a dynamic value, explicitly use a [dynamic literal](/azure/data-explorer/kusto/query/scalar-data-types/dynamic#dynamic-literals.md). For example: `dynamic(['192.168.','10.'])`.
>

## Schema overview

The Network Session information model is aligned with the [OSSEM Network entity schema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md).

Network session events use the descriptors `Src` and `Dst` to denote the roles of the devices and related users and applications involved in the session. So, for example, the source device hostname and IP address are named `SrcHostname` and `SrcIpAddr`. Note that other ASIM schemas typically use `Target` instead of `Dst`.

For events reported by an endpoint and for which the event type is `EndpointNetworkSession`, the descriptors `Local` and `Remote` denote the endpoint itself and the device at the other end of the network session respectively.

The descriptor `Dvc` is used for the reporting device, which is the local system for sessions reported by an endpoint, and the intermediary device or network tap for other network session events. 

## Schema details

### Common ASIM fields

> [!IMPORTANT]
> Fields common to all schemas are described in detail in the [ASIM Common Fields](normalization-common-fields.md) article.
>
#### Common fields with specific guidelines

The following list mentions fields that have specific guidelines for Network Session events:


| Field               | Class       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventCount** | Mandatory | Integer | Netflow sources support aggregation, and the **EventCount** field should be set to the value of the Netflow **FLOWS** field. For other sources, the value is typically set to `1`. |
| <a name="eventtype"></a> **EventType** | Mandatory | Enumerated | Describes the operation reported by the record.<br><br> For Network Session records, the allowed values are:<br> - `EndpointNetworkSession`: for sessions reported by endpoint systems, including clients and servers. For such systems, the schema supports the `remote` and `local` alias fields. <br> - `NetworkSession`: for sessions reported by intermediary systems and network taps. <br> - `Flow`: for `NetFlow` type aggregated flows which group multiple similar sessions together. For such records, [EventSubType](#eventsubtype) should be left empty. |
| <a name="eventsubtype"></a>**EventSubType** | Optional | String | Additional description of the event type, if applicable. <br> For Network Session records, supported values include:<br>- `Start`<br>- `End` |
| **EventResult** | Mandatory | Enumerated | If the source device does not provide an event result, **EventResult** should be based on the value of [DvcAction](#dvcaction).  If [DvcAction](#dvcaction) is `Deny`, `Drop`, `Drop ICMP`, `Reset`, `Reset Source`, or `Reset Destination`<br>, **EventResult** should be `Failure`. Otherwise, **EventResult** should be `Success`. |
| **EventSchema** | Mandatory | String | The name of the schema documented here is `NetworkSession`. |
| **EventSchemaVersion**  | Mandatory   | String     | The version of the schema. The version of the schema documented here is `0.2.3`.        |
| <a name="dvcaction"></a>**DvcAction** | Recommended | Enumerated | The action taken on the network session. Supported values are:<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. The original value should be stored in the [DvcOriginalAction](normalization-common-fields.md#dvcoriginalaction) field.<br><br>Example: `drop` |
| **EventSeverity** | Optional | Enumerated | If the source device does not provide an event severity, **EventSeverity** should be based on the value of [DvcAction](#dvcaction).  If [DvcAction](#dvcaction) is `Deny`, `Drop`, `Drop ICMP`, `Reset`, `Reset Source`, or `Reset Destination`<br>, **EventSeverity** should be `Low`. Otherwise, **EventSeverity** should be `Informational`. |
| **DvcInterface** | | | The DvcInterface field should alias either the [DvcInboundInterface](#dvcinboundinterface) or the [DvcOutboundInterface](#dvcoutboundinterface) fields. |
| **Dvc** fields|        |      | For Network Session events, device fields refer to the system reporting the Network Session event.  |


#### All common fields

Fields that appear in the table below are common to all ASIM schemas. Any guideline specified above overrides the general guidelines for the field. For example, a field might be optional in general, but mandatory for a specific schema. For further details on each field, refer to the [ASIM Common Fields](normalization-common-fields.md) article.

| **Class** | **Fields** |
| --------- | ---------- |
| Mandatory | - [EventCount](normalization-common-fields.md#eventcount)<br> - [EventStartTime](normalization-common-fields.md#eventstarttime)<br> - [EventEndTime](normalization-common-fields.md#eventendtime)<br> - [EventType](normalization-common-fields.md#eventtype)<br>- [EventResult](normalization-common-fields.md#eventresult)<br> - [EventProduct](normalization-common-fields.md#eventproduct)<br> - [EventVendor](normalization-common-fields.md#eventvendor)<br> - [EventSchema](normalization-common-fields.md#eventschema)<br> - [EventSchemaVersion](normalization-common-fields.md#eventschemaversion)<br> - [Dvc](normalization-common-fields.md#dvc)<br>|
| Recommended | - [EventResultDetails](normalization-common-fields.md#eventresultdetails)<br>- [EventSeverity](normalization-common-fields.md#eventseverity)<br> - [DvcIpAddr](normalization-common-fields.md#dvcipaddr)<br> - [DvcHostname](normalization-common-fields.md#dvchostname)<br> - [DvcDomain](normalization-common-fields.md#dvcdomain)<br>- [DvcDomainType](normalization-common-fields.md#dvcdomaintype)<br>- [DvcFQDN](normalization-common-fields.md#dvcfqdn)<br>- [DvcId](normalization-common-fields.md#dvcid)<br>- [DvcIdType](normalization-common-fields.md#dvcidtype)<br>- [DvcAction](normalization-common-fields.md#dvcaction)|
| Optional | - [EventMessage](normalization-common-fields.md#eventmessage)<br> - [EventSubType](normalization-common-fields.md#eventsubtype)<br>- [EventOriginalUid](normalization-common-fields.md#eventoriginaluid)<br>- [EventOriginalType](normalization-common-fields.md#eventoriginaltype)<br>- [EventOriginalSubType](normalization-common-fields.md#eventoriginalsubtype)<br>- [EventOriginalResultDetails](normalization-common-fields.md#eventoriginalresultdetails)<br> - [EventOriginalSeverity](normalization-common-fields.md#eventoriginalseverity) <br> - [EventProductVersion](normalization-common-fields.md#eventproductversion)<br> - [EventReportUrl](normalization-common-fields.md#eventreporturl)<br>- [DvcMacAddr](normalization-common-fields.md#dvcmacaddr)<br>- [DvcOs](normalization-common-fields.md#dvcos)<br>- [DvcOsVersion](normalization-common-fields.md#dvchostname)<br>- [DvcOriginalAction](normalization-common-fields.md#dvcoriginalaction)<br>- [DvcInterface](normalization-common-fields.md#dvcinterface)<br>- [AdditionalFields](normalization-common-fields.md#additionalfields)<br>- [DvcDescription](normalization-common-fields.md#dvcdescription)|


### Network session fields

| Field               | Class       | Type       |  Description        |
|---------------------|-------------|------------|--------------------|
| **NetworkApplicationProtocol** | Optional | String | The application layer protocol used by the connection or session. If the [DstPortNumber](#dstportnumber) value is provided, we recommend that you include **NetworkApplicationProtocol** too. If the value isn't available from the source, derive the value from the [DstPortNumber](#dstportnumber) value.<br><br>Example: `FTP` |
| <a name="networkprotocol"></a> **NetworkProtocol** | Optional | Enumerated | The IP protocol used by the connection or session as listed in [IANA protocol assignment](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml), which is typically `TCP`, `UDP`, or `ICMP`.<br><br>Example: `TCP` |
| **NetworkProtocolVersion** | Optional | Enumerated | The version of [NetworkProtocol](#networkprotocol). When using it to distinguish between IP version, use the values `IPv4` and `IPv6`. |
| <a name="networkdirection"></a>**NetworkDirection** | Optional | Enumerated | The direction of the connection or session:<br><br> - For the [EventType](#eventtype) `NetworkSession`, **NetworkDirection** represents the direction relative to the organization or cloud environment boundary. Supported values are `Inbound`, `Outbound`, `Local` (to the organization), `External` (to the organization) or `NA` (Not Applicable).<br><br> - For the [EventType](#eventtype) `EndpointNetworkSession`, **NetworkDirection** represents the direction relative to the endpoint. Supported values are `Inbound`, `Outbound`, `Local` (to the system), `Listen` or `NA` (Not Applicable). The `Listen` value indicates that a device has started accepting network connections but isn't actually, necessarily, connected. |
| <a name="networkduration"></a>**NetworkDuration** | Optional | Integer | The amount of time, in milliseconds, for the completion of the network session or connection.<br><br>Example: `1500` |
| **Duration** | Alias | | Alias to [NetworkDuration](#networkduration). |
| **NetworkIcmpCode** | Optional | Integer | For an ICMP message, the ICMP message type numeric value as described in [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) for IPv4 network connections, or in [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) for IPv6 network connections. If a [NetworkIcmpType](#networkicmptype) value is provided, this field is mandatory. If the value isn't available from the source, derive the value from the [NetworkIcmpType](#networkicmptype) field instead.<br><br>Example: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | Optional | String | For an ICMP message, the ICMP message type text representation, as described in [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) for IPv4 network connections, or in [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) for IPv6 network connections.<br><br>Example: `Destination Unreachable` |
| **NetworkConnectionHistory** | Optional | String | TCP flags and other potential IP header information. |
| **DstBytes** | Recommended | Long | The number of bytes sent from the destination to the source for the connection or session. If the event is aggregated, **DstBytes** should be the sum over all aggregated sessions.<br><br>Example: `32455` |
| **SrcBytes** | Recommended | Long | The number of bytes sent from the source to the destination for the connection or session. If the event is aggregated, **SrcBytes** should be the sum over all aggregated sessions.<br><br>Example: `46536` |
| **NetworkBytes** | Optional | Long | Number of bytes sent in both directions. If both **BytesReceived** and **BytesSent** exist, **BytesTotal** should equal their sum. If the event is aggregated, **NetworkBytes** should be the sum over all aggregated sessions.<br><br>Example: `78991` |
| **DstPackets** | Optional | Long | The number of packets sent from the destination to the source for the connection or session. The meaning of a packet is defined by the reporting device. If the event is aggregated, **DstPackets** should be the sum over all aggregated sessions.<br><br>Example: `446` |
| **SrcPackets** | Optional | Long | The number of packets sent from the source to the destination for the connection or session. The meaning of a packet is defined by the reporting device. If the event is aggregated, **SrcPackets** should be the sum over all aggregated sessions.<br><br>Example: `6478` |
| **NetworkPackets** | Optional | Long | The number of packets sent in both directions. If both **PacketsReceived** and **PacketsSent** exist, **BytesTotal** should equal their sum. The meaning of a packet is defined by the reporting device. If the event is aggregated, **NetworkPackets** should be the sum over all aggregated sessions.<br><br>Example: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | Optional | string | The session identifier as reported by the reporting device. <br><br>Example: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | String | Alias to [NetworkSessionId](#networksessionid). |


### Destination system fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="dst"></a>**Dst** | Recommended       | Alias     |    A unique identifier of the server receiving the DNS request. <br><br>This field might alias the [DstDvcId](#dstdvcid), [DstHostname](#dsthostname), or [DstIpAddr](#dstipaddr) fields. <br><br>Example: `192.168.12.1`       |
|<a name="dstipaddr"></a> **DstIpAddr** | Recommended | IP address | The IP address of the connection or session destination. If the session uses network address translation, this is the publicly visible address, and not the original address of the source which is stored in [DstNatIpAddr](#dstnatipaddr)<br><br>Example: `2001:db8::ff00:42:8329`<br><br>**Note**: This value is mandatory if [DstHostname](#dsthostname) is specified. |
| <a name="dstportnumber"></a>**DstPortNumber** | Optional | Integer | The destination IP port.<br><br>Example: `443` |
| <a name="dsthostname"></a>**DstHostname** | Recommended | Hostname | The destination device hostname, excluding domain information. If no device name is available, store the relevant IP address in this field.<br><br>Example: `DESKTOP-1282V4D` |
| <a name="dstdomain"></a>**DstDomain** | Recommended | String | The domain of the destination device.<br><br>Example: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Recommended | Enumerated | The type of [DstDomain](#dstdomain). For a list of allowed values and further information refer to [DomainType](normalization-about-schemas.md#domaintype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Required if [DstDomain](#dstdomain) is used. |
| **DstFQDN** | Optional | String | The destination device hostname, including domain information when available. <br><br>Example: `Contoso\DESKTOP-1282V4D` <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [DstDomainType](#dstdomaintype) reflects the format used.   |
| <a name="dstdvcid"></a>**DstDvcId** | Optional | String | The ID of the destination device. If multiple IDs are available, use the most important one, and store the others in the fields `DstDvc<DvcIdType>`. <br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Optional | Enumerated | The type of [DstDvcId](#dstdvcid). For a list of allowed values and further information refer to [DvcIdType](normalization-about-schemas.md#dvcidtype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>Required if **DstDeviceId** is used.|
| **DstDeviceType** | Optional | Enumerated | The type of the destination device.  For a list of allowed values and further information refer to [DeviceType](normalization-about-schemas.md#devicetype) in the [Schema Overview article](normalization-about-schemas.md). |
| **DstZone** | Optional | String | The network zone of the destination, as defined by the reporting device.<br><br>Example: `Dmz` |
| **DstInterfaceName** | Optional | String | The network interface used for the connection or session by the destination device.<br><br>Example: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | Optional | String | The GUID of the network interface used on the destination device.<br><br>Example:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | Optional | String | The MAC address of the network interface used for the connection or session by the destination device.<br><br>Example: `06:10:9f:eb:8f:14` |
| <a name="dstvlanid"></a>**DstVlanId** | Optional | String | The VLAN ID related to the destination device.<br><br>Example: `130` |
| **OuterVlanId** | Optional | Alias | Alias to [DstVlanId](#dstvlanid). <br><br>In many cases, the VLAN can't be determined as a source or a destination but is characterized as inner or outer. This alias to signifies that [DstVlanId](#dstvlanid) should be used when the VLAN is characterized as outer. |
| <a name="dstsubscription"></a>**DstSubscriptionId** | Optional | String | The cloud platform subscription ID the destination device belongs to. **DstSubscriptionId** map to a subscription ID on Azure and to an account ID on AWS. | 
| **DstGeoCountry** | Optional | Country | The country associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `USA` |
| **DstGeoRegion** | Optional | Region | The region, or state, within a country associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `Vermont` |
| **DstGeoCity** | Optional | City | The city associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `Burlington` |
| **DstGeoLatitude** | Optional | Latitude | The latitude of the geographical coordinate associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `44.475833` |
| **DstGeoLongitude** | Optional | Longitude | The longitude of the geographical coordinate associated with the destination IP address. For more information, see [Logical types](normalization-about-schemas.md#logical-types).<br><br>Example: `73.211944` |


### Destination user fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="dstuserid"></a>**DstUserId** | Optional | String | A machine-readable, alphanumeric, unique representation of the destination user. For the supported format for different ID types, refer to [the User entity](normalization-about-schemas.md#the-user-entity). <br><br>Example: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | Optional | UserIdType | The type of the ID stored in the [DstUserId](#dstuserid) field. For a list of allowed values and further information refer to [UserIdType](normalization-about-schemas.md#useridtype) in the [Schema Overview article](normalization-about-schemas.md). |
| <a name="dstusername"></a>**DstUsername** | Optional | String | The destination username, including domain information when available. For the supported format for different ID types, refer to [the User entity](normalization-about-schemas.md#the-user-entity). Use the simple form only if domain information isn't available.<br><br>Store the Username type in the [DstUsernameType](#dstusernametype) field. If other username formats are available, store them in the fields `DstUsername<UsernameType>`.<br><br>Example: `AlbertE` |
| <a name="user"></a>**User** | Alias | | Alias to [DstUsername](#dstusername). |
| <a name="dstusernametype"></a>**DstUsernameType** | Optional | UsernameType | Specifies the type of the username stored in the [DstUsername](#dstusername) field. For a list of allowed values and further information refer to [UsernameType](normalization-about-schemas.md#usernametype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Example: `Windows` |
| **DstUserType** | Optional | UserType | The type of destination user. For a list of allowed values and further information refer to [UserType](normalization-about-schemas.md#usertype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. Store the original value in the [DstOriginalUserType](#dstoriginalusertype) field. |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | Optional | String | The original destination user type, if provided by the source. |


### Destination application fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="dstappname"></a>**DstAppName** | Optional | String | The name of the destination application.<br><br>Example: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | Optional | String | The ID of the destination application, as reported by the reporting device.<br><br>Example: `124` |
| **DstAppType** | Optional | AppType | The type of the destination application. For a list of allowed values and further information refer to [AppType](normalization-about-schemas.md#apptype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>This field is mandatory if [DstAppName](#dstappname) or [DstAppId](#dstappid) are used. |


### Source system fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="src"></a>**Src** | Recommended       | Alias     |    A unique identifier of the source device. <br><br>This field might alias the [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname), or [SrcIpAddr](#srcipaddr) fields. <br><br>Example: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Recommended | IP address | The IP address from which the connection or session originated. This value is mandatory if **SrcHostname** is specified. If the session uses network address translation, this is the publicly visible address, and not the original address of the source which is stored in [SrcNatIpAddr](#srcnatipaddr)<br><br>Example: `77.138.103.108` |
| **SrcPortNumber** | Optional | Integer | The IP port from which the connection originated. Might not be relevant for a session comprising multiple connections.<br><br>Example: `2335` |
| <a name="srchostname"></a> **SrcHostname** | Recommended | Hostname | The source device hostname, excluding domain information. If no device name is available, store the relevant IP address in this field.<br><br>Example: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Recommended | String | The domain of the source device.<br><br>Example: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Recommended | DomainType | The type of [SrcDomain](#srcdomain). For a list of allowed values and further information refer to [DomainType](normalization-about-schemas.md#domaintype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Required if [SrcDomain](#srcdomain) is used. |
| **SrcFQDN** | Optional | String | The source device hostname, including domain information when available. <br><br>**Note**: This field supports both traditional FQDN format and Windows domain\hostname format. The [SrcDomainType](#srcdomaintype) field reflects the format used. <br><br>Example: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String |  The ID of the source device. If multiple IDs are available, use the most important one, and store the others in the fields `SrcDvc<DvcIdType>`.<br><br>Example: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | DvcIdType | The type of [SrcDvcId](#srcdvcid). For a list of allowed values and further information refer to [DvcIdType](normalization-about-schemas.md#dvcidtype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>**Note**: This field is required if [SrcDvcId](#srcdvcid) is used. |
| **SrcDeviceType** | Optional | DeviceType | The type of the source device. For a list of allowed values and further information refer to [DeviceType](normalization-about-schemas.md#devicetype) in the [Schema Overview article](normalization-about-schemas.md). |
| **SrcZone** | Optional | String | The network zone of the source, as defined by the reporting device.<br><br>Example: `Internet` |
| **SrcInterfaceName** | Optional | String | The network interface used for the connection or session by the source device. <br><br>Example: `eth01` |
| **SrcInterfaceGuid** | Optional | String | The GUID of the network interface used on the source device.<br><br>Example:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | Optional | String | The MAC address of the network interface from which the connection or session originated.<br><br>Example: `06:10:9f:eb:8f:14` |
| <a name="srcvlanid"></a>**SrcVlanId** | Optional | String | The VLAN ID related to the source device.<br><br>Example: `130` |
| **InnerVlanId** | Optional | Alias | Alias to [SrcVlanId](#srcvlanid). <br><br>In many cases, the VLAN can't be determined as a source or a destination but is characterized as inner or outer. This alias to signifies that [SrcVlanId](#srcvlanid) should be used when the VLAN is characterized as inner.    |
| <a name="srcsubscription"></a>**SrcSubscriptionId** | Optional | String | The cloud platform subscription ID the source device belongs to. **SrcSubscriptionId** map to a subscription ID on Azure and to an account ID on AWS. | 
| **SrcGeoCountry** | Optional | Country | The country associated with the source IP address.<br><br>Example: `USA` |
| **SrcGeoRegion** | Optional | Region | The region within a country associated with the source IP address.<br><br>Example: `Vermont` |
| **SrcGeoCity** | Optional | City | The city associated with the source IP address.<br><br>Example: `Burlington` |
| **SrcGeoLatitude** | Optional | Latitude | The latitude of the geographical coordinate associated with the source IP address.<br><br>Example: `44.475833` |
| **SrcGeoLongitude** | Optional | Longitude | The longitude of the geographical coordinate associated with the source IP address.<br><br>Example: `73.211944` |


### Source user fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="srcuserid"></a>**SrcUserId** | Optional | String | A machine-readable, alphanumeric, unique representation of the source user. For the supported format for different ID types, refer to [the User entity](normalization-about-schemas.md#the-user-entity). <br><br>Example: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Optional | UserIdType | The type of the ID stored in the [SrcUserId](#srcuserid) field. For a list of allowed values and further information refer to [UserIdType](normalization-about-schemas.md#useridtype) in the [Schema Overview article](normalization-about-schemas.md). |
| <a name="srcusername"></a>**SrcUsername** | Optional | String | The source username, including domain information when available. For the supported format for different ID types, refer to [the User entity](normalization-about-schemas.md#the-user-entity). Use the simple form only if domain information isn't available.<br><br>Store the Username type in the [SrcUsernameType](#srcusernametype) field. If other username formats are available, store them in the fields `SrcUsername<UsernameType>`.<br><br>Example: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | Optional | UsernameType | Specifies the type of the username stored in the [SrcUsername](#srcusername) field. For a list of allowed values and further information refer to [UsernameType](normalization-about-schemas.md#usernametype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>Example: `Windows` |
| **SrcUserType** | Optional | UserType | The type of source user. For a list of allowed values and further information refer to [UserType](normalization-about-schemas.md#usertype) in the [Schema Overview article](normalization-about-schemas.md). <br><br>**Note**: The value might be provided in the source record by using different terms, which should be normalized to these values. Store the original value in the [SrcOriginalUserType](#srcoriginalusertype) field. |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | Optional | String | The original destination user type, if provided by the reporting device. |


### Source application fields

| Field | Class | Type | Description |
|-------|-------|------|-------------|
| <a name="srcappname"></a>**SrcAppName** | Optional | String | The name of the source application. <br><br>Example: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | Optional | String | The ID of the source application, as reported by the reporting device.<br><br>Example: `124` |
| **SrcAppType** | Optional | AppType | The type of the source application. For a list of allowed values and further information refer to [AppType](normalization-about-schemas.md#apptype) in the [Schema Overview article](normalization-about-schemas.md).<br><br>This field is mandatory if [SrcAppName](#srcappname) or [SrcAppId](#srcappid) are used. |



### Local and remote aliases

All the source and destination fields listed above, can be optionally aliased by fields with the same name and the descriptors `Local` and `Remote`. This is typically helpful for events reported by an endpoint and for which the event type is `EndpointNetworkSession`. 

For such events the descriptors `Local` and `Remote` denote the endpoint itself and the device at the other end of the network session respectively. For inbound connections, the local system is the destination, `Local` fields are aliases to the `Dst` fields, and 'Remote' fields are aliases to `Src` fields. Conversely, for outbound connections, the local system is the source, `Local` fields are aliases to the `Src` fields, and `Remote` fields are aliases to `Dst` fields.

For example, for an inbound event, the field `LocalIpAddr` is an alias to `DstIpAddr` and the field `RemoteIpAddr` is an alias to `SrcIpAddr`.

### Hostname and IP address aliases

| Field | Class | Type | Description |
| --- | --- | --- | --- |
| <a name="hostname"></a>**Hostname** | Alias | | - If the event type is `NetworkSession`, Hostname is an alias to [DstHostname](#dsthostname).<br> - If the event type is `EndpointNetworkSession`, Hostname is an alias to `RemoteHostname`, which can alias either [DstHostname](#dsthostname) or [SrcHostName](#srchostname), depending on [NetworkDirection](#networkdirection) |
| <a name="ipaddr"></a>**IpAddr** | Alias | | - If the event type is `NetworkSession`, Hostname is an alias to [SrcIpAddr](#srcipaddr).<br> - If the event type is `EndpointNetworkSession`, Hostname is an alias to `LocalIpAddr`, which can alias either [SrcIpAddr](#srcipaddr) or [DstIpAddr](#dstipaddr), depending on [NetworkDirection](#networkdirection). |


### <a name="Intermediary"></a>Intermediary device and Network Address Translation (NAT) fields

The following fields are useful if the record includes information about an intermediary device, such as a firewall or a proxy, which relays the network session.

Intermediary systems often use address translation and therefore the original address and the address observed externally are not the same. In such cases, the primary address fields such as [SrcIPAddr](#srcipaddr) and [DstIpAddr](#dstipaddr) represent the addresses observed externally, while the NAT address fields, [SrcNatIpAddr](#srcnatipaddr) and [DstNatIpAddr](#dstnatipaddr) represent the internal address of the original device before translation.

| Field | Class | Type | Description |
| --- | --- | --- | --- |
| <a name="dstnatipaddr"></a>**DstNatIpAddr** | Optional | IP address | The **DstNatIpAddr** represents either of:<br> - The original address of the destination device if network address translation was used.<br> - The IP address used by the intermediary device for communication with the source.<br><br>Example: `2::1` | 
| **DstNatPortNumber** | Optional | Integer | If reported by an intermediary NAT device, the port used by the NAT device for communication with the source.<br><br>Example: `443` |
| <a name="srcnatipaddr"></a>**SrcNatIpAddr** | Optional | IP address | The **SrcNatIpAddr** represents either of:<br> - The original address of the source device if network address translation was used.<br> - The IP address used by the intermediary device for communication with the destination.<br><br>Example: `4.3.2.1` |
| **SrcNatPortNumber** | Optional | Integer | If reported by an intermediary NAT device, the port used by the NAT device for communication with the destination.<br><br>Example: `345` |
| <a name="dvcinboundinterface"></a>**DvcInboundInterface** | Optional | String | If reported by an intermediary device, the network interface used by the NAT device for the connection to the source device.<br><br>Example: `eth0` |
| <a name="dvcoutboundinterface"></a>**DvcOutboundInterface** | Optional | String | If reported by an intermediary device, the network interface used by the NAT device for the connection to the destination device.<br><br>Example: `Ethernet adapter Ethernet 4e` |


### <a name="inspection-fields"></a>Inspection fields

The following fields are used to represent that inspection which a security device such as a firewall, an IPS, or a web security gateway performed:

| Field | Class | Type | Description |
| --- | --- | --- | --- |
| <a name="networkrulename"></a>**NetworkRuleName** | Optional | String | The name or ID of the rule by which [DvcAction](#dvcaction) was decided upon.<br><br> Example: `AnyAnyDrop` |
| <a name="networkrulenumber"></a>**NetworkRuleNumber** | Optional | Integer | The number of the rule by which [DvcAction](#dvcaction) was decided upon.<br><br>Example: `23` |
| **Rule** | Mandatory | String | Either the value of [NetworkRuleName](#networkrulename) or the value of [NetworkRuleNumber](#networkrulenumber). Note that if the value of [NetworkRuleNumber](#networkrulenumber) is used, the type should be converted to string. |
| **ThreatId** | Optional | String | The ID of the threat or malware identified in the network session.<br><br>Example: `Tr.124` |
| **ThreatName** | Optional | String | The name of the threat or malware identified in the network session.<br><br>Example: `EICAR Test File` |
| **ThreatCategory** | Optional | String | The category of the threat or malware identified in the network session.<br><br>Example: `Trojan` |
| **ThreatRiskLevel** | Optional | Integer | The risk level associated with the session. The level should be a number between **0** and **100**.<br><br>**Note**: The value might be provided in the source record by using a different scale, which should be normalized to this scale. The original value should be stored in [ThreatRiskLevelOriginal](#threatriskleveloriginal). |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | Optional | String | The risk level as reported by the reporting device. |


### Other fields

If the event is reported by one of the endpoints of the network session, it might include information about the process that initiated or terminated the session. In such cases, the [ASIM Process Event schema](process-events-normalization-schema.md) is used to normalize this information.

### Schema updates

These are the changes in version 0.2.1 of the schema:

- Added `Src` and `Dst` as aliases to a leading identifier for the source and destination systems.
- Added the fields `**`NetworkConnectionHistory`**`, `**`SrcVlanId`**`, `**`DstVlanId`**`, `InnerVlanId`, and `OuterVlanId`.

 
These are the changes in version 0.2.2 of the schema:

- Added `Remote` and `Local` aliases.
- Added the event type `EndpointNetworkSession`.
- Defined `Hostname` and `IpAddr` as aliases for `RemoteHostname` and `LocalIpAddr` respectively when the event type is `EndpointNetworkSession`.
- Defined `DvcInterface` as an alias to `DvcInboundInterface` or `DvcOutboundInterface`.
- Changed the type of the following fields from Integer to Long: `SrcBytes`, `DstBytes`, `NetworkBytes`, `SrcPackets`, `DstPackets`, and `NetworkPackets`.
- Added the fields `NetworkProtocolVersion`, `SrcSubscriptionId`, and `DstSubscriptionId`.
- Deprecated `DstUserDomain` and `SrcUserDomain`.

Theses are the changes in version 0.2.3 of the schema:
- Added the `ipaddr_has_any_prefix` filtering parameter.
- The `hostname_has_any` filtering parameter now matches either source or destination hostnames.
- Added the fields `ASimMatchingHostname` and `ASimMatchingIpAddr`.

## Next steps

For more information, see:

- Watch the [ASIM Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) or review the [slides](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG)
- [Advanced Security Information Model (ASIM) overview](normalization.md)
- [Advanced Security Information Model (ASIM) schemas](normalization-about-schemas.md)
- [Advanced Security Information Model (ASIM) parsers](normalization-parsers-overview.md)
- [Advanced Security Information Model (ASIM) content](normalization-content.md)