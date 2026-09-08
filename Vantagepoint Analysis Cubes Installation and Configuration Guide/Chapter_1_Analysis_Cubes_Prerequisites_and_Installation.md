
# Chapter 1: Analysis Cubes Prerequisites and Installation

## Analysis Cubes Prerequisites
## GitHub Testing Part 2
### GitHub Testing 02/10/2026

The following prerequisites must be met before you configure and use Vantagepoint Analysis Cubes.

### Microsoft SQL Server

- You must have a supported version of Microsoft SQL Server installed.

<div class="note note note_note"><strong>Note:</strong> The SQL Native Client (SNAC or SQLNCLI11) is no longer included by default starting with Microsoft SQL 2022. You need to install the SQL Native Client manually if you are using Microsoft SQL 2022 or a newer version.</div>

- The Standard edition or Enterprise edition of SQL Server must be installed. These editions provide you with the required SQL Server Analysis Services. The SQL Edition checks have been updated to include the new core-based SQL licensing models.

- For a full list of Microsoft SQL Server releases (R), service packs (SP), and cumulative updates (CU) that Vantagepoint supports, see the Deltek Product Support Compatibility Matrix. You can download this at the Deltek Support Center.

<div class="note note note_note"><strong>Note:</strong> Service packs and Cumulative Updates for Microsoft SQL Server that Vantagepoint supports must be installed after Analysis Services is installed. If you already installed the update and you still need to install Analysis Services, install Analysis Services, and then reinstall the service pack.</div>

### Microsoft SQL Server Analysis Services

You must have Microsoft SQL Server Analysis Services (a component of SQL Server) installed. Vantagepoint only supports the Multidimensional mode (not the Tabular mode).

### Microsoft SQL Server Integration Services

You must have SQL Server Integration Services installed. This is a shared component of the SQL Server.

### Active Directory Domain Environment

You must have an Active Directory domain environment. Your Analysis Cubes clients and database server must be members of the domain.

### Microsoft SQL Server Availability Groups

If you are deploying Vantagepoint Analysis Cubes configured with Microsoft SQL Server Availability Groups, you must install and configure Analysis Cubes against the Primary Node, to support failover scenarios. For more information, see the chapter on Availability Groups in the *Deltek Vantagepoint Installation and Configuration Guide* and [Procedure for a SQL Availability Group Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_a_sql_availability_group_deployment), in this guide.

### Microsoft SQL Server Cluster

If you are deploying Vantagepoint Analysis Cubes to an SQL Server Cluster configuration, you must install and configure Analysis Cubes against the current Active Cluster Node, to support failover scenarios. For more information, see [Procedure for SQL Server Cluster Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_sql_server_cluster_deployment) in this document.

### Scaling out your Analysis Cubes Deployment

A default configuration of Analysis Cubes creates all components---Data Warehouse, Integration Services packages (ETL logic), and the Analysis Cubes---on the same server hosting your Vantagepoint database. You can set up your configuration such that the Data Warehouse, Integration Services Packages (ETL Logic), and Analysis Cubes exist on one or two different servers, thus freeing up the primary database server from the resource-intensive tasks of refreshing the Analysis Cubes data. This configuration also requires additional SQL Server licenses. The process that you use to create a scalable analysis cubes configuration is outlined in [Procedure for Scalable Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_scalable_deployment) and [Appendix A](Appendix_A.md) of this document.

### PowerShell Installation

If you are familiar with Deltek Vision Analysis Cubes, you know that the cubes were deployed using the Deltek Vision Resource Kit directly on the database server. With Vantagepoint, the cubes are deployed using the PowerShell deployment scripts (the same scripts used to install Vantagepoint), run from your Vantagepoint web server. The same requirements that apply when using PowerShell to install Vantagepoint are required for deploying the cubes. For more information, see the *Deltek Vantagepoint Installation and Configuration Guide*.

<div class="note note note_note"><strong>Note:</strong> Deltek provides OLAP (online analytical processing) services to assist you with the installation, configuration, and optimization of your Analysis Cubes. These services consist of consulting, web conferencing and training, installation guides, and reporting assistance. For more information, contact your Deltek account manager at <a href="mailto:accountmanager@deltek.com">accountmanager@deltek.com</a>.</div>

## Usage of SSL in this Document

SSL is a term commonly used when referring to both Secure Sockets Layer technology and its successor, Transport Layer Security (TLS). Although you may see references to SSL in this guide and in the application\'s user interface, all Deltek applications use current TLS protocols to provide protected connections between web servers and web browsers.

## Microsoft SQL Server Edition Dependencies

Some Analysis Cubes functionality works only if you use Microsoft SQL Server Enterprise Edition. If you are using the Standard edition, the following items are not included in the Analysis Cubes and Vantagepoint because the Standard Edition does not support semi-additive measures.

### Dimensions and Measures

The following dimensions and measures are not included in the data cubes if you use SQL Server Standard Edition.

| Dimension and Measure | Description |
|----------------|-------------------------------------------------------|
| Presentation Currency | All the dimensions in the Presentation Currency dimension group in the Project and General Ledger data cubes. |
| Accounts Receivable Trending | All the measures in the AR Trending measure group (including the measures in the Multicurrency subfolder) in the Project data cube. |
| Days Sales Outstanding (DSO) | <ul><li>The DSO 90 and DSO 360 measures in the Accounts Receivable measure group in the Project data cube.</li><li>The DSO 90 in Billing Currency and DSO 360 in Billing Currency measures in the Multicurrency folder in the Accounts Receivable group.</li><li> The DSO 90 in Project Currency and DSO 360 in Project Currency measures in the Multicurrency folder in the Accounts Receivable group.</li></ul>|
| Days Work-In-Progress Outstanding (DWO) | <ul><li>The DWO 90 and DWO 360 measures in the Unbilled folder in the Values group in the Project data cube.</li><li>The DWO 90 in Billing Currency and DWO 360 in Billing Currency measures in the Unbilled -- Multicurrency folder in the Values group in the Project data cube.</li><li>The DWO 90 in Project Currency and DWO 360 in Project Currency measures in the Unbilled -- Multicurrency folder in the Values group in the Project data cube.</li></ul> |


### Currency Exchange

The Currency Exchange tab in Vantagepoint does not display if you use SQL Server Standard Edition.

### Key Performance Indicators (KPIs)

The following Deltek-provided KPIs are not available if you use SQL Server Standard Edition:

- DSOTargetPrincipal

- DSOTargetOrg1

- DSOTargetProjectManager

- DSOTargetNoDimension

- DWOTargetPrincipal

- DWOTargetOrg1

- DWOTargetProjectManager

- DWOTargetNoDimension

## Microsoft SQL Server: Important Information

The following information applies for all supported editions for the core database and Analysis Services requirements of Vantagepoint.

### Multi-Dimensional Mode Required for Analysis Services

Analysis Services support several modes of installation, multi-dimensional, tabular (default in SQL 2017 and above) and PowerPivot. Vantagepoint only supports the multi-dimensional mode. If you have installed Analysis Services in either of the other modes, you need to uninstall Analysis Services and then reinstall in the required configuration. You will also need to reinstall the current SQL Server Cumulative Update. The PowerShell installation will validate the correct configuration.

### Database Compatibility Levels and Microsoft SQL Server Collation

#### Database Compatibility Level

The Data Warehouse (DW) database will be created using the Compatibility Level of the Model database, which should match the version of SQL Server. You should ensure that the Compatibility Level of your Vantagepoint database matches the Data Warehouse database. You can validate the Compatibility Level on the Options page of the Database properties, using SQL Server Management Studio.

#### SQL Server Collation

The Data Warehouse (DW) database will be created using the Collation of the Vantagepoint database. This may or may not match the SQL Server Collation settings. The Analysis Services Database will be based on the Collation of the Analysis Server. If you receive "Cannot resolve collation conflict" errors during the SQL Agent refresh job processing, ensure that the Collation settings of the Vantagepoint database, Data Warehouse, and Analysis Services match. You can validate the Collation settings of the SQL Server databases on the Options page of the Database properties using SQL Server Management Studio. You can validate the Analysis Services Collation settings on the Language/Collation page of the Analysis Services properties using SQL Server Management Studio.

### Service Accounts and Permissions

You can use domain based service accounts for the SQL Server services or use the default service accounts (<https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions?view=sql-server-ver15>). All service accounts used with Analysis Cubes must be entered in the form "Domain\\User", not "user@domain.com" as this format is not compatible with SQL Server domain logins.

#### Vantagepoint Process Server Service Account

The Vantagepoint Process Server service account must be a domain account and have rights to the Vantagepoint database, the data warehouse database, the Analysis Cube database and must also be a member of the SQL Agent User Role. The PowerShell installation will automatically grant the required rights. Specifically, the installation creates a SQL Server Credential (SQLAgentProxyCredential_CubeRefresh\_&lt;server&gt;) for the Process Server service account and also a SQL Agent Proxy (SQLAgentProxy_CubeRefresh\_&lt;server&gt;). The SQL Server Credential is needed to run the Proxy, which in turn, runs the SQL Agent Refresh job. The purpose of the Credential and Proxy objects is to grant the minimum necessary rights to the Process Server service account.

During installation, if you receive an error message indicating that the Process Server service account was not added to the SQLAgentUserRole, complete these steps to manually grant the rights to the user account:

1.  Open the SQL Server Management Studio and connect to your database server/instance.

2.  Navigate to **Security » Logins** and locate the SQL Login user account.

3.  Right-click the user account and select **Properties**.

4.  Click the User Mapping tab.

5.  Select the **MSDB database** check box.

6.  Scroll down the database role membership list to locate and select the **SQLAgentUserRole** check box.

7.  Click **OK** to save the changes.

## Vantagepoint Incremental Refresh for Analysis Cubes

Incremental refresh for Analysis Cubes allows the end user to schedule a refresh of only those cube data that were changed since the initial build and last full successful refresh. The advantage of the incremental refresh is refresh time for the cubes is much faster than a full refresh.

<div class="note note note_note"><strong>Note:</strong> Changes made to cube configuration, system labels, or user defined fields are not reflected when the Incremental Refresh job is run. In order for those changes to update the cube structure, the Full Refresh SQL Agent job needs to be executed.</div>

To schedule an incremental refresh:

1.  In the Vantagepoint navigation pane, go to **Settings » General » Analysis Cubes**.

2.  In the Refresh tab, click **Refresh** to display the Schedule dialog box.

3.  Select **Incremental Data Refresh** and fill in the other options.

      **Important:** The **Incremental Data Refresh** option is disabled if the cubes labels or measurements are changed *(path to labels / analysis cubes)*. A message is displayed to inform the end user that the labels need to be applied before incremental refresh is available again.

4.  Click **Schedule**.

<div class="note note note_note"><strong>Note:</strong> If the password for the Process Server service account changes you must also update the password in the SQL Agent Credential object in <strong>SSMS » Database Engine » Security » Credentials</strong> or with the following SQL statement:

ALTER CREDENTIAL &lt;CredentialName&gt; WITH IDENTITY = '&lt;ProcessServerServiceAccount&gt;', SECRET = '&lt;new_password&gt;'

The error you will receive in SQL Agent Job History if the password is not update is "Error authenticating proxy &lt;service_account&gt;...The user name or password is incorrect."</div>

### Reconfigure the Process Server Service Account to Run as a Domain Account

You can modify the Deltek Vantagepoint Process Server service account either using the PowerShell deployment script SetServiceAccounts switch or by changing the account directly in the Windows Services application.

<div class="word-special word-procedure Procedure">To reconfigure the Process Server service account to run as a domain account using the PowerShell deployment scripts:</div>

1.  Refer to the *SetServiceAccounts switch* in the Deltek Vantagepoint Installation and Maintenance Guide

<div class="word-special word-procedure Procedure">To reconfigure the Process Server service account to run as a domain account using the Windows Services application:</div>

1.  On the Vantagepoint web and/or dedicated process server, open the Services application under Administrative Tools.

2.  In the list of services, locate the Deltek Vantagepoint Process Server &lt;version&gt; service.

3.  Right-click the Deltek Vantagepoint Process Server &lt;version&gt; service and click **Properties**.

4.  On the Log On tab of the Properties dialog box:

      - If the service is running as a domain account, no changes are necessary.

      - If the service is not running as a domain account, select the **This account:** option, and in the **Account Name** field, enter a valid domain username in the form of &lt;*Domain*&gt;\\&lt;*Username*&gt;.

5.  Enter and confirm the password for the domain account.

6.  Restart the service when you are prompted.

      In order for the Process Server to function properly, the service account used must be a member of the local Administrators Group.