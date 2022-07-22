---
title: Logs ingestion API in Azure Monitor (Preview)
description: Send data to Log Analytics workspace using REST API.
ms.topic: conceptual
ms.date: 06/27/2022

---

# Logs ingestion API in Azure Monitor (Preview)
The Logs ingestion API in Azure Monitor lets you send data to a Log Analytics workspace from any REST API client. This allows you to send data from virtually any source to [supported built-in tables](#supported-tables) or to custom tables that you create. You can even extend the schema of built-in tables with custom columns.

> [!NOTE]
> The Logs ingestion API was previously referred to as the custom logs API.


## Basic operation
Your application sends data to a [data collection endpoint](../essentials/data-collection-endpoint-overview.md) which is a unique connection point for your subscription. The payload of your API call includes the source data formatted in JSON. The call specifies a [data collection rule](../essentials/data-collection-rule-overview.md) that understands the format of the source data, potentially filters and transforms it for the target table, and then directs it to a specific table in a specific workspace. You can modify the target table and workspace by modifying the data collection rule without any change to the REST API call or source data.


:::image type="content" source="media/data-ingestion-api-overview/data-ingestion-api-overview.png" lightbox="media/data-ingestion-api-overview/data-ingestion-api-overview.png" alt-text="Overview diagram of logs ingestion API.":::

> [!NOTE]
> See [Migrate from Data Collector API and custom fields-enabled tables to DCR-based custom logs](custom-logs-migrate.md) to migrate solutions from the [Data Collector API](data-collector-api.md).

## Supported tables

### Custom tables
Logs ingestion API can send data to any custom table that you create and to certain built-in tables in your Log Analytics workspace. The target table must exist before you can send data to it. 

### Built-in tables
Logs ingestion API can send data to the following built-in tables. Other tables may be added to this list as support for them is implemented.

- [CommonSecurityLog](/azure/azure-monitor/reference/tables/commonsecuritylog)
- [SecurityEvents](/azure/azure-monitor/reference/tables/securityevent)
- [Syslog](/azure/azure-monitor/reference/tables/syslog)
- [WindowsEvents](/azure/azure-monitor/reference/tables/windowsevent)

### Table limits

* Custom tables must have the `_CL` suffix.
* Column names can consist of alphanumeric characters as well as the characters `_` and `-`. They must start with a letter.  
* Columns extended on top of built-in tables must have the suffix `_CF`. Columns in a custom table do not need this suffix. 


## Authentication
Authentication for the logs ingestion API is performed at the data collection endpoint which uses standard Azure Resource Manager authentication. A common strategy is to use an Application ID and Application Key as described in [Tutorial: Add ingestion-time transformation to Azure Monitor Logs (preview)](tutorial-logs-ingestion-portal.md).

## Source data
The source data sent by your application is formatted in JSON and must match the structure expected by the data collection rule. It doesn't necessarily need to match the structure of the target table since the DCR can include a [transformation](../essentials//data-collection-transformations.md) to convert the data to match the table's structure.

## Data collection rule
[Data collection rules](../essentials/data-collection-rule-overview.md) define data collected by Azure Monitor and specify how and where that data should be sent or stored. The REST API call must specify a DCR to use. A single DCE can support multiple DCRs, so you can specify a different DCR for different sources and target tables.

The DCR must understand the structure of the input data and the structure of the target table. If the two don't match, it can use a [transformation](../essentials/data-collection-transformations.md) to convert the source data to match the target table. You may also use the transformation to filter source data and perform any other calculations or conversions.

## Sending data
To send data to Azure Monitor with the logs ingestion API, make a POST call to the data collection endpoint over HTTP. Details of the call are as follows:

### Endpoint URI
The endpoint URI uses the following format, where the `Data Collection Endpoint` and `DCR Immutable ID` identify the DCE and DCR. `Stream Name` refers to the [stream](../essentials/data-collection-rule-structure.md#custom-logs) in the DCR that should handle the custom data.

```
{Data Collection Endpoint URI}/dataCollectionRules/{DCR Immutable ID}/streams/{Stream Name}?api-version=2021-11-01-preview
```

> [!NOTE]
> You can retrieve the immutable ID from the JSON view of the DCR. See [Collect information from DCR](tutorial-logs-ingestion-portal.md#collect-information-from-dcr).

### Headers

| Header | Required? | Value | Description |
|:---|:---|:---|:---|
| Authorization     | Yes | Bearer {Bearer token obtained through the Client Credentials Flow}  | |
| Content-Type      | Yes | `application/json` | |
| Content-Encoding  | No  | `gzip` | Use the GZip compression scheme for performance optimization. |
| x-ms-client-request-id | No | String-formatted GUID |  Request ID that can be used by Microsoft for any troubleshooting purposes.  |

### Body
The body of the call includes the custom data to be sent to Azure Monitor. The shape of the data must be a JSON object or array with a structure that matches the format expected by the stream in the DCR.

## Sample call
For sample data and API call using the logs ingestion API, see either [Send custom logs to Azure Monitor Logs using the Azure portal (preview)](tutorial-logs-ingestion-portal.md) or [Send custom logs to Azure Monitor Logs using Resource Manager templates](tutorial-logs-ingestion-api.md)

## Limits and restrictions
For limits related to Logs ingestion API, see [Azure Monitor service limits](../service-limits.md#logs-ingestion-api).

 

## Next steps

- [Walk through a tutorial sending custom logs using the Azure portal.](tutorial-logs-ingestion-portal.md)
- [Walk through a tutorial sending custom logs using Resource Manager templates and REST API.](tutorial-logs-ingestion-api.md)
