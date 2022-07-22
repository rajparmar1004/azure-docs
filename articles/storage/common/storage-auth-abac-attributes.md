---
title: Actions and attributes for Azure role assignment conditions in Azure Storage (preview)
titleSuffix: Azure Storage
description: Supported actions and attributes for Azure role assignment conditions and Azure attribute-based access control (Azure ABAC) in Azure Storage. 
services: storage
author: jimmart-dev

ms.service: storage
ms.topic: conceptual
ms.date: 05/24/2022
ms.author: jammart
ms.reviewer: jiacfan
ms.subservice: common
---

# Actions and attributes for Azure role assignment conditions in Azure Storage (preview)

> [!IMPORTANT]
> Azure ABAC and Azure role assignment conditions are currently in preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities.
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

This article describes the supported attribute dictionaries that can be used in conditions on Azure role assignments for each Azure Storage [DataAction](../../role-based-access-control/role-definitions.md#dataactions). For the list of Blob service operations that are affected by a specific permission or DataAction, see [Permissions for Blob service operations](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations).

To understand the role assignment condition format, see [Azure role assignment condition format and syntax](../../role-based-access-control/conditions-format.md).

## Suboperations

Multiple Storage service operations can be associated with a single permission or DataAction. However, each of these operations that are associated with the same permission might support different parameters. *Suboperations* enable you to differentiate between service operations that require the same permission but support different set of attributes for conditions. Thus, by using a suboperation, you can specify one condition for access to a subset of operations that support a given parameter. Then, you can use another access condition for operations with the same action that doesn't support that parameter.

For example, the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` action is required for over a dozen different service operations. Some of these operations can accept blob index tags as request parameter, while others don't. For operations that accept blob index tags as a parameter, you can use blob index tags in a Request condition. However, if such a condition is defined on the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` action, all operations that don't accept tags as a request parameter cannot evaluate this condition, and will fail the authorization access check.

In this case, the optional suboperation `Blob.Write.WithTagHeaders` can be used to apply a condition to only those operations that support blob index tags as a request parameter.

> [!NOTE]
> Blobs also support the ability to store arbitrary user-defined key-value metadata. Although metadata is similar to blob index tags, you must use blob index tags with conditions. For more information, see [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md).

In this preview, storage accounts support the following suboperations:

> [!div class="mx-tableFixed"]
> | Display name | DataAction | Suboperation |
> | :--- | :--- | :--- |
> | [List blobs](#list-blobs) | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.List` |
> | [Read a blob](#read-a-blob) | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | **NOT** `Blob.List` |
> | [Read content from a blob with tag conditions](#read-content-from-a-blob-with-tag-conditions) | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions (deprecated)` |
> | [Sets the access tier on a blob](#sets-the-access-tier-on-a-blob) | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `Blob.Write.Tier` |
> | [Write to a blob with blob index tags](#write-to-a-blob-with-blob-index-tags) | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` |

## Azure Blob storage actions and suboperations

This section lists the supported Azure Blob storage actions and suboperations you can target for conditions.

### List blobs

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | List blobs |
> | **Description** | List blobs operation. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | **Suboperation** | `Blob.List` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name) |
> | **Request attributes** | [Blob prefix](#blob-prefix) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.List'})`<br/>[Example: Read or list blobs in named containers with a path](storage-auth-abac-examples.md#example-read-or-list-blobs-in-named-containers-with-a-path) |

### Read a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Read a blob |
> | **Description** | All blob read operations excluding list. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | **Suboperation** | NOT `Blob.List` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Encryption scope name](#encryption-scope-name) |
> | **Request attributes** | [Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND NOT SubOperationMatches{'Blob.List'})`<br/>[Example: Read blobs in named containers with a path](storage-auth-abac-examples.md#example-read-blobs-in-named-containers-with-a-path) |

### Read content from a blob with tag conditions

> [!IMPORTANT]
> Although `Read content from a blob with tag conditions` is currently supported for compatibility with conditions implemented during the ABAC feature preview, that suboperation has been deprecated and Microsoft recommends using the [“Read a blob”](#read-a-blob) action instead.
>
> When configuring ABAC conditions in the Azure portal, you might see "DEPRECATED: Read content from a blob with tag conditions". Remove the operation and replace it with the “Read a blob” operation instead.
>
> If you are authoring your own condition where you want to restrict read access by tag conditions, please refer to [Example: Read blobs with a blob index tag](storage-auth-abac-examples.md#example-read-blobs-with-a-blob-index-tag).

### Read blob index tags

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Read blob index tags |
> | **Description** | DataAction for reading blob index tags. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Blob index tags [Values in key]](#blob-index-tags-values-in-key)<br/>[Blob index tags [Keys]](#blob-index-tags-keys) |
> | **Request attributes** | [Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |
> | **Learn more** | [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md) |

### Find blobs by tags

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Find blobs by tags |
> | **Description** | DataAction for finding blobs by index tags. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Write to a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Write to a blob |
> | **Description** | DataAction for writing to blobs. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Encryption scope name](#encryption-scope-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'})`<br/>[Example: Read, write, or delete blobs in named containers](storage-auth-abac-examples.md#example-read-write-or-delete-blobs-in-named-containers) |

### Sets the access tier on a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Sets the access tier on a blob |
> | **Description** | DataAction for writing to blobs. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | **Suboperation** | `Blob.Write.Tier` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Encryption scope name](#encryption-scope-name) |
> | **Request attributes** | [Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.Tier'})` |

### Write to a blob with blob index tags

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Write to a blob with blob index tags |
> | **Description** | REST operations: Put Blob, Put Block List, Copy Blob and Copy Blob From URL. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | **Suboperation** | `Blob.Write.WithTagHeaders` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Encryption scope name](#encryption-scope-name) |
> | **Request attributes** | [Blob index tags [Values in key]](#blob-index-tags-values-in-key)<br/>[Blob index tags [Keys]](#blob-index-tags-keys) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})`<br/>`!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'} AND SubOperationMatches{'Blob.Write.WithTagHeaders'})`<br/>[Example: New blobs must include a blob index tag](storage-auth-abac-examples.md#example-new-blobs-must-include-a-blob-index-tag) |
> | **Learn more** | [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md) |

### Create a blob or snapshot, or append data

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Create a blob or snapshot, or append data |
> | **Description** | DataAction for creating blobs. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Encryption scope name](#encryption-scope-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action'})`<br/>[Example: Read, write, or delete blobs in named containers](storage-auth-abac-examples.md#example-read-write-or-delete-blobs-in-named-containers) |

### Write blob index tags

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Write blob index tags |
> | **Description** | DataAction for writing blob index tags. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path)<br/>[Blob index tags [Values in key]](#blob-index-tags-values-in-key)<br/>[Blob index tags [Keys]](#blob-index-tags-keys) |
> | **Request attributes** | [Blob index tags [Values in key]](#blob-index-tags-values-in-key)<br/>[Blob index tags [Keys]](#blob-index-tags-keys)<br/>[Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write'})`<br/>[Example: Existing blobs must have blob index tag keys](storage-auth-abac-examples.md#example-existing-blobs-must-have-blob-index-tag-keys) |
> | **Learn more** | [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md) |

### Write Blob legal hold and immutability policy

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Write Blob legal hold and immutability policy |
> | **Description** | DataAction for writing Blob legal hold and immutability policy. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/immutableStorage/runAsSuperUser/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Delete a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Delete a blob |
> | **Description** | DataAction for deleting blobs. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** | [Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete'})`<br/>[Example: Read, write, or delete blobs in named containers](storage-auth-abac-examples.md#example-read-write-or-delete-blobs-in-named-containers) |

### Delete a version of a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Delete a version of a blob |
> | **Description** | DataAction for deleting a version of a blob. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** | [Version ID](#version-id) |
> | **Principal attributes support** | True |
> | **Examples** | `!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action'})`<br/>[Example: Delete old blob versions](storage-auth-abac-examples.md#example-delete-old-blob-versions) |

### Permanently delete a blob overriding soft-delete

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Permanently delete a blob overriding soft-delete |
> | **Description** | DataAction for permanently deleting a blob overriding soft-delete. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** | [Version ID](#version-id)<br/>[Snapshot](#snapshot) |
> | **Principal attributes support** | True |

### Modify permissions of a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Modify permissions of a blob |
> | **Description** | DataAction for modifying permissions of a blob. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Change ownership of a blob

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Change ownership of a blob |
> | **Description** | DataAction for changing ownership of a blob. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Rename a file or a directory

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Rename a file or a directory |
> | **Description** | DataAction for renaming files or directories. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### All data operations for accounts with hierarchical namespace enabled

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | All data operations for accounts with hierarchical namespace enabled |
> | **Description** | DataAction for all data operations on storage accounts with hierarchical namespace enabled.<br/>If your role definition includes the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` action, you should target this action in your condition. Targeting this action ensures the condition will still work as expected if hierarchical namespace is enabled for a storage account. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | **Suboperation** |  |
> | **Resource attributes** | [Account name](#account-name)<br/>[Is Current Version](#is-current-version)<br/>[Is hierarchical namespace enabled](#is-hierarchical-namespace-enabled)<br/>[Container name](#container-name)<br/>[Blob path](#blob-path) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |
> | **Examples** | [Example: Read, write, or delete blobs in named containers](storage-auth-abac-examples.md#example-read-write-or-delete-blobs-in-named-containers)<br/>[Example: Read blobs in named containers with a path](storage-auth-abac-examples.md#example-read-blobs-in-named-containers-with-a-path)<br/>[Example: Read or list blobs in named containers with a path](storage-auth-abac-examples.md#example-read-or-list-blobs-in-named-containers-with-a-path)<br/>[Example: Write blobs in named containers with a path](storage-auth-abac-examples.md#example-write-blobs-in-named-containers-with-a-path)<br/>[Example: Read only current blob versions](storage-auth-abac-examples.md#example-read-only-current-blob-versions)<br/>[Example: Read current blob versions and any blob snapshots](storage-auth-abac-examples.md#example-read-current-blob-versions-and-any-blob-snapshots)<br/>[Example: Read only storage accounts with hierarchical namespace enabled](storage-auth-abac-examples.md#example-read-only-storage-accounts-with-hierarchical-namespace-enabled) |
> | **Learn more** | [Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

## Azure Queue storage actions

This section lists the supported Azure Queue storage actions you can target for conditions.

### Peek messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Peek messages |
> | **Description** | DataAction for peeking messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Put a message

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Put a message |
> | **Description** | DataAction for putting a message. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Put or update a message

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Put or update a message |
> | **Description** | DataAction for putting or updating a message. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/write` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Clear messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Clear messages |
> | **Description** | DataAction for clearing messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

### Get or delete messages

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Get or delete messages |
> | **Description** | DataAction for getting or deleting messages. |
> | **DataAction** | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | **Resource attributes** | [Account name](#account-name)<br/>[Queue name](#queue-name) |
> | **Request attributes** |  |
> | **Principal attributes support** | True |

## Azure Blob storage attributes

This section lists the Azure Blob storage attributes you can use in your condition expressions depending on the action you target. If you select multiple actions for a single condition, there might be fewer attributes to choose from for your condition because the attributes must be available across the selected actions.

> [!NOTE]
> Attributes and values listed are considered case-insensitive, unless stated otherwise.

### Account name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Account name |
> | **Description** | Name of a storage account. |
> | **Attribute** | `Microsoft.Storage/storageAccounts:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts:name] StringEquals 'sampleaccount'`<br/>[Example: Read or write blobs in named storage account with specific encryption scope](storage-auth-abac-examples.md#example-read-or-write-blobs-in-named-storage-account-with-specific-encryption-scope) |

### Blob index tags [Keys]

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Blob index tags [Keys] |
> | **Description** | Index tags on a blob resource.<br/>Arbitrary user-defined key-value properties that you can store alongside a blob resource. Use when you want to check the key in blob index tags. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&` |
> | **Attribute source** | Resource<br/>Request |
> | **Attribute type** | StringList |
> | **Is key case sensitive** | True |
> | **Hierarchical namespace support** | False |
> | **Examples** | `@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags&$keys$&] ForAllOfAnyValues:StringEquals {'Project', 'Program'}`<br/>[Example: Existing blobs must have blob index tag keys](storage-auth-abac-examples.md#example-existing-blobs-must-have-blob-index-tag-keys) |
> | **Learn more** | [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md)<br/>[Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

### Blob index tags [Values in key]

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Blob index tags [Values in key] |
> | **Description** | Index tags on a blob resource.<br/>Arbitrary user-defined key-value properties that you can store alongside a blob resource. Use when you want to check both the key (case-sensitive) and value in blob index tags. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags` |
> | **Attribute source** | Resource<br/>Request |
> | **Attribute type** | String |
> | **Is key case sensitive** | True |
> | **Hierarchical namespace support** | False |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:`*keyname*`<$key_case_sensitive$>`<br/>`@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEquals 'Cascade'`<br/>[Example: Read blobs with a blob index tag](storage-auth-abac-examples.md#example-read-blobs-with-a-blob-index-tag) |
> | **Learn more** | [Manage and find Azure Blob data with blob index tags](../blobs/storage-manage-find-blobs.md)<br/>[Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

### Blob path

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Blob path |
> | **Description** | Path of a virtual directory, blob, folder or file resource.<br/>Use when you want to check the blob name or folders in a blob path. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path] StringLike 'readonly/*'`<br/>[Example: Read blobs in named containers with a path](storage-auth-abac-examples.md#example-read-blobs-in-named-containers-with-a-path) |

> [!NOTE]
> When specifying conditions for the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` attribute, the values shouldn't include the container name or a preceding slash (`/`) character. Use the path characters without any URL encoding.

### Blob prefix

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Blob prefix |
> | **Description** | Allowed prefix of blobs to be listed.<br/>Path of a virtual directory or folder resource. Use when you want to check the folders in a blob path. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:prefix` |
> | **Attribute source** | Request |
> | **Attribute type** | String |
> | **Examples** | `@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:prefix] StringStartsWith 'readonly/'`<br/>[Example: Read or list blobs in named containers with a path](storage-auth-abac-examples.md#example-read-or-list-blobs-in-named-containers-with-a-path) |

> [!NOTE]
> When specifying conditions for the `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:prefix` attribute, the values shouldn't include the container name or a preceding slash (`/`) character. Use the path characters without any URL encoding.

### Container name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Container name |
> | **Description** | Name of a storage container or file system.<br/>Use when you want to check the container name. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'`<br/>[Example: Read, write, or delete blobs in named containers](storage-auth-abac-examples.md#example-read-write-or-delete-blobs-in-named-containers) |

### Encryption scope name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Encryption scope name |
> | **Description** | Name of the encryption scope used to encrypt data.<br/>Available only for storage accounts where hierarchical namespace is not enabled. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/encryptionScopes:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |
> | **Exists support** | True |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts/encryptionScopes:name] ForAnyOfAnyValues:StringEquals {'validScope1', 'validScope2'}`<br/>[Example: Read blobs with specific encryption scopes](storage-auth-abac-examples.md#example-read-blobs-with-specific-encryption-scopes) |
> | **Learn more** | [Create and manage encryption scopes](../blobs/encryption-scope-manage.md) |

### Is Current Version

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Is Current Version |
> | **Description** | Identifies if the resource is the current version of the blob, in contrast of a snapshot or a specific blob version. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:isCurrentVersion` |
> | **Attribute source** | Resource |
> | **Attribute type** | Boolean |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:isCurrentVersion] BoolEquals true`<br/>[Example: Read only current blob versions](storage-auth-abac-examples.md#example-read-only-current-blob-versions)<br/>[Example: Read current blob versions and a specific blob version](storage-auth-abac-examples.md#example-read-current-blob-versions-and-a-specific-blob-version) |

### Is hierarchical namespace enabled

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Is hierarchical namespace enabled |
> | **Description** | Whether hierarchical namespace is enabled on the storage account.<br/>Applicable only at resource group scope or above. |
> | **Attribute** | `Microsoft.Storage/storageAccounts:isHnsEnabled` |
> | **Attribute source** | Resource |
> | **Attribute type** | Boolean |
> | **Examples** | `@Resource[Microsoft.Storage/storageAccounts:isHnsEnabled] BoolEquals true`<br/>[Example: Read only storage accounts with hierarchical namespace enabled](storage-auth-abac-examples.md#example-read-only-storage-accounts-with-hierarchical-namespace-enabled) |
> | **Learn more** | [Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

### Snapshot

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Snapshot |
> | **Description** | The Snapshot identifier for the Blob snapshot.<br/>Available for storage accounts where hierarchical namespace is not enabled and currently in preview for storage accounts where hierarchical namespace is enabled. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:snapshot` |
> | **Attribute source** | Request |
> | **Attribute type** | DateTime |
> | **Exists support** | True |
> | **Hierarchical namespace support** | False |
> | **Examples** | `Exists @Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:snapshot]`<br/>[Example: Read current blob versions and any blob snapshots](storage-auth-abac-examples.md#example-read-current-blob-versions-and-any-blob-snapshots) |
> | **Learn more** | [Blob snapshots](../blobs/snapshots-overview.md)<br/>[Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

### Version ID

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Version ID |
> | **Description** | The version ID of the versioned Blob.<br/>Available only for storage accounts where hierarchical namespace is not enabled. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:versionId` |
> | **Attribute source** | Request |
> | **Attribute type** | DateTime |
> | **Exists support** | True |
> | **Hierarchical namespace support** | False |
> | **Examples** | `@Request[Microsoft.Storage/storageAccounts/blobServices/containers/blobs:versionId] DateTimeEquals '2022-06-01T23:38:32.8883645Z'`<br/>[Example: Read current blob versions and a specific blob version](storage-auth-abac-examples.md#example-read-current-blob-versions-and-a-specific-blob-version)<br/>[Example: Read current blob versions and any blob snapshots](storage-auth-abac-examples.md#example-read-current-blob-versions-and-any-blob-snapshots) |
> | **Learn more** | [Azure Data Lake Storage Gen2 hierarchical namespace](../blobs/data-lake-storage-namespace.md) |

## Azure Queue storage attributes

This section lists the Azure Queue storage attributes you can use in your condition expressions depending on the action you target.

### Queue name

> [!div class="mx-tdCol2BreakAll"]
> | Property | Value |
> | --- | --- |
> | **Display name** | Queue name |
> | **Description** | Name of a storage queue. |
> | **Attribute** | `Microsoft.Storage/storageAccounts/queueServices/queues:name` |
> | **Attribute source** | Resource |
> | **Attribute type** | String |

## See also

- [Example Azure role assignment conditions (preview)](storage-auth-abac-examples.md)
- [Azure role assignment condition format and syntax (preview)](../../role-based-access-control/conditions-format.md)
- [Troubleshoot Azure role assignment conditions (preview)](../../role-based-access-control/conditions-troubleshoot.md)
