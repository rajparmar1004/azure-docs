---
title: Supported versions - Azure Database for PostgreSQL - Flexible Server
description: Describes the supported PostgreSQL major and minor versions in Azure Database for PostgreSQL - Flexible Server.
ms.author: sunila
author: sunilagarwal
ms.service: postgresql
ms.subservice: flexible-server
ms.topic: conceptual
ms.date: 07/14/2022
---

# Supported PostgreSQL major versions in Azure Database for PostgreSQL - Flexible Server

[!INCLUDE [applies-to-postgresql-flexible-server](../includes/applies-to-postgresql-flexible-server.md)]

Azure Database for PostgreSQL - Flexible Server currently supports the following major versions:

## PostgreSQL version 14

The current minor release is **14.3**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/14/static/release-14-3.html) to learn more about improvements and fixes in this release. New servers will be created with this minor version.
>[!NOTE]
> PostgreSQL community released an out of band version 14.4 to address a critical issue. See the [release notes](https://www.postgresql.org/docs/release/14.4/) and this [discussion thread](https://www.postgresql.org/message-id/165473835807.573551.1512237163040609764%40wrigleys.postgresql.org) for details and the workaround till your server is patched to 14.4.

## PostgreSQL version 13

The current minor release is **13.7**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/13/static/release-13-7.html) to learn more about improvements and fixes in this release. New servers will be created with this minor version. 

## PostgreSQL version 12

The current minor release is **12.11**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/12/static/release-12-11.html) to learn more about improvements and fixes in this release. New servers will be created with this minor version. Your existing servers will be automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 11

The current minor release is **11.16**. Refer to the [PostgreSQL documentation](https://www.postgresql.org/docs/11/static/release-11-16.html) to learn more about improvements and fixes in this release. New servers will be created with this minor version. Your existing servers will be automatically upgraded to the latest supported minor version in your future scheduled maintenance window.

## PostgreSQL version 10 and older

We don't support PostgreSQL version 10 and older for Azure Database for PostgreSQL - Flexible Server. Please use the [Single Server](../concepts-supported-versions.md) deployment option if you require older versions.

## Managing upgrades

The PostgreSQL project regularly issues minor releases to fix reported bugs. Azure Database for PostgreSQL automatically patches servers with minor releases during the service's monthly deployments.

Automation for major version upgrade isn't yet supported. For example, there's currently no automatic upgrade from PostgreSQL 11 to PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

## Managing PostgreSQL engine defects

Microsoft has a team of committers and contributors who work full time on the open source Postgres project and are long term members of the community. Our contributions include but aren't  limited to features, performance enhancements, bug fixes, security patches among other things. Our open source team also incorporates feedback from our Azure fleet (and customers) when prioritizing work, however please keep in mind that Postgres project has its own independent contribution guidelines, review process and release schedule.

If you encounter a PostgreSQL engine defect while working with Azure Database for PostgreSQL, Microsoft will take immediate action to mitigate the issue when possible, Microsoft will fix the defect to address the production issue and work with the community to incorporate it.


<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->