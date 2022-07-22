---
title: "Azure Arc-enabled data services validation"
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 06/14/2022
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: "Describes validation program for Kubernetes distributions for Azure Arc-enabled data services."
keywords: "Kubernetes, Arc, Azure, K8s, validation, data services, SQL Managed Instance"
---

# Azure Arc-enabled data services Kubernetes validation

Azure Arc-enabled data services team has worked with industry partners to validate specific distributions and solutions to host Azure Arc-enabled data services. This validation extends the [Azure Arc-enabled Kubernetes validation](../kubernetes/validation-program.md) for the data services. This article identifies partner solutions, versions, Kubernetes versions, SQL engine versions, and PostgreSQL Hyperscale versions that have been verified to support the data services. 

To see how all Azure Arc-enabled components are validated, see [Validation program overview](../validation-program/overview.md)

> [!NOTE]
> At the current time, Azure Arc-enabled SQL Managed Instance is generally available in select regions.
>
> Azure Arc-enabled PostgreSQL Hyperscale is available for preview in select regions.

## Partners

### Cisco

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
|Cisco Hyperflex on VMware <br/> Cisco IKS ESXi 6.7 U3 |v1.20.14|v1.4.1_2022-03-08|15.0.2255.119| PostgreSQL 12.3 (Ubuntu 12.3-1) |

### Dell

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.21.5|v1.4.1_2022-03-08|15.0.2255.119 | Not validated |
| PowerFlex version 3.6 |1.21.5|v1.4.1_2022-03-08|15.0.2255.119 | Not validated |
| PowerFlex CSI version 1.4 |1.21.5|v1.4.1_2022-03-08 | Not validated |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1) |
| PowerStore T|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3 (Ubuntu 12.3-1)|

### HPE

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
|HPE Superdome Flex 280|1.20.0|v1.8.0_2022-06-14|16.0.41.7339|12.3 (Ubuntu 12.3-1)

### Kublr

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
|Kublr |1.22.0 / 1.20.12 |v1.1.0_2021-11-02 |15.0.2195.191 |PostgreSQL 12.3 (Ubuntu 12.3-1) |

### Lenovo

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
|Lenovo ThinkAgile MX3520 |AKS on Azure Stack HCI 21H2|v1.0.0_2021-07-30 |15.0.2148.140|Not validated|

### Nutanix

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS: 5.19.1.5<br/>AHV: 20201105.1021<br/>PC: Version pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3 (Ubuntu 12.3-1)|

### Platform 9

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| Platform9  Managed Kubernetes v5.3.0 | 1.20.5 | v1.0.0_2021-07-30| 15.0.2195.191 | PostgreSQL 12.3 (Ubuntu 12.3-1) |

### PureStorage

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7	1.22.5 | 1.20.7 | v1.1.0_2021-11-02 | 15.0.2148.140 | Not validated |

### Red Hat

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| OpenShift 4.7.13 | 1.20.0 | v1.0.0_2021-07-30 | 15.0.2148.140 | postgres 12.3 (Ubuntu 12.3-1)|

### VMware

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
| TKGm v1.5.1 | 1.20.5 | v1.4.1_2022-03-08 |15.0.2255.119|postgres 12.3 (Ubuntu 12.3-1)|

### WindRiver

|Solution and version | Kubernetes version | Azure Arc-enabled data services version | SQL engine version | PostgreSQL Hyperscale version
|-----|-----|-----|-----|-----|
|WindRiver| v1.22.5|v1.1.0_2021-11-02 |15.0.2195.191|postgres 12.3 (Ubuntu 12.3-1) |

## Data services validation process

The Sonobuoy Azure Arc-enabled data services plug-in automates the provisioning and testing of Azure Arc-enabled data services on a Kubernetes cluster.

### Prerequisites
v1.22.5+vmware.1

- [Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio - Insider build](https://github.com/microsoft/azuredatastudio)

Create a Kubernetes config file configured to access the target Kubernetes cluster and set as the current context. How this file is generated and brought local to your computer is different from platform to platform. See [Kubernetes.io](https://kubernetes.io/docs/home/).

### Process

The conformance tests run as part of the Azure Arc-enabled Data services validation. A pre-requisite to running these tests is to pass on the Azure Arc-enabled Kubernetes tests for the Kubernetes distribution in use.

These tests verify that the product is compliant with the requirements of running and operating data services. This process helps assess if the product is enterprise ready for deployments.

The tests for data services cover the following in indirectly connected mode

1. Deploy data controller in indirect mode
2. Deploy [Azure Arc-enabled SQL Managed Instance](create-sql-managed-instance.md)
3. Deploy [Azure Arc-enabled PostgreSQL Hyperscale](create-postgresql-hyperscale-server-group.md)
4. Scale out [PostgreSQL Hyperscale](scale-out-in-postgresql-hyperscale-server-group.md)

More tests will be added in future releases of Azure Arc-enabled data services.

## Additional information

- [Validation program overview](../validation-program/overview.md)
- [Azure Arc-enabled Kubernetes validation](../kubernetes/validation-program.md)
- [Azure Arc validation program - GitHub project](https://github.com/Azure/azure-arc-validation/)

## Next steps

- [Plan an Azure Arc-enabled data services deployment](plan-azure-arc-data-services.md)
- [Create a data controller - indirectly connected with the CLI](create-data-controller-indirect-cli.md)
- To create a directly connected data controller, start with [Prerequisites to deploy the data controller in direct connectivity mode](create-data-controller-direct-prerequisites.md).
