---
title: Previous monthly updates in Azure Synapse Analytics 
description: Archive of the new features and documentation improvements for Azure Synapse Analytics
author: ryanmajidi
ms.author: rymajidi 
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: conceptual
ms.date: 05/20/2022
---

# Previous monthly updates in Azure Synapse Analytics

This article describes previous month updates to Azure Synapse Analytics. For the most current month's release, check out [Azure Synapse Analytics latest updates](whats-new.md). Each update links to the Azure Synapse Analytics blog and an article that provides more information.

## May 2022 update

The following updates are new to Azure Synapse Analytics this month.

### General

**Get connected with the new Azure Synapse Influencer program!**  [Join a community of Azure Synapse Influencers](https://aka.ms/synapseinfluencers) who are helping each other achieve more with cloud analytics! The Azure Synapse Influencer program recognizes Azure Synapse Analytics users and advocates who actively support the community by sharing Synapse-related content, announcements, and product news via social media. 

### SQL

* **Data Warehouse Migration guide for Dedicated SQL Pools in Azure Synapse Analytics** - With the benefits that cloud migration offers, we hear that you often look for steps, processes, or guidelines to follow for quick and easy migrations from existing data warehouse environments. We just released a set of [Data Warehouse migration guides](./migration-guides/index.yml) to make your transition to dedicated SQL Pools in Azure Synapse Analytics easier. 

* **Automatic character column length calculation** - It's no longer necessary to define character column lengths!  Serverless SQL pools let you query files in the data lake without knowing the schema upfront. The best practice was to specify the lengths of character columns to get optimal performance. Not anymore! With this new feature, you can get optimal query performance without having to define the schema. The serverless SQL pool will calculate the average column length for each inferred character column or character column defined as larger than 100 bytes. The schema will stay the same, while the serverless SQL pool will use the calculated average column lengths internally. It will also automatically calculate the cardinality estimation in case there was no previously created statistic.

### Apache Spark for Synapse

* **Azure Synapse Dedicated SQL Pool Connector for Apache Spark Now Available in Python** - Previously, the Azure Synapse Dedicated SQL Pool connector was only available using Scala.  Now, it can be used with Python on Spark 3.  The only difference between the Scala and Python implementations is the optional Scala callback handle, which allows you to receive post-write metrics.  

  The following are now supported in Python on Spark 3: 

  * Read using Azure Active Directory (AD) Authentication or Basic Authentication 
  * Write to Internal Table using Azure AD Authentication or Basic Authentication 
  * Write to External Table using Azure AD Authentication or Basic Authentication  

  To learn more about the connector in Python, read [Azure Synapse Dedicated SQL Pool Connector for Apache Spark](./spark/synapse-spark-sql-pool-import-export.md).

* **Manage Azure Synapse Apache Spark configuration** - Apache Spark configuration management is always a challenging task because Spark has hundreds of properties. It is also challenging for you to know the optimal value for Spark configurations. With the new Spark configuration management feature, you can create a standalone Spark configuration artifact with auto-suggestions and built-in validation rules. The Spark configuration artifact allows you to share your Spark configuration within and across Azure Synapse workspaces. You can also easily associate your Spark configuration with a Spark pool, a Notebook, and a Spark job definition for reuse and minimize the need to copy the Spark configuration in multiple places. To learn more about the new Spark configuration management feature, read [Manage Apache Spark configuration](./spark/apache-spark-azure-create-spark-configuration.md).

### Synapse Data Explorer

* **Synapse Data Explorer live query in Excel** - Using the new Data Explorer web experience Open in Excel feature, you can now provide access to live results of your query by sharing the connected Excel Workbook with colleagues and team members.  You can open the live query in an Excel Workbook and refresh it directly from Excel to get the most up to date query results. To learn more about Excel live query, read [Open live query in Excel](https://techcommunity.microsoft.com/t5/azure-data-explorer-blog/open-live-kusto-query-in-excel/ba-p/3198500).

* **Use Managed Identities for External SQL Server Tables** - One of the key benefits of Azure Synapse is the ability to bring together data integration, enterprise data warehousing, and big data analytics. With Managed Identity support, Synapse Data Explorer table definition is now simpler and more secure. You can now use managed identities instead of entering in your credentials. 
  
  An external SQL table is a schema entity that references data stored outside the Synapse Data Explorer database. Using the Create and alter SQL Server external tables command, External SQL tables can easily be added to the Synapse Data Explorer database schema. 

  To learn more about managed identities, read [Managed identities overview](/azure/data-explorer/managed-identities-overview). 

  To learn more about external tables, read [Create and alter SQL Server external tables](/azure/data-explorer/kusto/management/external-sql-tables). 

* **New KQL Learn module (2 out of 3) is live!** - The power of Kusto Query Language (KQL) is its simplicity to query structured, semi-structured, and unstructured data together. To make it easier for you to learn KQL, we are releasing Learn modules. Previously, we released [Write your first query with Kusto Query Language](/learn/modules/write-first-query-kusto-query-language/). New this month is [Gain insights from your data by using Kusto Query Language](/learn/modules/gain-insights-data-kusto-query-language/). 
 
  KQL is the query language used to query Synapse Data Explorer big data. KQL has a fast-growing user community, with hundreds of thousands of developers, data engineers, data analysts, and students. 

  Check out the newest [KQL Learn Model](/learn/modules/gain-insights-data-kusto-query-language/) and see for yourself how easy it is to become a KQL master.  

  To learn more about KQL, read [Kusto Query Language (KQL) overview](/azure/data-explorer/kusto/query/). 

* **Azure Synapse Data Explorer connector for Microsoft Power Automate, Logic Apps, and Power Apps [Generally Available]** - The Azure Data Explorer connector for Power Automate enables you to orchestrate and schedule flows, send notifications, and alerts, as part of a scheduled or triggered task. To learn more, read [Azure Data Explorer connector for Microsoft Power Automate](/azure/data-explorer/flow) and [Usage examples for Azure Data Explorer connector to Power Automate](/azure/data-explorer/flow-usage). 

* **Dynamic events routing from event hub to multiple databases** - Routing events from Event Hub/IOT Hub/Event Grid is an activity commonly performed by Azure Data Explorer (ADX) users. Previously, you could route events only to a single database per defined connection. If you wanted to route the events to multiple databases, you needed to create multiple ADX cluster connections. 

  To simplify the experience, we now support routing events data to multiple databases hosted in a single ADX cluster. To learn more about dynamic routing, read [Ingest from event hub](/azure/data-explorer/ingest-data-event-hub-overview#events-routing). 

* **Configure a database using a KQL inline script as part of JSON ARM deployment template** - Previously, Azure Data Explorer supported running a Kusto Query Language (KQL) script to configure your database during Azure Resource Management (ARM) template deployment. Now, this can be done using an inline script provided inline as a parameter to a JSON ARM template. To learn more about using a KQL inline script, read [Configure a database using a Kusto Query Language script](/azure/data-explorer/database-script).

### Data Integration

* **Export pipeline monitoring as a CSV** - The ability to export pipeline monitoring to CSV has been added after receiving many community requests for the feature. Simply filter the Pipeline runs screen to the data you want and click ‘Export to CSV’. To learn more about exporting pipeline monitoring and other monitoring improvements, read [Azure Data Factory monitoring improvements](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/adf-monitoring-improvements/ba-p/3295531). 

* **Incremental data loading made easy for Synapse and Azure Database for PostgreSQL and MySQL** - In a data integration solution, incrementally loading data after an initial full data load is a widely used scenario. Automatic incremental source data loading is now natively available for Synapse SQL and Azure Database for PostgreSQL and MySQL. With a simple click, users can “enable incremental extract” and only inserted or updated rows will be read by the pipeline. To learn more about incremental data loading, read [Incrementally copy data from a source data store to a destination data store](../data-factory/tutorial-incremental-copy-overview.md). 

* **User-Defined Functions for Mapping Data Flows [Public Preview]** - We hear you that you can find yourself doing the same string manipulation, math calculations, or other complex logic several times. Now, with the new user-defined function feature, you can create customized expressions that can be reused across multiple mapping data flows. User-defined functions will be grouped in libraries to help developers group common sets of functions.  Once you’ve created a data flow library, you can add in your user-defined functions. You can even add in multiple arguments to make your function more reusable. To learn more about user-defined functions, read [User defined functions in mapping data flows](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/introducing-user-defined-functions-preview-for-mapping-data/ba-p/3414628).

* **Assert Error Handling** - Error handling has now been added to sinks following an assert transformation. Assert transformations enable you to build custom rules for data quality and data validation. You can now choose whether to output the failed rows to the selected sink or to a separate file. To learn more about error handling, read [Assert data transformation in mapping data flow](../data-factory/data-flow-assert.md).

* **Mapping data flows projection editing** - New UI updates have been made to source projection editing in mapping data flows. You can now update source projection column names and column types with the click of a button. To learn more about source projection editing, read [Source transformation in mapping data flow](../data-factory/data-flow-source.md).

### Synapse Link

**Azure Synapse Link for SQL [Public Preview]** - At Microsoft Build 2022, we announced the Public Preview availability of Azure Synapse Link for SQL, for both SQL Server 2022 and Azure SQL Database. Data-driven, quality insights are critical for companies to stay competitive. The speed to achieve those insights can make all the difference. The costly and time-consuming nature of traditional ETL and ELT pipelines is no longer enough. With this release, you can now take advantage of low- and no-code, near real-time data replication from your SQL-based operational stores into Azure Synapse Analytics. This makes it easier to run BI reporting on operational data in near real-time, with minimal impact on your operational store. To learn more, read [Announcing the Public Preview of Azure Synapse Link for SQL](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/announcing-the-public-preview-of-azure-synapse-link-for-sql/ba-p/3372986) and watch our YouTube video. 

> [!VIDEO https://www.youtube.com/embed/pgusZy34-Ek]

## Apr 2022 update

The following updates are new to Azure Synapse Analytics this month.

### SQL

* Cross-subscription restore for Azure Synapse SQL is now generally available.  Previously, it took many undocumented steps to restore a dedicated SQL pool to another subscription.  Now, with the PowerShell Az.Sql module 3.8 update, the Restore-AzSqlDatabase cmdlet can be used for cross-subscription restore.  To learn more, see [Restore a dedicated SQL pool (formerly SQL DW) to a different subscription](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-april-update-2022/ba-p/3280185).

* It is now possible to recover a SQL pool from a dropped server or workspace.  With the PowerShell Restore cmdlets in Az.Sql and Az.Synapse modules, you can now restore from a deleted server or workspace without filing a support ticket.  For more information, read [Synapse workspace SQL pools](./backuprestore/restore-sql-pool-from-deleted-workspace.md) or [standalone SQL pools (formerly SQL DW)](./sql-data-warehouse/sql-data-warehouse-restore-from-deleted-server.md), depending on your scenario.

### Synapse database templates and database designer

* Based on popular customer feedback, we've made significant improvements to our exploration experience when creating a lake database using an industry template.  To learn more, read [Quickstart: Create a new Lake database leveraging database templates](./database-designer/quick-start-create-lake-database.md).

* We've added the option to clone a lake database.  This  unlocks additional opportunities to manage new versions of databases or support schemas that evolve in discrete steps. You can quickly clone a database using the action menu available on the lake database.  To learn more, read [How-to: Clone a lake database](./database-designer/clone-lake-database.md).

* You can now use wildcards to specify custom folder hierarchies.  Lake databases sit on top of data that is in the lake and this data can live in nested folders that don’t fit into clean partition patterns. Previously, querying lake databases required that your data exists in a simple directory structure that you could browse using the folder icon without the ability to manually specify directory structure or use wildcard characters.  To learn more, read [How-to: Modify a datalake](./database-designer/modify-lake-database.md).

### Apache Spark for Synapse

* We are excited to announce the preview availability of Apache Spark™ 3.2 on Synapse Analytics. This new version incorporates user-requested enhancements and resolves 1,700+ Jira tickets. Please review the [official release notes](https://spark.apache.org/releases/spark-release-3-2-0.html) for the complete list of fixes and features and review the [migration guidelines between Spark 3.1 and 3.2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-31-to-32) to assess potential changes to your applications. For more details, read [Apache Spark version support and Azure Synapse Runtime for Apache Spark 3.2](./spark/apache-spark-version-support.md).  

* Assigning parameters dynamically based on variables, metadata, or specifying Pipeline specific parameters has been one of your top feature requests. Now, with the release of parameterization for the Spark job definition activity, you can do just that.  For more details, read [Transform data using Apache Spark job definition](quickstart-transform-data-using-spark-job-definition.md#settings-tab).

* We often receive customer requests to access the snapshot of the Notebook when there is a Pipeline Notebook run failure or there is a long-running Notebook job. With the release of the Synapse Notebook snapshot feature, you can now view the snapshot of the Notebook activity run with the original Notebook code, the cell output, and the input parameters. You can also access the snapshot of the referenced Notebook from the referencing Notebook cell output if you refer to other Notebooks through Spark utils. To learn more, read [Transform data by running a Synapse notebook](synapse-notebook-activity.md?tabs=classical#see-notebook-activity-run-history) and [Introduction to Microsoft Spark utilities](./spark/microsoft-spark-utilities.md?pivots=programming-language-scala#reference-a-notebook-1). 

### Security

* The Synapse Monitoring Operator RBAC role is now generally available.  Since the GA of Synapse, customers have asked for a fine-grained RBAC (role-based access control) role that allows a user persona to monitor the execution of Synapse Pipelines and Spark applications without having the ability to run or cancel the execution of these applications.  Now, customers can assign the Synapse Monitoring Operator role to such monitoring personas. This allows organizations to stay compliant while having flexibility in the delegation of tasks to individuals or teams. Learn more by reading [Synapse RBAC Roles](security/synapse-workspace-synapse-rbac-roles.md). 
### Data integration

* Microsoft has added Dataverse as a source and sink connector to Synapse Data Flows so that you can now build low-code data transformation ETL jobs in Synapse directly accessing your Dataverse environment. For more details on how to use this new connector, read [Mapping data flow properties](../data-factory/connector-dynamics-crm-office-365.md#mapping-data-flow-properties). 

* We heard from you that a 1-minute timeout for Web activity was not long enough, especially in cases of synchronous APIs. Now, with the response timeout property 'httpRequestTimeout', you can define timeout for the HTTP request up to 10 minutes. Learn more by reading [Web activity response timeout improvements](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/web-activity-response-timeout-improvement/ba-p/3260307).
 
### Developer experience

* Previously, if you wanted to reference a notebook in another notebook, you could only reference published or committed content. Now, when using %run notebooks, you can enable ‘unpublished notebook reference’ which will allow you to reference unpublished notebooks. When enabled, notebook run will fetch the current contents in the notebook web cache, meaning the changes in your notebook editor can be referenced immediately by other notebooks without having to be published (Live mode) or committed (Git mode).  To learn more, read [Reference unpublished notebook](spark/apache-spark-development-using-notebooks.md#reference-unpublished-notebook). 

## Mar 2022 update

The following updates are new to Azure Synapse Analytics this month.

### Developer Experience

* Code cells in Synapse notebooks that result in exception will now show standard output along with the exception message. This feature is supported for Python and Scala languages. To learn more, see the [example output when a code statement fails](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_1).

* Synapse notebooks now support partial output when running code cells. To learn more, see the [examples at this blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_1)

* You can now dynamically control Spark session configuration for the notebook activity with pipeline parameters. To learn more, see the [variable explorer feature of Synapse notebooks.](./spark/apache-spark-development-using-notebooks.md?tabs=classical#parameterized-session-configuration-from-pipeline)

* You can now reuse and manage notebook sessions without having to start a new one. You can easily connect a selected notebook to an active session in the list started from another notebook. You can detach a session from a notebook, stop the session, and monitor it. To learn more, see [how to manage your active notebook sessions.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_3)

* Synapse notebooks now capture anything written through the Python logging module, in addition to the driver logs. To learn more, see [support for Python logging.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_4)

### SQL

* Column Level Encryption for Azure Synapse dedicated SQL Pools is now Generally Available. With column level encryption, you can use different protection keys for each column with each key having its own access permissions. The data in CLE-enforced columns are encrypted on disk and remain encrypted in memory until the DECRYPTBYKEY function is used to decrypt it. To learn more, see [how to encrypt a data column](/sql/relational-databases/security/encryption/encrypt-a-column-of-data?view=azure-sqldw-latest&preserve-view=true).

* Serverless SQL pools now support better performance for CETAS (Create External Table as Select) and subsequent SELECT queries. The performance improvements include, a parallel execution plan resulting in faster CETAS execution and outputting multiple files. To learn more, see [CETAS with Synapse SQL](./sql/develop-tables-cetas.md) article and the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_7)

### Apache Spark for Synapse

* Synapse Spark Common Data Model (CDM) Connector is now Generally Available. The CDM format reader/writer enables a Spark program to read and write CDM entities in a CDM folder via Spark dataframes. To learn more, see [how the CDM connector supports reading, writing data, examples, & known issues](./spark/data-sources/apache-spark-cdm-connector.md).

* Synapse Spark Dedicated SQL Pool (DW) Connector now supports improved performance. The new architecture eliminates redundant data movement and uses COPY-INTO instead of PolyBase. You can authenticate through SQL basic authentication or opt into the Azure Active Directory/Azure AD based authentication method. It now has ~5x improvements over the previous version. To learn more, see [Azure Synapse Dedicated SQL Pool Connector for Apache Spark](./spark/synapse-spark-sql-pool-import-export.md)

* Synapse Spark Dedicated SQL Pool (DW) Connector now supports all Spark Dataframe SaveMode choices. It supports Append, Overwrite, ErrorIfExists, and Ignore modes. The Append and Overwrite are critical for managing data ingestion at scale. To learn more, see [DataFrame write SaveMode support](./spark/synapse-spark-sql-pool-import-export.md#supported-dataframe-save-modes)

* Accelerate Spark execution speed using the new Intelligent Cache feature. This feature is currently in public preview. Intelligent Cache automatically stores each read within the allocated cache storage space, detecting underlying file changes and refreshing the files to provide the most recent data. To learn more, see how to [Enable/Disable the cache for your Apache Spark pool](./spark/apache-spark-intelligent-cache-concept.md) or see the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_12)

### Security

* Azure Synapse Analytics now supports Azure Active Directory (Azure AD) authentication. You can turn on Azure AD authentication during the workspace creation or after the workspace is created. To learn more, see [how to use Azure AD authentication with Synapse SQL](./sql/active-directory-authentication.md).

* API support to raise or lower minimal TLS version for workspace managed SQL Server Dedicated SQL. To learn more, see [how to update the minimum TLS setting](/rest/api/synapse/sqlserver/workspace-managed-sql-server-dedicated-sql-minimal-tls-settings/update) or read the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_15) for more details.

### Data Integration

* Flowlets and CDC Connectors are now Generally Available. Flowlets in Synapse Data Flows allow for reusable and composable ETL logic. To learn more, see [Flowlets in mapping data flow](../data-factory/concepts-data-flow-flowlet.md) or see the [blog post.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-march-update-2022/ba-p/3269194#TOCREF_17)

* sFTP connector for Synapse data flows. You can read and write data while transforming data from sftp using the visual low-code data flows interface in Synapse. To learn more, see [source transformation](../data-factory/connector-sftp.md#source-transformation)

* Data flow improvements to Data Preview. To learn more, see [Data Preview and debug improvements in Mapping Data Flows](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/data-preview-and-debug-improvements-in-mapping-data-flows/ba-p/3268254?wt.mc_id=azsynapseblog_mar2022_blog_azureeng)

* Pipeline script activity. The Script Activity enables data engineers to build powerful data integration pipelines that can read from and write to Synapse databases, and other database types. To learn more, see [Transform data by using the Script activity in Azure Data Factory or Synapse Analytics](../data-factory/transform-data-using-script.md)

## Feb 2022 update

The following updates are new to Azure Synapse Analytics this month.

### SQL

* Serverless SQL Pools now support more consistent query execution times. [Learn how Serverless SQL pools automatically detect spikes in read latency and support consistent query execution time.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_2)

* [The `OPENJSON` function makes it easy to get array element indexes](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_3). To learn more, see how the OPENJSON function in a serverless SQL pool allows you to [parse nested arrays and return one row for each JSON array element with the index of each element](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true#array-element-identity).

### Data integration

* [Upserting data is now supported by the copy activity](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_5). See how you can natively load data into a temporary table and then merge that data into a sink table with [upsert.](../data-factory/connector-azure-sql-database.md?tabs=data-factory#upsert-data)

* [Transform Dynamics Data Visually in Synapse Data Flows.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_6) Learn more on how to use a [Dynamics dataset or an inline dataset as source and sink types to transform data at scale.](../data-factory/connector-dynamics-crm-office-365.md?tabs=data-factory#mapping-data-flow-properties)

* [Connect to your SQL sources in data flows using Always Encrypted](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_7). To learn more, see [how to securely connect to your SQL databases from Synapse data flows using Always Encrypted.](../data-factory/connector-azure-sql-database.md?tabs=data-factory)

* [Capture descriptions from asserts in Data Flows](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_8) To learn more, see [how to define your own dynamic descriptive messages](../data-factory/data-flow-expressions-usage.md#assertErrorMessages) in the assert data flow transformation at the row or column level.

* [Easily define schemas for complex type fields.](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-february-update-2022/ba-p/3221841#TOCREF_9) To learn more, see how you can make the engine to [automatically detect the schema of an embedded complex field inside a string column](../data-factory/data-flow-parse.md).

## Jan 2022 update

The following updates are new to Azure Synapse Analytics this month.

### Apache Spark for Synapse

You can now use four new database templates in Azure Synapse. [Learn more about Automotive, Genomics, Manufacturing, and Pharmaceuticals templates from the blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/four-additional-azure-synapse-database-templates-now-available/ba-p/3058044) or the [database templates article](./database-designer/overview-database-templates.md). These templates are currently in public preview and are available within the Synapse Studio gallery.

### Machine Learning

Improvements to the Synapse Machine Learning library v0.9.5 (previously called MMLSpark). This release simplifies the creation of massively scalable machine learning pipelines with Apache Spark. To learn more, [read the blog post about the new capabilities in this release](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_3) or see the [full release notes](https://microsoft.github.io/SynapseML/)

### Security

* The Azure Synapse Analytics security overview - A whitepaper that covers the five layers of security. The security layers include authentication, access control, data protection, network security, and threat protection. [Understand each security feature in detailed](./guidance/security-white-paper-introduction.md) to implement an industry-standard security baseline and protect your data on the cloud.

* TLS 1.2 is now required for newly created Synapse Workspaces. To learn more, see how [TLS 1.2 provides enhanced security using this article](./security/connectivity-settings.md) or the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_6). Login attempts to a newly created Synapse workspace from connections using TLS versions lower than 1.2 will fail.

### Data Integration

* Data quality validation rules using Assert transformation - You can now easily add data quality, data validation, and schema validation to your Synapse ETL jobs by using Assert transformation in Synapse data flows. To learn more, see the [Assert transformation in mapping data flow article](../data-factory/data-flow-assert.md) or [the blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_8).

* Native data flow connector for Dynamics - Synapse data flows can now read and write data directly to Dynamics through the new data flow Dynamics connector. Learn more on how to [Create data sets in data flows to read, transform, aggregate, join, etc. using this article](../data-factory/connector-dynamics-crm-office-365.md) or the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_9). You can then write the data back into Dynamics using the built-in Synapse Spark compute.

* IntelliSense and auto-complete added to pipeline expressions - IntelliSense makes creating expressions, editing them easy. To learn more, see how to [check your expression syntax, find functions, and add code to your pipelines.](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/intellisense-support-in-expression-builder-for-more-productive/ba-p/3041459)

### Synapse SQL

* COPY schema discovery for complex data ingestion. To learn more, see the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_12) or [how GitHub leveraged this functionality in Introducing Automatic Schema Discovery with auto table creation for complex datatypes](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/introducing-automatic-schema-discovery-with-auto-table-creation/ba-p/3068927).

* Serverless SQL pools now support the HASHBYTES function. HASHBYTES is a T-SQL function, which hashes values. Learn how to use [hash values in distributing data using this article](/sql/t-sql/functions/hashbytes-transact-sql) or the [blog post](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-january-update-2022/ba-p/3071681#TOCREF_13).

## December 2021 update

The following updates are new to Azure Synapse Analytics this month.

### Apache Spark for Synapse

* Accelerate Spark workloads with NVIDIA GPU acceleration [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--16536080) [article](./spark/apache-spark-rapids-gpu.md)
* Mount remote storage to a Synapse Spark pool [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1823990543) [article](./spark/synapse-file-mount-api.md)
* Natively read & write data in ADLS with Pandas [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-663522290) [article](./spark/tutorial-use-pandas-spark-pool.md)
* Dynamic allocation of executors for Spark [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1143932173) [article](./spark/apache-spark-autoscale.md)

### Machine Learning

* The Synapse Machine Learning library [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--463873803) [article](https://microsoft.github.io/SynapseML/docs/about/)
* Getting started with state-of-the-art pre-built intelligent models [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-2023639030) [article](./machine-learning/tutorial-form-recognizer-use-mmlspark.md)
* Building responsible AI systems with the Synapse ML library [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-914346508) [article](https://microsoft.github.io/SynapseML/docs/features/responsible_ai/Model%20Interpretation%20on%20Spark/)
* PREDICT is now GA for Synapse Dedicated SQL pools [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1594404878) [article](./machine-learning/tutorial-sql-pool-model-scoring-wizard.md)
* Simple & scalable scoring with PREDICT and MLFlow for Apache Spark for Synapse [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--213049585) [article](./machine-learning/tutorial-score-model-predict-spark-pool.md)
* Retail AI solutions [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--2020504048) [article](./machine-learning/quickstart-industry-ai-solutions.md)

### Security

* User-Assigned managed identities now supported in Synapse Pipelines in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1340445678) [article](../data-factory/credentials.md?context=%2Fazure%2Fsynapse-analytics%2Fcontext%2Fcontext&tabs=data-factory)
* Browse ADLS Gen2 folders in an Azure Synapse Analytics workspace in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1147067155) [article](how-to-access-container-with-access-control-lists.md)

### Data Integration

* Pipeline Fail activity [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1827125525) [article](../data-factory/control-flow-fail-activity.md)
* Mapping Data Flow gets new native connectors [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-717833003) [article](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/mapping-data-flow-gets-new-native-connectors/ba-p/2866754)
* More notebook export formats: HTML, Python, and LaTeX [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF3) 
* Three new chart types in notebook view: box plot, histogram, and pivot table [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF4)
* Reconnect to lost notebook session [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF5)

### Integrate

* Synapse Link for Dataverse [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1397891373) [article](/powerapps/maker/data-platform/azure-synapse-link-synapse)
* Custom partitions for Synapse link for Azure Cosmos DB in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--409563090) [article](../cosmos-db/custom-partitioning-analytical-store.md)
* Map data tool (Public Preview), a no-code guided ETL experience [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF7) [article](./database-designer/overview-map-data.md)
* Quick reuse of spark cluster [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF7) [article](../data-factory/concepts-integration-runtime-performance.md#time-to-live)
* External Call transformation [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF9) [article](../data-factory/data-flow-external-call.md)
* Flowlets (Public Preview) [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-december-2021-update/ba-p/3042904#REF10) [article](../data-factory/concepts-data-flow-flowlet.md)

## November 2021 update

The following updates are new to Azure Synapse Analytics this month.

### Synapse Data Explorer

* Synapse Data Explorer now available in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1022327194) [article](./data-explorer/data-explorer-overview.md)

### Working with Databases and Data Lakes

* Introducing Lake databases (formerly known as Spark databases) [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--795630373) [article](./database-designer/concepts-lake-database.md)
* Lake database designer now available in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1691882460) [article](./database-designer/concepts-lake-database.md#database-designer)
* Database Templates and Database Designer [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--115572003) [article](./database-designer/concepts-database-templates.md)

### SQL

* Delta Lake support for serverless SQL is generally available [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-564486367) [article](./sql/query-delta-lake-format.md)
* Query multiple file paths using OPENROWSET in serverless SQL [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1242968096) [article](./sql/query-single-csv-file.md)
* Serverless SQL queries can now return up to 200 GB of results [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1110860013) [article](./sql/resources-self-help-sql-on-demand.md)
* Handling invalid rows with OPENROWSET in serverless SQL [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--696594450) [article](./sql/develop-openrowset.md)

### Apache Spark for Synapse

* Accelerate Spark workloads with NVIDIA GPU acceleration [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--16536080) [article](./spark/apache-spark-rapids-gpu.md)
* Mount remote storage to a Synapse Spark pool [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1823990543) [article](./spark/synapse-file-mount-api.md)
* Natively read & write data in ADLS with Pandas [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-663522290) [article](./spark/tutorial-use-pandas-spark-pool.md)
* Dynamic allocation of executors for Spark [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1143932173) [article](./spark/apache-spark-autoscale.md)

### Machine Learning

* The Synapse Machine Learning library [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--463873803) [article](https://microsoft.github.io/SynapseML/docs/about/)
* Getting started with state-of-the-art pre-built intelligent models [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-2023639030) [article](./machine-learning/tutorial-form-recognizer-use-mmlspark.md)
* Building responsible AI systems with the Synapse ML library [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-914346508) [article](https://microsoft.github.io/SynapseML/docs/features/responsible_ai/Model%20Interpretation%20on%20Spark/)
* PREDICT is now GA for Synapse Dedicated SQL pools [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1594404878) [article](./machine-learning/tutorial-sql-pool-model-scoring-wizard.md)
* Simple & scalable scoring with PREDICT and MLFlow for Apache Spark for Synapse [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--213049585) [article](./machine-learning/tutorial-score-model-predict-spark-pool.md)
* Retail AI solutions [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--2020504048) [article](./machine-learning/quickstart-industry-ai-solutions.md)

### Security

* User-Assigned managed identities now supported in Synapse Pipelines in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--1340445678) [article](../data-factory/credentials.md?context=%2Fazure%2Fsynapse-analytics%2Fcontext%2Fcontext&tabs=data-factory)
* Browse ADLS Gen2 folders in an Azure Synapse Analytics workspace in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1147067155) [article](how-to-access-container-with-access-control-lists.md)

### Data Integration

* Pipeline Fail activity [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1827125525) [article](../data-factory/control-flow-fail-activity.md)
* Mapping Data Flow gets new native connectors [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-717833003) [article](https://techcommunity.microsoft.com/t5/azure-data-factory-blog/mapping-data-flow-gets-new-native-connectors/ba-p/2866754)

### Synapse Link

* Synapse Link for Dataverse [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId-1397891373) [article](/powerapps/maker/data-platform/azure-synapse-link-synapse)
* Custom partitions for Synapse link for Azure Cosmos DB in preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics-blog/azure-synapse-analytics-november-2021-update/ba-p/3020740#toc-hId--409563090) [article](../cosmos-db/custom-partitioning-analytical-store.md)

## October 2021 update

The following updates are new to Azure Synapse Analytics this month.

### General

* Manage your cost with Azure Synapse pre-purchase plans [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#manage-cost) [article](../cost-management-billing/reservations/synapse-analytics-pre-purchase-plan.md)
* Move your Azure Synapse workspace across Azure regions [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#move-workspace-region) [article](how-to-move-workspace-from-one-region-to-another.md)

### Apache Spark for Synapse

* Spark performance optimizations [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#spark-performance) 

### Security

* All Synapse RBAC roles are now generally available for use in production [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-rbac) [article](./security/synapse-workspace-synapse-rbac-roles.md)
* Apply User-Assigned Managed Identities for Double Encryption [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#user-assigned-managed-identities) [article](./security/workspaces-encryption.md)
* Synapse Administrators now have elevated access to dedicated SQL pools [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#elevated-access) [article](./security/synapse-workspace-access-control-overview.md)
    
###  Governance

* Synapse workspaces can now automatically push lineage data to Microsoft Purview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#synapse-purview-lineage) [article](../purview/how-to-lineage-azure-synapse-analytics.md)
 
### Integrate

* Use Stringify in data flows to easily transform complex data types to strings [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#stringify-transform) [article](../data-factory/data-flow-stringify.md)
* Control Spark session time-to-live (TTL) in data flows [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#data-flowspark-ttl) [article](../data-factory/concepts-integration-runtime-performance.md)

### CI/CD & Git

* Deploy Synapse workspaces using GitHub Actions [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#deploy-synapse-github-action) [article](./cicd/continuous-integration-delivery.md#configure-github-actions-secrets)
* More control creating Git branches in Synapse Studio [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#create-git-branch-in-studio) [article](./cicd/source-control.md#creating-feature-branches)

### Developer Experience

* Enhanced Markdown editing in Synapse notebooks preview [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-markdown-toolbar) [article](./spark/apache-spark-development-using-notebooks.md)
* Pandas dataframes automatically render as nicely formatted HTML tables [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#pandas-dataframe-html) [article](./spark/apache-spark-data-visualization.md)
* Use IPython widgets in Synapse Notebooks [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#notebook-ipythong-widgets) [article](./spark/apache-spark-development-using-notebooks.md)
* Mssparkutils runtime context now available for Python and Scala [blog](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/azure-synapse-analytics-october-update/ba-p/2875372#mssparkutils-context) [article](./spark/microsoft-spark-utilities.md?pivots=programming-language-python)

## Next steps

[Get started with Azure Synapse Analytics](get-started.md)