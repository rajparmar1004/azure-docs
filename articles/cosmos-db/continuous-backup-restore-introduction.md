---
title: Continuous backup with point in time restore feature in Azure Cosmos DB
description: Azure Cosmos DB's point-in-time restore feature helps to recover data from an accidental write, delete operation, or to restore data into any region. Learn about pricing and current limitations.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2022
ms.author: govindk
ms.reviewer: mjbrown
ms.custom: references_regions, cosmos-db-video
---

# Continuous backup with point-in-time restore in Azure Cosmos DB

[!INCLUDE[appliesto-all-apis-except-cassandra](includes/appliesto-all-apis-except-cassandra.md)]

Azure Cosmos DB's point-in-time restore feature helps in multiple scenarios including:

* Recovering from an accidental write or delete operation within a container.
* Restoring a deleted account, database, or a container.
* Restoring into any region (where backups existed) at the restore point in time.

>
> [!VIDEO https://aka.ms/docs.continuous-backup-restore]

Azure Cosmos DB performs data backup in the background without consuming any extra provisioned throughput (RUs) or affecting the performance and availability of your database. Continuous backups are taken in every region where the account exists. For example, an account can have a write region in West US and read regions in East US and East US 2. These replica regions can then be backed up to a remote Azure Storage account in each respective region. By default, each region stores the backup in Locally Redundant storage accounts. If the region has [Availability zones](/azure/architecture/reliability/architect) enabled  then the backup is stored in Zone-Redundant storage accounts.

:::image type="complex" source="media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" lightbox="media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Diagram illustrating how a container is backed up across multiple regions." border="false":::
Diagram illustrating how a container with a write region in West US and read regions in East and East US 2 is backed up. The container is backed up to a remote Azure Blob Storage account in each respective write and read region.
:::image-end:::

The time window available for restore (also known as retention period) is the lower value of the following two options: 30-day &amp; 7-day.

The selected option depends on the chosen tier of continuous backup. The point in time for restore can be any timestamp within the retention period no further back than the point when the resource was created. In strong consistency mode, backups taken in the write region are more up to date when compared to the read regions. Read regions can lag behind due to network or other transient issues. While doing restore, you can [get the latest restorable timestamp](get-latest-restore-timestamp.md) for a given resource in a specific region. Getting the latest timestamp ensures that the resource has taken backups up to the given timestamp, and can restore in that region.

Currently, you can restore an Azure Cosmos DB account (SQL API or API for MongoDB) contents at a specific point in time to another account. You can perform this restore operation via the [Azure portal](restore-account-continuous-backup.md#restore-account-portal), the [Azure CLI](restore-account-continuous-backup.md#restore-account-cli) (Azure CLI), [Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell), or [Azure Resource Manager templates](restore-account-continuous-backup.md#restore-arm-template). Table API or Gremlin APIs are in preview and supported through [Azure CLI](restore-account-continuous-backup.md#restore-account-cli) (Azure CLI) and [Azure PowerShell](restore-account-continuous-backup.md#restore-account-powershell).

## Backup storage redundancy

By default, Azure Cosmos DB stores continuous mode backup data in locally redundant storage blobs. For the regions that have zone redundancy configured, the backup is stored in zone-redundant storage blobs. In continuous backup mode, you can't update the backup storage redundancy.

## What is restored?

In a steady state, all mutations performed on the source account (which includes databases, containers, and items) are backed up asynchronously within 100 seconds. If the Azure Storage backup media is down or unavailable, the mutations are persisted locally until the media is available. Then the mutations are flushed out to prevent any loss in fidelity of operations that can be restored.

You can choose to restore any combination of provisioned throughput containers, shared throughput database, or the entire account. The restore action restores all data and its index properties into a new account. The restore process ensures that all the data restored in an account, database, or a container is guaranteed to be consistent up to the restore time specified. The duration of restore will depend on the amount of data that needs to be restored.

> [!NOTE]
> With the continuous backup mode, the backups are taken in every region where your Azure Cosmos DB account is available. Backups taken for each region account are Locally redundant by default and Zone redundant if your account has [availability zone](/azure/architecture/reliability/architect) feature enabled for that region. The restore action always restores data into a new account.

## What isn't restored?

The following configurations aren't restored after the point-in-time recovery:

* Firewall, VNET, private endpoint settings.
* Consistency settings. By default, the account is restored with session consistency.  
* Regions.
* Stored procedures, triggers, UDFs.

You can add these configurations to the restored account after the restore is completed.

## Restorable timestamp for live accounts

To restore Azure Cosmos DB live accounts that aren't deleted, it's a best practice to always identify the [latest restorable timestamp](get-latest-restore-timestamp.md) for the container. You can then use this timestamp to restore the account to its latest version.

## Restore scenarios

The following are some of the key scenarios that are addressed by the point-in-time-restore feature. Scenarios [1] through [3] demonstrate how to trigger a restore if the restore timestamp is known beforehand.
However, there could be scenarios where you don't know the exact time of accidental deletion or corruption. Scenarios [4] and [5] demonstrate how to *discover* the restore timestamp using the new event feed APIs on the restorable database or containers.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="Life-cycle events with timestamps for a restorable account." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

1. **Restore deleted account** - All the deleted accounts that you can restore are visible from the **Restore** pane. For example, if *Account A* is deleted at timestamp T3. In this case the timestamp just before T3, location, target account name, resource group, and target account name is sufficient to restore from [Azure portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), or [CLI](restore-account-continuous-backup.md#trigger-restore-cli).  

   :::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="Life-cycle events with timestamps for a restorable database and container." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

2. **Restore data of an account in a particular region** - For example, if *Account A* exists in two regions *East US* and *West US* at timestamp T3. If you need a copy of account A in *West US*, you can do a point in time restore from [Azure portal](restore-account-continuous-backup.md#restore-deleted-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), or [CLI](restore-account-continuous-backup.md#trigger-restore-cli) with West US as the target location.

3. **Recover from an accidental write or delete operation within a container with a known restore timestamp** - For example, if you **know** that the contents of *Container 1* within *Database 1* were modified accidentally at timestamp T3. You can do a point in time restore from [Azure portal](restore-account-continuous-backup.md#restore-live-account), [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), or [CLI](restore-account-continuous-backup.md#trigger-restore-cli) into another account at timestamp T3 to recover the desired state of container.

4. **Restore an account to a previous point in time before the accidental delete of the database** - In the [Azure portal](restore-account-continuous-backup.md#restore-live-account), you can use the event feed pane to determine when a database was deleted and find the restore time. Similarly, with [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) and [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), you can discover the database deletion event by enumerating the database events feed and then trigger the restore command with the required parameters.

5. **Restore an account to a previous point in time before the accidental delete or modification of the container properties.** - In [Azure portal](restore-account-continuous-backup.md#restore-live-account), you can use the event feed pane to determine when a container was created, modified, or deleted to find the restore time. Similarly, with [Azure CLI](restore-account-continuous-backup.md#trigger-restore-cli) and [PowerShell](restore-account-continuous-backup.md#trigger-restore-ps), you can discover all the container events by enumerating the container events feed and then trigger the restore command with required parameters.

## Permissions

Azure Cosmos DB allows you to isolate and restrict the restore permissions for continuous backup account to a specific role or a principal. The owner of the account can trigger a restore and assign a role to other principals to perform the restore operation. To learn more, see the [Permissions](continuous-backup-restore-permissions.md) article.

## <a id="continuous-backup-pricing"></a>Pricing

Azure Cosmos DB accounts that have continuous 30-day backup enabled will incur an extra monthly charge to *store the backup*. Both the 30-day and 7-day tier of continuous back incur charges to *restore your data*. The restore cost is added every time the restore operation is initiated. If you configure an account with continuous backup but don't restore the data, only backup storage cost is included in your bill.

The following example is based on the price for an Azure Cosmos account deployed in West US. The pricing and calculation can vary depending on the region you're using, see the [Azure Cosmos DB pricing page](https://azure.microsoft.com/pricing/details/cosmos-db/) for latest pricing information.

* All accounts enabled with continuous backup policy with 30-day incur a monthly charge for backup storage that is calculated as follows:

  $0.20/GB \* Data size in GB in account \* Number of regions

* Every restore API invocation incurs a one time charge. The charge is a function of the amount of data restore and it's calculated as follows:

  $0.15/GB \* Data size in GB.

For example, if you have 1 TB of data in two regions then:

* Backup storage cost is calculated as (1000 \* 0.20 \* 2) = $400 per month

* Restore cost is calculated as (1000 \* 0.15) = $150 per restore

> [!TIP]
> For more information about measuring the current data usage of your Azure Cosmos DB account, see [Explore Azure Monitor Cosmos DB insights](../azure-monitor/insights/cosmosdb-insights-overview.md#view-utilization-and-performance-metrics-for-azure-cosmos-db). Continous 7-day tier does not incur charges for backup of the data.

## Continuous 30-day tier vs Continuous 7-day tier

* Retention period for one tier is 30-day vs 7-day for another tier.
* 30-day retention tier is charged for backup storage, 7-day retention tier isn't charged.
* Restore is always charged in either tier

## Customer-managed keys

See [How do customer-managed keys affect continuous backups?](./how-to-setup-cmk.md#how-do-customer-managed-keys-affect-continuous-backups) to learn:

* How to configure your Azure Cosmos DB account when using customer-managed keys with continuous backups.
* How do customer-managed keys affect restores?

## Current limitations

Currently the point in time restore functionality has the following limitations:

* Azure Cosmos DB APIs for SQL and MongoDB are supported for continuous backup. Cassandra API isn't supported now.

* Table API and Gremlin API are in preview and supported via PowerShell and Azure CLI.

* Multi-regions write accounts aren't supported.

* Azure Synapse Link and periodic backup mode can coexist in the same database account. However, analytical store data isn't included in backups and restores. When Azure Synapse Link is enabled, Azure Cosmos DB will continue to automatically take backups of your data in the transactional store at a scheduled backup interval.

* Azure Synapse Link and continuous backup mode can't coexist in the same database account. Currently database accounts with Azure Synapse Link enabled can't use continuous backup mode and vice-versa.

* The restored account is created in the same region where your source account exists. You can't restore an account into a region where the source account didn't exist.

* The restore window is only 30-day for continuous 30-day tier and it can't be changed. Similarly it's only 7-day for continuous 7-day tier and that also can't be changed.

* The backups aren't automatically geo-disaster resistant. You've to explicitly add another region to have resiliency for the account and the backup.

* While a restore is in progress, don't modify or delete the Identity and Access Management (IAM) policies. These policies grant the permissions for the account to change any VNET, firewall configuration.

* Azure Cosmos DB API for SQL or MongoDB accounts that create unique index after the container is created aren't supported for continuous backup. Only containers that create unique index as a part of the initial container creation are supported. For MongoDB accounts, you create unique index using [extension commands](mongodb/custom-commands.md).

* The point-in-time restore functionality always restores to a new Azure Cosmos account. Restoring to an existing account is currently not supported. If you're interested in providing feedback about in-place restore, contact the Azure Cosmos DB team via your account representative.

* After restoring, it's possible that for certain collections the consistent index may be rebuilding. You can check the status of the rebuild operation via the [IndexTransformationProgress](how-to-manage-indexing-policy.md) property.

* The restore process restores all the properties of a container including its TTL configuration. As a result, it's possible that the data restored is deleted immediately if you configured that way. In order to prevent this situation, the restore timestamp must be before the TTL properties were added into the container.

* Unique indexes in API for MongoDB can't be added or updated when you create a continuous backup mode account. They also can't be modified when you migrate an account from periodic to continuous mode.

* Continuous mode restore may not restore throughput setting valid as of restore point.

## Next steps

* Enable continuous backup using [Azure portal](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), [CLI](provision-account-continuous-backup.md#provision-cli), or [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* [Get the latest restorable timestamp](get-latest-restore-timestamp.md) for SQL and MongoDB accounts.
* Restore continuous backup account using [Azure portal](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), [CLI](restore-account-continuous-backup.md#restore-account-cli), or [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Migrate to an account from periodic backup to continuous backup](migrate-continuous-backup.md).
* [Manage permissions](continuous-backup-restore-permissions.md) required to restore data with continuous backup mode.
* [Resource model of continuous backup mode](continuous-backup-restore-resource-model.md)
