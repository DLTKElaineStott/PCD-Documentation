# Chapter 2: Configure Analysis Cubes

You must complete the steps in this chapter to configure your database server before you can use Analysis Cubes to create custom reports.

## Custom Reports Created in Vision

The following information applies if you created custom reports with Vision Analysis Cubes 7.6 and you are upgrading to Vantagepoint.

### Renamed and Deleted Vision Fields

If you have custom reports in which a Vision field name was renamed, the renamed field is automatically removed from your report. You must re-add the new field name to the report. Vision fields that were deleted from the Vantagepoint are automatically removed from your reports. This includes Opportunity information that now exists in Projects, or Vendor information that now is part of Firms.

<div class="note attention note_attention"><strong>Attention:</strong> For more information on database changes are located in the Links section of the Deltek Vantagepoint Release Notes. The linked Database Changes webpage provides a list of renamed and deleted fields in the "Removed Columns" and "Removed Tables" subsections.</div>

## Summary List of Analysis Cubes Configuration Steps

The following table lists the steps that are required to configure your database server for Analysis Cubes.

- For upgrade installations, start with Step 1.

- For new installations, start with Step 2.

The detailed instructions for each of the general configuration steps are included in the remaining sections of this chapter.

| Step | Where to Perform | Description |
|------|------------------------|------------------------------------------|
| 1 | Vantagepoint: **Module Activation** | If you purchased the Analysis Cubes module, activate it now so that setup can run. |
| 2 | Screen Designer form in Vantagepoint: <ul><li>In a hub, select **Design** in **Other Actions** or</li> <li>**Settings » General » Screen Designer**</li></ul> **Settings » General » User Defined Components** in the desktop application | In Vantagepoint, select the user defined fields to include in the data cubes.<br><br>See the Vantagepoint online Help for specific instructions. <br><br>Note the following character limits for user defined field labels: <ul><li>Less than 95 characters in length.</li><li>No invalid characters.</li></ul> During the installation process, a message shows user defined fields that do not pass these requirements. |
| 3 | Vantagepoint Web Server | Install, or upgrade tom the latest Vantagepoint version to ensure you have the most recent software. |
| 4 | Vantagepoint Web Server | Run the --IntelligenceSetupOption SetupCube switch to deploy Analysis Cubes. After all prompts have been completed for your deployment scenario, the setup will run unattended. See [Run the Script with the SetupCube Sub-Switch](#run_the_script_with_the_setupcube_sub-switch) for details on deployment scenarios. |
| 5 | SQL Server Management Studio | Validate that the Analysis Cubes deployment completed successfully. See [Confirm that the Vantagepoint Data Warehouse, Analysis Cubes, and SQL Server Agent Job Are Created](#confirm_that_the_vantagepoint_data_warehouse_analysis_cubes_and_sql_server_agent_job_are_created) for more information. |
| 6 | SQL Server Management Studio | Add Domain users that need access to Analysis Cubes to the Analysis Cubes Users Role. See [Add Your Domain Users to the User Accounts Analysis Cube Role](#add_your_domain_users_to_the_user_accounts_analysis_cube_role) for more information. |
| 7 | SQL Server Management Studio | Script the Analysis Cube Roles so that there is no need to re-add your Domain users after an Analysis Cube rebuild. See [Script the Role](#script_the_role) for more information. |
| 8 | Vantagepoint Web server or another server with a supported installation of IIS (Internet Information Services) | Optional: Configure Analysis Cubes for Internet Accessibility. See [Chapter 5: Configure Analysis Cubes for Internet Accessibility](Chapter_5_Configure_Analysis_Cubes_for_Internet_Accessibility.md) for instructions. |
|  | Vantagepoint Web Server | If you have multiple Vantagepoint databases, or you need to support cubes in different languages, you must set up data cubes for each database. Repeat the steps above to create additional data cubes. |
|  | Vantagepoint Web Server or Analysis Cubes Application "Refresh" tab functions | Ongoing Maintenance: After you initially configure Analysis Cubes, any time that you modify system labels, user defined fields available for Analysis Cubes, or Analysis Cubes configuration settings in Vantagepoint, you must use the PowerShell deployment scripts to apply these changes. See [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md) for more information. |


## Upgrade Vantagepoint Analysis Cubes from Earlier Versions

### Rebuilding the Analysis Cubes

If you currently have Vision Analysis Cubes configured and deployed, you must deploy the Analysis Cubes in their entirety when you upgrade to Vantagepoint; there is no upgrade path. See [Delete Your Existing Data Warehouse and Data Cubes](#delete_your_existing_data_warehouse_and_data_cubes) for more information.

The following is a summary of the steps that you must perform to rebuild the Analysis Cubes data cubes after you upgrade Vantagepoint, including MR's (Maintenance Releases) that contain Analysis Cube updates and major releases (such as 4.0, 4.5, and so on).

<div class="note note note_note"><strong>Note:</strong> Not all MR's or major releases will include Analysis Cube updates. The Vantagepoint upgrade installation will alert you to any required post-installation Analysis Cube updates that you need to complete. Always refer to the Release Notes for details on the changes.</div>

**To rebuild the Analysis Cubes after you install a release or MR with cube-related changes:**

1.  Run the PowerShell installation on your Vantagepoint web server to upgrade Vantagepoint and the analysis cube components.

2.  Run the -- IntelligenceSetupOption SetupCube switch and when prompted choose the option to Rebuild Analysis Cubes for &lt;database_name&gt; - &lt;language&gt;

## Delete Your Existing Data Warehouse and Data Cubes

If you are configuring Analysis Cubes for the first time, skip this step, and continue with the next section of this chapter.

The PowerShell script will detect whether Vision Analysis Cubes components exist on the database server. Deltek recommends that you remove the Vision application and all Analysis Cubes components before you deploy Vantagepoint Analysis Cubes.

The steps in this section explain how to:

- Uninstall the Deltek Vision application from the database server

- Remove the Data warehouse database

- Remove the SQL Server Agent job, which refreshes the data

- Remove the Analysis Services database

Naming conventions for the Analysis Cube components are as follows:

- Data warehouse database name: &lt;Vision database&gt;DW. For example, \"VisionDemo76DW\" in the example below.

- SQL Server Agent Job: Refresh &lt;Vision database&gt; DW and Cubes. For example, \"Refresh VisionDemo76 DW and Cubes\" in the example below.

- Analysis Services database: Deltek Vision Analysis - &lt;Vision Database&gt;

<div class="word-special word-procedure Procedure">To uninstall Deltek Vision from the database server:</div>

1.  Uninstall the Deltek Vision application from **Control Panel » Programs and Features » Uninstall a Program** or, **Settings » Apps** (depending on your operating system).

2. Remove the Vision ETL Environment Variables:

      a.  Click the **Start** button and then click **Run**.

      b.  Enter 'sysdm.cpl **SystemProperties'** (without the quotation marks).

      c.  Click the Advanced tab and then click the **Environment Variables** button.

      d.  Under System Variables, locate the following two lines and click the **Delete** button for each of them:

      - VisionETL_Configuration

      - VisionETL_Root_Dir

      e.  Reboot the computer

<div class="word-special word-procedure Procedure">To delete the Analysis Cubes data warehouse database, the SQL Agent job, and the Analysis Services database:</div>

1.  Connect to the Database Engine using SQL Server Management Studio.

2.  Expand the **Databases** folder.

3.  Right-click the data warehouse database and then click **Delete**.

4.  Expand the SQL Server Agent folder, and then expand the **Jobs** folder.

5.  Right-click the SQL Agent refresh job and then click **Delete**.

6.  Connect to Analysis Services using SQL Server Management Studio.

7.  Expand the **Databases** folder.

8.  Right-click the Analysis Services database and then click **Delete**.

## Naming Conventions for Analysis Cube Components

The naming convention of the Vantagepoint **Data Warehouse Database** is shown below, where &lt;database&gt; is the name of your Vantagepoint database.

| Language Specified in the Language Field | Data Warehouse Database Name |
|------------------------------------------|------------------------------|
| English (United States)                  | &lt;database&gt;DW               |
| English (International)                  | &lt;database&gt;DW_en-GB         |
| French (Canada)                          | &lt;database&gt;DW_fr-CA         |
| French (France)                          | &lt;database&gt;DW_fr-FR         |
| Spanish (International)                  | &lt;database&gt;DW_es-ES         |
| Dutch (Netherlands)                      | &lt;database&gt;DW_nl-NL         |
| German (Germany)                         | &lt;database&gt;DW_de-DE         |
| Portuguese (Brazil)                      | &lt;database&gt;DW_pt-BR         |

The naming conventions for the Vantagepoint **SQL Agent Refresh Jobs** is shown below where &lt;database&gt; is the name of your Vantagepoint database.

| Language Specified in the Language Field | SQL Agent Refresh Jobs Name |
|---------------------------------|--------------------------------------|
| English (United States) | Full Refresh &lt;database&gt;\_en-US DW and Cubes |
| English (International) | Full Refresh &lt;database&gt;\_en-GB DW and Cubes |
| French (Canada) | Full Refresh &lt;database&gt;\_fr-CA DW and Cubes |
| French (France) | Full Refresh &lt;database&gt;\_fr-FR DW and Cubes |
| Spanish (International) | Full Refresh &lt;database&gt;\_es-ES DW and Cubes |
| Dutch (Netherlands) | Full Refresh &lt;database&gt;\_nl-NL DW and Cubes |
| German (Germany) | Full Refresh &lt;database&gt;\_de-DE DW and Cubes |
| Portuguese (Brazil) | Full Refresh &lt;database&gt;\_pt-BR DW and Cubes |

The naming convention of the Vantagepoint **Analysis Database** is shown below where &lt;database&gt; is the name of your Vantagepoint database.

| Language Specified in the Language Field | Analysis Database Name |
|---------------------------------|--------------------------------------|
| English (United States) | Deltek Vantagepoint Analysis - &lt;database&gt; |
| English (International) | Deltek Vantagepoint Analysis - &lt;database&gt;\_en-GB |
| French (Canada) | Deltek Vantagepoint Analysis - &lt;database&gt;\_fr-CA |
| French (France) | Deltek Vantagepoint Analysis - &lt;database&gt;\_fr-FR |
| Spanish (International) | Deltek Vantagepoint Analysis - &lt;database&gt;\_es-ES |
| Dutch (Netherlands) | Deltek Vantagepoint Analysis - &lt;database&gt;\_nl-NL |
| German (Germany) | Deltek Vantagepoint Analysis - &lt;database&gt;\_de-DE |
| Portuguese (Brazil) | Deltek Vantagepoint Analysis - &lt;database&gt;\_pt-BR |

## Install Vantagepoint Analysis Cubes

When you run the PowerShell installation script on your Vantagepoint web server to install or upgrade your Vantagepoint software, the script components to install Vantagepoint Analysis Cubes are installed and updated automatically. After these are installed, a system administrator uses the - IntelligenceSetupOption SetupCube switch of the PowerShell installation to configure Analysis Cubes.

<div class="note note note_note"><strong>Note:</strong> You always run the PowerShell installation from the Vantagepoint web server, not the database server.</div>

## PowerShell Analysis Cubes Deployment Script

You use the PowerShell script to:

- Check that you have all the prerequisites installed.

- Create and configure the Vantagepoint data warehouse, SQL Agent refresh jobs and the Analysis Cubes.

- Apply system labels to the Analysis Cubes.

- Apply the Analysis Cube configurations (KPIs, user defined fields, and so on) to the cubes.

- Populate the data warehouse and cubes with data from your Vantagepoint database.

If you have Vantagepoint installed on more than one tier, you must run the PowerShell script from the Vantagepoint web server; the PowerShell script will not run on any other Vantagepoint tier.

You will need the following information in order to complete the Analysis Cube Deployments:

<table style="width:99%;">
<colgroup>
<col style="width: 47%" />
<col style="width: 52%" />
</colgroup>
<thead>
<tr>
<th>Prerequisite Information</th>
<th>Notes</th>
</tr>
</thead>
<tbody>
<tr>
<td>Ensure the Vantagepoint Analysis Cubes module has been activated</td>
<td>Verify in <strong>Settings » General » Modules</strong>.</td>
</tr>
<tr>
<td>Know the type of deployment you are using</td>
<td>Basic, Scalable, Availability Group, or Cluster</td>
</tr>
<tr>
<td>Know the type of setup you want to use</td>
<td>Interactive or Silent</td>
</tr>
<tr>
<td>Know the SQL Server Edition being used</td>
<td><p>Enterprise or Standard Edition is supported for production use.</p>
<p>The Developer Edition is supported for testing purposes only.</p></td>
</tr>
<tr>
<td>Ensure all required services are running</td>
<td>SQL Integration Services, SQL Database Engine, SQL Analysis Services, and SQL Server Agent.</td>
</tr>
<tr>
<td>Ensure Analysis Services is installed in the Multidimensional mode</td>
<td>Tabular and PowerPivot modes are not supported.</td>
</tr>
<tr>
<td>Weblink Password</td>
<td></td>
</tr>
<tr>
<td>Vantagepoint Application User account and password (non-Windows Integrated) that has access to the Analysis Cubes application in Vantagepoint. As of Vantagepoint 4.5.2, a Windows Integrated login can be used.</td>
<td>Log in to Vantagepoint as the user and ensure that you can access <strong>Settings » General » Analysis Cubes</strong>.</td>
</tr>
<tr>
<td>Process Server service account Password</td>
<td></td>
</tr>
<tr>
<td>The language for which you want to deploy Analysis Cubes.</td>
<td>If you are deploying multiple language, run the deployment again for each language.</td>
</tr>
</tbody>
</table>


### Run the Script with the SetupCube Sub-Switch

Use the PowerShell script, DeltekVantagepoint.ps1, with the - IntelligenceSetupOption SetupCube switch to install Vantagepoint Analysis Cubes. The following deployment scenarios are supported:

- Deploy Vantagepoint Analysis Cubes to the same server as your Vantagepoint database, a basic deployment. For more information, see [Procedure for Basic Deployment](#procedure_for_basic_deployment).

- Deploy Vantagepoint Analysis Cubes in a Scalable configuration (requires 1 or 2 additional SQL Servers with appropriate licensing). For more information, see [Procedure for Scalable Deployment](#procedure_for_scalable_deployment) and [Appendix A](Appendix_A.md).

- Deploy Vantagepoint Analysis Cubes when your Vantagepoint database is part of a SQL Server Availability Group. For more information, see [Procedure for a SQL Availability Group Deployment](#procedure_for_a_sql_availability_group_deployment) and the *Advanced Administration Topics* section of the *Deltek Vantagepoint Installation and Maintenance Guide*.

- Deploy Vantagepoint Analysis Cubes when your Vantagepoint database is part of a SQL Server Failover Cluster. For more information, see [Procedure for SQL Server Cluster Deployment](#procedure_for_sql_server_cluster_deployment).

- Deploy multiple instances of Vantagepoint Analysis Cubes for different databases or for different languages supported by Vantagepoint. For more information, see [Multiple Vantagepoint Databases](#multiple_vantagepoint_databases) and [Multiple Languages](#multiple_languages).

- Deploy Vantagepoint Analysis Cubes in a Silent setup. Silent setup is only supported for basic deployments. It is also possible to run multiple Analysis Cube actions (deploy, rebuild, reapply system labels or reapply cube configurations using the DelekVantagepointSilentInstallWrapper.ps1 script). For more information, see [Procedure for a Silent Installation of Analysis Cubes](#procedure_for_a_silent_installation_of_analysis_cubes) and [Procedure for Performing Multiple Silent Analysis Cube Updates](#procedure_for_performing_multiple_silent_analysis_cube_updates).

- Uninstall Deltek Vantagepoint Analysis Cubes for a specific database/language combination after deployment. For more information, see [Procedure for Uninstalling Analysis Cubes](#procedure_for_uninstalling_analysis_cubes).

<div class="note note note_note"><strong>Note:</strong> You run the SetupCube sub-switch as part of the regular Vantagepoint deployment scripts and you always run it from the Vantagepoint web server.</div>


### Procedure for Basic Deployment

<div class="word-special word-procedure Procedure">To run the script with the SetupCube sub-switch for a basic deployment of Analysis Cubes:</div>

1.  From the installation directory Scripts folder, enter .\\**DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**.

2.  When prompted, enter your Deltek Support username and password.

      The script checks prerequisites, verifies your access to Deltek Vantagepoint, checks your IIS prerequisites, and then displays the message:

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      Vantagepoint -- Analysis Cubes Setup

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      **Analysis Cube setup must be run from the Primary web/application server. TLS 1.2 changes are required on all servers. TLS 1.0 and SSL 2.0/3.0 must be disabled.**

      **Additionally, you will need an application login to save cube configurations. This can be a Vantagepoint login or a Windows Integrated login if your Vantagepoint application is configured for Windows Authentication.**

      **Please read the installation documentation (that explains the information above in more detail) before you continue.**

3.  Select **Yes** to acknowledge that you have read the installation documentation.

      The script asks if you are deploying in a Scalable Cube configuration.

4.  For a basic deployment enter **No** and then press ENTER.

      The script asks if you are deploying to a SQL Server Cluster. If you answered Yes to either of these prompts, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

5.  Again, for a basic deployment enter **No** and press ENTER.

      The script prompts you for the name of your Vantagepoint database server in the form (Server\\Instance). The default value in brackets will be the database server entered during Vantagepoint setup, which is taken from the DeltekVantagepointSettings.xml file.

6.  Either accept the default value, if correct, or enter the name of the database server that hosts your Vantagepoint database.

      The script checks the database server name entered to determine if the name is an IP Address or Custom DNS, which are not compatible with the Analysis Cube setup and attempts to resolve the name to the hostname of the database server. If the hostname can be resolved it will be used for the setup, if not you will receive an error message and will need to make sure that DNS resolution is properly setup before setup can continue.

      The script checks to determine if the database server entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

      The script runs through prerequisite checks:
      - Verifies the SQL Server Edition and supported version. If SQL Standard Edition is detected, the following message is displayed:

        **Setup has identified SQL Standard Edition. Please refer to the Vantagepoint &lt;documentation&gt; "Microsoft SQL Server Edition Dependencies" for limitations associated with this edition of SQL Server.**
      - Verifies that SQL Server services are installed and configured properly.
      - Checks to ensure that Analysis Services is installed in the correct mode.
        Analysis Services has three options for installation:
        - Tabular mode (default in SQL Server 2017 and higher)
        - Multidimensional mode **(required for Vantagepoint)**
        - PowerPivot mode

        To verify the mode installed, connect to Analysis Services with SQL Server Management Studio, right-click the server name, choose **Properties**, and check the **Server Mode property**. If the server mode property is not **Multidimensional**, you need to uninstall and reinstall Analysis Services in the correct mode.

      - Checks to ensure that the user running setup has Sysadmin rights to the database server.
      - Checks for Vision Analysis Cubes. Deltek recommends that you remove the Vision application and Analysis Cube configuration before you install Vantagepoint Analysis Cubes.

      The script then prompts for the password for Weblink in order to provide a list of valid databases for which cubes can be deployed and then prompts you for the name of the database. The database must exist in Weblink and must be configured for the same database server entered in Step 6.

7.  Enter the name of the Vantagepoint database and press ENTER. (If there is only one database it will be displayed in brackets as the default and you can press ENTER.)
    
    The script checks to determine if the Vantagepoint database entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.
    
    The script then prompts for a valid Vantagepoint application user and password. Setup needs this information or order to save cube configurations later in the script execution.

8.  Enter the Vantagepoint username and confirm the password. If your Vantagepoint installation is using Windows Authentication and your domain login is configured as an integrated login in Vantagepoint, you can simply press ENTER.

    The script then queries for the service account running the Deltek Vantagepoint Process Server and prompts for the password. The script retrieves the server name of the process server by first checking to see if a server is pinned to the Refresh Queue, next by checking to see if a process server name has been entered in the Application Server section of Process Server configuration and last by defaulting to the Vantagepoint web server where setup is being run. If you receive errors, check the Process Server and Refresh Queue configuration in Vantagepoint (**Settings » General » Process Servers**).

9.  Enter the password for the Process Server service account and press ENTER.

    The script verifies that the username and password are valid and that the account is a domain account. The script prompts you for the language to use to build the Analysis Cubes.

10. Enter the language code (for example. fr-CA) and press ENTER. If the language is English (United States), you can press ENTER, as it is the default language in brackets. The script checks for the following:

      a. Checks cube hierarchies to ensure that they do not exceed 100 characters in length. System Labels are combined to create the hierarchies. If any hierarchies exceed 100 characters, the script identifies them and exits. You must modify the system labels to reduce these hierarchies to fewer than 100 characters.
     
      b. Checks the database to identify whether or not multiple currencies are enabled. If they are, the script alerts the user to ensure that the currency exchange process server job is scheduled.
     
      c. Checks the database for unsupported characters and long labels (95 characters and more) in User Defined Fields that have been marked as Available for Cubes. If any are found, each affected UDF is marked as not available for cubes and setup alerts the user running the script that they will need to be fixed.
      
      d. Checks for reserved words that create label conflicts when building the analysis cube. If these conflicts are detected, setup identifies the conflict and exits. You then need to modify the conflicting system label and re-run setup. Two checks are performed:

       - To see if any of the "Org" System Labels (1-5) are using the reserved word "Market"

       - To see if the WBS1 System Label is using the reserved word "Contract"
      
      e.  Checks the database for duplicate system labels. If any are found, they are reported to the user running the script. Duplicate system labels may cause errors when creating the Analysis Services database. Duplicate system labels should be reviewed and/or changed before continuing setup.

      At this point the script has all of the information needed to deploy Analysis Cubes. A prompt displays, asking if you want to continue. Verify the information and press ENTER to proceed.

11. If Analysis Cubes have already been deployed for the database/language combination entered, you are prompted with a list of choices.
      - 1. Apply System Label updates.
      - 2. Apply Cube Configuration updates.
      - 3. Rebuild Analysis Cubes.
      - 4. Full (Data) Refresh of Analysis Cubes.
      - 5. Exit setup.

12. Enter the number of the option that you want to perform and press ENTER. For more information, see [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md).

#### Setup Steps Performed by the Script

After you enter all of the information needed to run **DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**, the script completes the following steps:

1.  The script creates the Vantagepoint data warehouse database, &lt;databasename&gt;DW, deleting it first if it already exists.

2.  The script modifies the Integration Services packages for use with your data source. The files are modified on the Vantagepoint web server and then copied, via PowerShell Remoting, to the database server. PowerShell Remoting must be enabled (see *Firewall Rules for PowerShell Remoting* in the *Deltek Vantagepoint Installation and Maintenance Guide* for more information). If the drive where Vantagepoint is installed on the web server does not exist on the database server, setup will create the Deltek Vantagepoint directory structure and copy the Integration Services Package files to the drive represented by the Program Files environment variable (in PowerShell this is represented by \$env:ProgramFiles).

3.  The script updates Analysis Cubes-specific tables and stored procedures in the Vantagepoint database. The script also checks to ensure that there are no unsupported characters and long labels (95 characters and more) in the User Defined Fields (UDF's) that are marked as Available for Cubes. If any are found, the script identifies these, marks those that are unavailable for cubes, and notifies the user running the script that these UDF's will need to be fixed.

4.  The script creates the Full and Incremental SQL Agent Refresh Jobs and grants the necessary SQL Server permissions to execute these jobs. Part of this process includes assigning the necessary permissions and creating a SQL Server Credential for the Process Server service account and a SQL Agent Proxy, which is assigned as the owner of the Refresh jobs. This ensures the minimum rights for the Process Server service account to execute the SQL Agent jobs.

5.  The script creates the Vantagepoint Analysis Cube database (including the specific system labels from your database), Deltek Vantagepoint Analysis - &lt;databasename&gt;\_&lt;language&gt;, deleting it first if it already exists. User accounts that have been added to the User Accounts Role in the cube are backed up and will be restored when the Analysis Cube database is rebuilt.

6.  If there are errors creating the Analysis Cube database, review the errors identified in the error message and then contact Deltek Support for assistance if necessary.

7.  The script creates an administrator (Service Accounts Role) and user (User Accounts Role) roles in the Analysis Cube database and adds the SQL Agent Service account, Process Server service account and the IIS Application Pool Identity to the Service Accounts role. The script restores any user accounts that were backed up in Step 6.

8.  After setup completes, add any users to the User Accounts Role that need direct access to the cube via Excel for creating Pivot Table reports.

9.  The script starts the Full Refresh SQL Agent job, which populates the data warehouse and Analysis Cubes with data from your Vantagepoint database. The Full Refresh may take a while to complete, depending on the size of your database. The job status updates every 30 seconds.

10. If there are errors in the refresh job, you can view the log using the steps below:

      a.  Open SQL Server Management and connect to your SQL Server instance.

      b.  Expand **(+) SQL Server Agent**.

      c.  Expand **(+) Jobs**.

      d.  Right-click the **Full Refresh &lt;database&gt;\_&lt;language&gt; DW and Cubes** job and then select **Properties**.

      e.  Select the **Steps** page, select Step 2, and then click the **Edit** button.

      f.  Click the **Advanced** page and then click the **View** button.

      If there is no log displayed: Close the **Property** pages, right-click the **Full Refresh** job again, select **View History**, locate the error identified with a red X, and then review the log message in the bottom pane. Contact Deltek Support if you need assistance.

11. When the Full Refresh job has completed, setup writes a log to the Vantagepoint\\logs\\&lt;date_timestamp&gt; folder.

12. If this is the first time that Analysis Cubes setup has been run, the script will Save the Analysis Cube configurations to the Vantagepoint database.

13. The script then applies User Defined Fields (marked for use in Analysis Cubes), User Defined Calculated Measures, Filters, and KPI's to the Analysis Cubes database.

14. The script processes the Analysis Cubes and checks the setup log for errors.


### Procedure for Scalable Deployment

<div class="word-special word-procedure Procedure">To run the script with the SetupCube sub-switch for a Scalable deployment of Analysis Cubes:</div>

1.  From the installation directory Scripts folder, enter .\\**DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**.

2.  When prompted, enter your Deltek Support username and password.

      The script checks prerequisites, verifies your access to Deltek Vantagepoint, checks your IIS prerequisites, and then displays the message:

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      Vantagepoint -- Analysis Cubes Setup

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      **Analysis Cube setup must be run from the Primary web/application server. TLS 1.2 changes are required on all servers. TLS 1.0 and SSL 2.0/3.0 must be disabled.**   

      **Additionally, you will need an application login to save cube configurations. This can be a Vantagepoint login or a Windows Integrated login if your Vantagepoint application is configured for Windows Authentication.**

      **Please read the installation documentation (that explains all above in more detail) before continuing.**

3.  Select **Yes** to acknowledge that you have read the installation documentation.

      The script displays the following message:

      **Vantagepoint Analysis Cubes are supported in a scalable configuration. Please review the Vantagepoint Analysis Cubes Installation and Configuration Guide for more information.**

      The script asks if you are deploying in a Scalable Cube configuration.

4.  For a Scalable deployment enter **Yes** and press ENTER.

      The script displays the following message:

      **A Scalable Cube configuration can be deployed in either a 2-tier or 3-tier configuration. The Vantagepoint database will always be on its own server. For a 2-tier configuration you will enter the same server name for both the DW and Analysis Services prompts.**

      The script prompts for the name of the database server.

5.  Either accept the default value if correct or enter the name of the server that hosts the Vantagepoint transaction database press ENTER.

      The script checks the database server name entered to determine if the name is an IP Address or Custom DNS, which are not compatible with the Analysis Cube setup and attempts to resolve the name to the hostname of the database server. If the hostname can be resolved it will be used for the setup, if not you will receive an error message and will need to make sure that DNS resolution is properly setup before setup can continue.

      The script prompts for the name of the Data Warehouse server.

6.  Enter the name of the server that will host the Data Warehouse components and press ENTER.

      The same IP Address / Custom DNS check (above) is made for the Data Warehouse server entered.

      The script prompts for the name of the Analysis Services server.

      Enter the name of the server that will host the Analysis Services components and press ENTER. The same IP Address / Custom DNS check (above) is made for the Analysis Services server entered.

      The script checks to determine if the database server entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

      The script runs through a variety of prerequisite checks:

      - Verifies the SQL Server Edition and supported version. If the SQL Standard Edition is detected, the following message is displayed:

        **Setup has identified SQL Standard Edition. Please refer to the Vantagepoint &lt;documentation&gt; "Microsoft SQL Server Edition Dependencies" for limitations associated with this edition of SQL Server.**

      - Verifies that SQL Server services are installed and configured properly.

        In a scalable configuration, domain delegation and SPNs (Service Principal Names) must be configured for the SQL Server service account on the DW Server. The script queries the SPNs configured for the SQL Server service account and displays them. If no SPNs are detected, the script indicates this and exits. Contact Deltek support if you need assistance.

      - Checks to ensure that Analysis Services is installed in the correct mode.

        Analysis Services has three options for installation:
        - Tabular mode (default in SQL Server 2017 and higher)
        - Multidimensional mode **(required for Vantagepoint)**
        - PowerPivot mode

        To verify the mode installed, connect to Analysis Services with SQL Server Management Studio, right-click the server name, choose **Properties**, and check the **Server Mode property**. If the server mode property is not **Multidimensional**, you need to uninstall and reinstall Analysis Services in the correct mode.

      - Checks to ensure the user running setup has Sysadmin rights to the database server.

      - Checks for Vision Analysis Cubes. Deltek recommends that you remove the Vision application and Analysis Cube configuration before you install Vantagepoint Analysis Cubes.

      The script then prompts for the password for Weblink in order to provide a list of valid databases for which cubes can be deployed and then prompts you for the name of the database. The database must exist in Weblink and must be configured for the same database server entered in Step 6 above.

7.  Enter the name of the Vantagepoint database and press ENTER. (If there is only one database it will be displayed in brackets as the default and you can press ENTER.)

      The script checks to determine if the Vantagepoint database entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

      The script then prompts for a valid Vantagepoint application user and password. Setup needs this information or order to save cube configurations later in the script execution.

8.  Enter the Vantagepoint username and confirm the password. If your Vantagepoint installation is using Windows Authentication and your domain login is configured as an integrated login in Vantagepoint, you can simply press ENTER. The script then queries for the service account running the Deltek Vantagepoint Process Server and prompts for the password. The script retrieves the server name of the process server by first checking to see if a server is pinned to the Refresh Queue, next by checking to see if a process server name has been entered in the Application Server section of Process Server configuration and last by defaulting to the Vantagepoint web server where setup is being run. If you receive errors, check the Process Server and Refresh Queue configuration in Vantagepoint (**Settings » General » Process Servers**).

 9.  Enter the password for the Process Server service account and press ENTER.

      The script verifies the username and password are valid and that the account is a domain account. The script prompts you for the language to use to build the Analysis Cubes.

10. Enter the language code (for example, fr-CA) and press ENTER. If the language is English (United States), you can press ENTER, as it is the default language in brackets.

      The script checks for the following:

      a.  Checks cube hierarchies to ensure that they do not exceed 100 characters in length. System Labels are combined to create the hierarchies. If any hierarchies exceed 100 characters, the script identifies them and exits. You must modify the system labels to reduce these hierarchies to fewer than 100 characters.

      b.  Checks the database to identify whether or not multiple currencies are enabled. If they are, the script alerts the user to ensure that the currency exchange process server job is scheduled.

      c.  Checks the database for unsupported characters and long labels (95 characters and more) in User Defined Fields that have been marked as Available for Cubes. If any are found, each affected UDF is marked as not available for cubes and setup alerts the user running the script that they will need to be fixed.

      d.  Checks for reserved words that create label conflicts when building the analysis cube. If these conflicts are detected, setup identifies the conflict and exits. You then need to modify the conflicting system label and re-run setup. Two checks are performed:

       - To see if any of the "Org" System Labels (1-5) are using the reserved word "Market"

       - To see if the WBS1 System Label is using the reserved word "Contract"

      e.  Checks the database for duplicate system labels. If any are found, they are reported to the user running the script. Duplicate system labels may cause errors when creating the Analysis Services database. Duplicate system labels should be reviewed and/or changed before continuing setup.

      At this point the script has all of the information needed to deploy Analysis Cubes and prompts if you want to continue. Verify the information entered and press ENTER to proceed.

11. If Analysis Cubes have already been deployed for the database/language combination entered, you are prompted with a list of choices.
       - 1. Apply System Label updates.
       - 2. Apply Cube Configuration updates.
       - 3. Rebuild Analysis Cubes.
       - 4. Full (Data) Refresh of Analysis Cubes.
       - 5. Exit setup

12. Enter the number of the option that you want and press ENTER. For more information on the options, see [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md).

#### Setup Steps Performed by the Script

After you enter all of the information needed to run **DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**, the script completes the following steps:

1.  The script creates a SQL Server Linked Server on the DW server for the Vantagepoint database, which is needed to create the DW database.

2.  The script creates the Vantagepoint data warehouse database, &lt;databasename&gt;DW, on the server designated as the DW server, deleting it first if it already exists.

3.  The script modifies the Integration Services packages for use with your data source. The files are modified on the Vantagepoint web server and then copied, via PowerShell Remoting, to the DW server. PowerShell Remoting must be enabled (see *Firewall Rules for PowerShell Remoting* in the *Deltek Vantagepoint Installation and Maintenance Guide* for more information). If the drive where Vantagepoint is installed on the web server does not exist on the database server, setup will create the Deltek Vantagepoint directory structure and copy the Integration Services Package files to the drive represented by the Program Files environment variable (in PowerShell this is represented by \$env:ProgramFiles).

4.  The script updates Analysis Cubes-specific tables and stored procedures in the Vantagepoint database. The script also checks to ensure that there are no unsupported characters and long labels (95 characters and more) in the User Defined Fields (UDF's) that are marked as Available for Cubes. If any are found, the script identifies these, marks these are unavailable for cubes and notifies the user running the PowerShell script that these UDF's will need to be fixed.

5.  The script creates the Full and Incremental SQL Agent Refresh JobS on the DW server and grants the necessary SQL Server permissions to execute these jobs. Part of this process includes assigning permissions and creating a SQL Server Credential for the Process Server service account and a SQL Agent Proxy, which is assigned as the owner of the Refresh jobs. This ensures the minimum rights for the Process Server service account to execute the SQL Agent jobs.

6.  The script creates the Vantagepoint Analysis Cube database on the server designated as the Analysis Services server (including the specific system labels from your database), Deltek Vantagepoint Analysis - &lt;databasename&gt;\_&lt;language&gt;, deleting it first if it already exists. User accounts that have been added to the User Accounts Role in the cube are backed up and will be restored when the Analysis Cube database is rebuilt.

7.  If there are errors creating the Analysis Cube database, review the error log identified in the error message and contact Deltek Support for further assistance.

8.  The script creates an administrator (Service Accounts Role) and user (User Accounts Role) roles in the Analysis Cube database and adds the SQL Agent Service account, Process Server service account and the IIS Application Pool Identity to the Service Accounts role. The script restores any user accounts that were backed up in Step 7.

9.  After setup completes, add any users to the User Accounts Role that need direct access to the cube via Excel for creating Pivot Table reports.

10. The script starts the Full Refresh SQL Agent job, which populates the data warehouse and Analysis Cube with data from your Vantagepoint database. The Full Refresh may take a while to complete, depending on the size of your database. The job status updates every 30 seconds.

11. If there are errors in the refresh job, view the log using these steps:

      a.  Open SQL Server Management and connect to your SQL Server instance.

      b.  Expand **(+) SQL Server Agent**.

      c.  Expand **(+) Jobs**.

      d.  Right-click the **Full Refresh &lt;database&gt;\_&lt;language&gt; DW and Cubes** job and select **Properties**.

      e.  Select the **Steps** page, select Step 2, and then click the **Edit** button.

      f.  Click the **Advanced** page and then click the **View** button.

      If there is no log displayed: Close the **Property** pages, right-click the **Full Refresh** job again, select **View History**, locate the error identified with a red X, and then review the log message in the bottom pane. Contact Deltek Support if you need assistance.

      When the Full Refresh job has completed, setup writes this log to the Vantagepoint\\logs\\&lt;date_timestamp&gt; folder.

12. If this is the first time that Analysis Cube setup has been run, the script will Save the Analysis Cube configurations to the Vantagepoint database.

11. The script applies User Defined Fields (marked for use in Analysis Cubes), User Defined Calculated Measures, Filters, and KPI's to the Analysis Cube database.

12. The script processes the Analysis Cube and checks the setup log for any errors.


### Procedure for SQL Server Cluster Deployment

<div class="word-special word-procedure Procedure">To run the script with the SetupCube sub-switch for a deployment of Analysis Cubes to a SQL Server Cluster:</div>

1.  From the installation directory Scripts folder, enter .\\**DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**.

2.  When prompted, enter your Deltek Support username and password.

      The script checks prerequisites, verifies your access to Deltek Vantagepoint, checks your IIS prerequisites, and then displays the message:

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      Vantagepoint -- Analysis Cubes Setup

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      **Analysis Cube setup must be run from the Primary web/application server. TLS 1.2 changes are required on all servers. TLS 1.0 and SSL 2.0/3.0 must be disabled.**

      **Additionally, you will need an application login to save cube configurations. This can be a Vantagepoint login or a Windows Integrated login if your Vantagepoint application is configured for Windows Authentication.**

      **Please read the installation documentation (that explains all above in more detail) before continuing.**

3.  Select **Yes** to acknowledge that you have read the installation documentation.

      The script asks if you are deploying in a Scalable Cube configuration.

4.  For a SQL Cluster deployment enter **No** and press ENTER.

5.  The script asks if you are deploying to a SQL Server Cluster.

6.  For a SQL Cluster deployment enter **Yes** and press ENTER.

      The script prompts you for the virtual cluster name of the SQL Server Cluster hosting the Vantagepoint database. The default value in brackets will be the database server entered during Vantagepoint setup, which is pulled from the DeltekVantagepointSettings.xml file.

      <div class="note note note_note"><strong>Note:</strong> An error will occur if you use the server name of any node in the SQL Cluster. You must enter the virtual cluster name to continue.</div>

7.  Either accept the default value (if correct) or enter the virtual cluster name of the database server that hosts your Vantagepoint database.

      The script checks to determine if the database server entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

      The script verifies your SQL Cluster configuration and, if needed, installs PowerShell Clustering tools.

      The script runs through prerequisite checks:

      - Verifies the SQL Server Edition and supported version. If the SQL Standard Edition is detected, the following message is displayed:

        **Setup has identified SQL Standard Edition. Please refer to the Vantagepoint &lt;documentation&gt; "Microsoft SQL Server Edition Dependencies" for limitations associated with this edition of SQL Server.**

      - Verifies that the SQL Server services are installed and configured properly.

      - Checks to ensure that Analysis Services is installed in the correct mode.

        Analysis Services has three options for installation:

        - Tabular mode (default in SQL Server 2017 and higher)

        - Multidimensional mode **(required for Vantagepoint)**

        - PowerPivot mode

        To verify the mode installed, connect to Analysis Services with SQL Server Management Studio, right-click the server name, choose **Properties**, and check the **Server Mode property**. If the server mode property is not **Multidimensional**, you need to uninstall and reinstall Analysis Services in the correct mode.

      - Checks to ensure that the user running setup has Sysadmin rights to the database server.

      - Checks for Vision Analysis Cubes. Deltek recommends that you remove the Vision application and Analysis Cube configuration before you install Vantagepoint Analysis Cubes.

      The script then prompts for the password for Weblink in order to provide a list of valid databases for which cubes can be deployed and then prompts you for the name of the database. The database must exist in Weblink and must be configured for the same database server entered in Step 6 above.

8.  Enter the name of the Vantagepoint database and press ENTER. If there is only one database it will be displayed in brackets as the default and you can press ENTER.

    The script checks to determine if the Vantagepoint database entered is part of a SQL Server Availability Group. If it is, skip ahead to the next sections for specific steps and information specific to your chosen configuration.

    The script then prompts for a valid Vantagepoint application user and password. Setup needs this information or order to save cube configurations later in the script execution.

9.  Enter the Vantagepoint username and confirm the password. If your Vantagepoint installation is using Windows Authentication and your domain login is configured as an integrated login in Vantagepoint, you can simply press ENTER.

    The script then queries for the service account running the Deltek Vantagepoint Process Server and prompts for the password. The script retrieves the server name of the process server by first checking to see if a server is pinned to the Refresh Queue, next by checking to see if a process server name has been entered in the Application Server section of Process Server configuration and last by defaulting to the Vantagepoint web server where setup is being run. If you receive errors, check the Process Server and Refresh Queue configuration in Vantagepoint (**Settings » General » Process Servers**).

10. Enter the password for the Process Server service account and then press ENTER.

    The script verifies the username and password are valid and that the account is a domain account. The script prompts you for the language to use to build the Analysis Cubes.

11. Enter the language code (for example, fr-CA) and press ENTER. If the language is English (United States), you can press ENTER, as it is the default language in brackets.

    The script checks for the following:

      a.  Checks cube hierarchies to ensure that they do not exceed 100 characters in length. System Labels are combined to create the hierarchies. If any hierarchies exceed 100 characters the script identifies them and exits. You must modify the system labels to reduce these hierarchies to fewer than 100 characters.

      b.  Checks the database to identify whether or not multiple currencies are enabled. If they are, the script alerts the user to ensure that the currency exchange process server job is scheduled.

      c.  Checks the database for unsupported characters and long labels (95 characters and more) in User Defined Fields that have been marked as Available for Cubes. If any are found, each affected UDF is marked as not available for cubes and setup alerts the user running the script that they will need to be fixed.

      d.  Checks for reserved words that create label conflicts when building the analysis cube. If these conflicts are detected, setup identifies the conflict and exits. You then need to modify the conflicting system label and re-run setup. Two checks are performed:

       - To see if any of the "Org" System Labels (1-5) are using the reserved word "Market"

       - To see if the WBS1 System Label is using the reserved word "Contract"

      e.  Checks the database for duplicate system labels. If any are found, they are reported to the user running the script. Duplicate system labels may cause errors when creating the Analysis Services database. Duplicate system labels should be reviewed and/or changed before continuing setup.

      At this point the script has all of the information needed to deploy Analysis Cubes and prompts if you want to continue. Verify the information entered and press ENTER to proceed.

12. If Analysis Cubes have already been deployed for the database/language combination entered, you are prompted with a list of choices.

      - 1. Apply System Label updates.

      - 2. Apply Cube Configuration updates.

      - 3. Rebuild Analysis Cubes.

      - 4. Full (Data) Refresh of Analysis Cubes.

      - 5. Exit setup

13. Enter the number of the option you want and press ENTER. For information about these options, see [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md).

#### Setup Steps Performed by the Script

After you enter all of the information needed to run **DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**, the script completes the following steps:

1.  The script creates the Vantagepoint data warehouse database, &lt;databasename&gt;DW, deleting it first if it already exists.

2.  The script modifies the Integration Services packages for use with your data source, specifically the virtual cluster name. The files are modified on the Vantagepoint web server and then copied, via PowerShell Remoting, to all nodes in the SQL Cluster. PowerShell Remoting must be enabled (see *Firewall Rules for PowerShell Remoting* in the *Deltek Vantagepoint Installation and Maintenance Guide* for more information). If the drive where Vantagepoint is installed on the web server does not exist on the database server, setup will create the Deltek Vantagepoint directory structure and copy the Integration Services Package files to the drive represented by the Program Files environment variable (in PowerShell this is represented by \$env:ProgramFiles).

      The use of the virtual cluster name, and the copying of the Analysis Cubes files to all nodes that are part of the Cluster, ensures that the data refresh jobs will continue to run in the event of a failover, regardless of which cluster node is acting as the primary node.

3.  The script configures the Integration Services packages to use the virtual cluster name in the configuration so that in the event of a Failover of the cluster, the SQL Agent Refresh Job will continue to run with no additional configuration needed.

4.  The script updates Analysis Cube-specific tables and stored procedures in the Vantagepoint database. The script also checks to ensure that there are no unsupported characters and long labels (95 characters and more) in the User Defined Fields (UDF's) that are marked as Available for Cubes. If any are found, the script identifies these, marks these are unavailable for cubes and notifies the user running the PowerShell script that these UDF's will need to be fixed.

5.  The script creates the Full and Incremental SQL Agent Refresh Jobs and grants the necessary SQL Server permissions to execute these jobs. Part of this process includes assigning the necessary permissions and creating a SQL Server Credential for the Process Server service account and a SQL Agent Proxy which is assigned as the owner of the Refresh jobs. This ensures the minimum rights for the Process Server service account to execute the SQL Agent jobs.

6.  The script creates the Vantagepoint Analysis Cube database (including the specific system labels from your database), Deltek Vantagepoint Analysis - &lt;databasename&gt;\_&lt;language&gt;, deleting it first if it already exists. User accounts that have been added to the User Accounts Role in the cube are backed up and will be restored when the Analysis Cube database is rebuilt.

7.  If there are errors creating the Analysis Cube database, review the error log identified in the error message and contact Deltek Support for assistance.

8.  The script creates an administrator (Service Accounts Role) and user (User Accounts Role) roles in the Analysis Cube database and adds the SQL Agent Service account, Process Server service account and the IIS Application Pool Identity to the Service Accounts role. The script restores any user accounts that were backed up in Step 7.

9.  After setup completes, add any users to the User Accounts Role that need direct access to the cube via Excel for creating Pivot Table reports.

10. The script starts the Full Refresh SQL Agent job, which will populate the data warehouse and Analysis Cube with data from your Vantagepoint database. The Full Refresh may take a while to complete, depending on the size of your database. The job status updates every 30 seconds.

11. If there are errors in the refresh job, you can view the log:

      a.  Open SQL Server Management and connect to your SQL Server instance.

      b.  Expand **(+) SQL Server Agent**.

      c.  Expand **(+) Jobs**.

      d.  Right-click the **Full Refresh &lt;database&gt;\_&lt;language&gt; DW and Cubes** job and select **Properties**.

      e.  Select the **Steps** page, select Step 2, and then click the **Edit** button.

      f.  Click the **Advanced** page and then click the **View** button.

      If there is no log displayed: Close the **Property** pages, right-click the **Full Refresh** job again, select **View History**, locate the error identified with a red X, and then review the log message in the bottom pane. Contact Deltek Support if you need assistance.

12. When the Full Refresh job has completed, setup writes this log to the Vantagepoint\\logs\\&lt;date_timestamp&gt; folder.

13. If this is the first time that Analysis Cube setup has been run, the script saves the Analysis Cube configurations to the Vantagepoint database.

14. The script applies User Defined Fields (marked for use in Analysis Cubes), User Defined Calculated Measures, Filters, and KPI's to the Analysis Cube database.

15. The script processes the Analysis Cube and checks the setup log for any errors.


### Procedure for a SQL Availability Group Deployment

<div class="word-special word-procedure Procedure">To run the script with the SetupCube sub-switch for a SQL Availability Group deployment of Analysis Cubes:</div>

1.  From the installation directory Scripts folder, enter .\\**DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**.

2.  When prompted, enter your Deltek Support username and password.

      The script checks prerequisites, verifies your access to Deltek Vantagepoint, checks your IIS prerequisites, and then displays the message:

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      Vantagepoint -- Analysis Cubes Setup

      \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--

      **Analysis Cube setup must be run from the Primary web/application server. TLS 1.2 changes are required on all servers. TLS 1.0 and SSL 2.0/3.0 must be disabled.**

      **Additionally, you will need an application login to save cube configurations. This can be a Vantagepoint login or a Windows Integrated login if your Vantagepoint application is configured for Windows Authentication.**

      **Please read the installation documentation (that explains all above in more detail) before continuing.**

3.  Select **Yes** to acknowledge that you have read the installation documentation.

      The script asks if you are deploying in a Scalable Cube configuration.

4.  For deployment to a SQL Availability Group, enter **No** and then press ENTER.

      The script asks if you are deploying to a SQL Server Cluster.

5.  Again, for deployment to a SQL Availability Group, enter **No** and then press ENTER.

      The script prompts you for the name of your Vantagepoint database server in the form (Server\\Instance). The default value in brackets will be the database server entered during Vantagepoint setup, which is pulled from the DeltekVantagepointSettings.xml file.

      The Availability Group Listener Name should be used as the database server name.

6.  Either accept the default value, if correct, or enter the Availability Group Listener Name as the server that hosts your Vantagepoint database.

      The script checks to determine if the database server entered is part of a SQL Server Availability Group and whether the listener name was used. If the listener name was not used, the script displays the following message:

      **Setup has detected that the database server entered (&lt;name of server entered&gt;) is part of a SQL Server Availability Group but you have entered the physical server name not the Availability Group listener name. If your Vantagepoint database is part of an Availability Group, please exit setup and use the Availability group Listener Name as the database server. Setup will use the listener name for prerequisite checks. NOTE: The Weblink entry for your database, if part of an Availability Group, also needs to use the listener name (&lt;Availability Group Listener Name&gt;).**

      <div class="note note note_note"><strong>Note:</strong> When using Availability Groups, the listener name should be used. If the SQL Server used in the Weblink setup for your database does not match the name of the server entered during the Analysis Cube setup, setup will be unable to identify your database and setup cannot continue.</div>

      **If your database is not part of the Availability Group configuration, setup can continue. Setup will verify the database configuration after the database name has been entered later in setup.**

      This message indicates that if your Vantagepoint database exists on a server that is part of an Availability Group, but the database itself is not part of an Availability Group, setup can continue.

      The script runs through prerequisite checks:

      - Verifies the SQL Server Edition and supported version. If the SQL Standard Edition is detected, the following message is displayed:

        **Setup has identified SQL Standard Edition. Please refer to the Vantagepoint &lt;documentation&gt; "Microsoft SQL Server Edition Dependencies" for limitations associated with this edition of SQL Server.**

      - Verifies that SQL Server services are installed and configured properly.

      - Checks to ensure that Analysis Services is installed in the correct mode.

        Analysis Services has three options for installation:

        - Tabular mode (default in SQL Server 2017 and higher)

        - Multidimensional mode **(required for Vantagepoint)**

        - PowerPivot mode

        To verify the mode installed, connect to Analysis Services with SQL Server Management Studio, right-click the server name, choose **Properties**, and check the **Server Mode property**. If the server mode property is not **Multidimensional**, you need to uninstall and reinstall Analysis Services in the correct mode.

      - Checks to ensure that the user running setup has Sysadmin rights to the database server.

      - Checks for Vision Analysis Cubes. Deltek recommends that you remove the Vision application and Analysis Cube configuration before you install Vantagepoint Analysis Cubes.

      The script then prompts for the password for Weblink, in order to provide a list of valid databases for which cubes can be deployed, and then prompts you for the name of the database. The database must exist in Weblink and must be configured for the same database server entered in Step 6 above.

7.  Enter the name of the Vantagepoint database and press ENTER. If there is only one database it will be displayed in brackets as the default and you can press ENTER.

      The script checks to determine if the Vantagepoint database entered is part of a SQL Server Availability Group and if the listener name was used. If the database is part of an Availability Group and the listener name was not used, the following message is displayed:

      **Setup has detected your Vantagepoint database is configured in a SQL Server Availability Group but you have entered a server name (&lt;name of server entered&gt;), not the Availability Group listener name (&lt;Availability Group Listener Name&gt;). Setup will use the Availability Group listener in the configuration and will deploy the Data Warehouse and Analysis cubes to the PRIMARY Node (&lt;name of Primary node&gt;).**

      The script then prompts for a valid Vantagepoint application user and password. Setup needs this information or order to save cube configurations later in the script execution.

8.  Enter the Vantagepoint user name and confirm the password. If your Vantagepoint installation is using Windows Authentication and your domain login is configured as an integrated login in Vantagepoint, you can simply press ENTER.

      The script then queries for the service account running the Deltek Vantagepoint Process Server and prompts for the password. The script retrieves the server name of the process server by first checking to see if a server is pinned to the Refresh Queue, next by checking to see if a process server name has been entered in the Application Server section of Process Server configuration and last by defaulting to the Vantagepoint web server where setup is being run. If you receive errors, check the Process Server and Refresh Queue configuration in Vantagepoint (**Settings » General » Process Servers**).

9.  Enter the password for the Process Server service account and then press ENTER.

      The script verifies that the username and password are valid and that the account is a domain account. The script prompts you for the language to use to build the Analysis Cubes.

10. Enter the language code (for example. fr-CA) and press ENTER. If the language is English (United States), you can press ENTER, as it is the default language in brackets.

      The script checks for the following:

       a.  Checks cube hierarchies to ensure that they do not exceed 100 characters in length. System Labels are combined to create the hierarchies. If any hierarchies exceed 100 characters the script identifies them and exits. You must modify the system labels to reduce these hierarchies to fewer than 100 characters.

       b.  Checks the database to identify if Multiple Currency is enabled and alerts the user to ensure that the schedule the currency exchange process server job.

       c.  Checks the database for unsupported characters and long labels (95 characters and more) in User Defined Fields that have been marked as Available for Cubes. If any are found the UDF is marked as not available for cubes and alerts the user running the script that they will need to be fixed.

       d.  Checks for reserved words that create label conflicts when building the analysis cube. If these conflicts are detected, setup identifies the conflict and exits. You then need to modify the conflicting system label and re-run setup. Two checks are performed:
                  
      - To see if any of the "Org" System Labels (1-5) are using the reserved word "Market"
                  
      - To see if the WBS1 System Label is using the reserved word "Contract"

       e.  Checks the database for duplicate system labels. If any are found they are reported to the user running the script. Duplicate system labels may cause errors when creating the Analysis Services database. Duplicate system labels should be reviewed and/or changed before continuing setup.

      At this point the script has all of the information needed to deploy Analysis Cubes and displays a prompt that asks if you want to continue.

11. Verify the information entered and press ENTER to proceed.

12. If Analysis Cubes have already been deployed for the database/language combination entered, you are prompted with a list of choices.

      - 1. Apply System Label updates.

      - 2. Apply Cube Configuration updates.

      - 3. Rebuild Analysis Cubes.

      - 4. Full (Data) Refresh of Analysis Cubes.

      - 5. Exit setup

13. Enter the number of the option you want and press ENTER. Refer to the section [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md) for more information on these options.

#### Setup Steps Performed by the Script

After you enter all of the information needed to run **DeltekVantagepoint.ps1 -** IntelligenceSetupOption **SetupCube**, the script completes the following steps:

1.  The script creates the Vantagepoint data warehouse database, &lt;databasename&gt;DW on the Primary Node of the Availability Group, deleting it first if it already exists.

2.  The script modifies the Integration Services packages for use with your data source. The files are modified on the Vantagepoint web server and then copied, via PowerShell Remoting, to the Primary Node of the Availability Group. PowerShell Remoting must be enabled (see *Firewall Rules for PowerShell Remoting* in the *Deltek Vantagepoint Installation and Maintenance Guide* for more information). If the drive where Vantagepoint is installed on the web server does not exist on the database server, setup will create the Deltek Vantagepoint directory structure and copy the Integration Services Package files to the drive represented by the Program Files environment variable (in PowerShell this is represented by \$env:ProgramFiles.

3.  The script configures the Integration Services packages to use the Availability Group Listener name in the configuration so that in the event of a Failover, the SQL Agent Refresh Job will continue to run with no additional configuration needed.

4.  The script updates the Analysis Cube-specific tables and stored procedures in the Vantagepoint database. The script also checks to ensure that there are no unsupported characters and long labels (95 characters and more) in the User Defined Fields (UDF's) that are marked as Available for Cubes. If any are found, the script identifies these, marks them as unavailable for cubes, and notifies the user running the PowerShell script that these UDF's will need to be fixed.

5.  The script creates the Full and incremental SQL Agent Refresh Jobs on the Primary Node of the Availability Group and grants the necessary SQL Server permissions to execute these jobs. Part of this process includes assigning the necessary permissions and creating a SQL Server Credential for the Process Server service account and a SQL Agent Proxy which is assigned as the owner of the Refresh jobs. This ensures the minimum rights for the Process Server service account to execute the SQL Agent jobs.

6.  The script creates the Vantagepoint Analysis Cube database on the Primary Node of the Availability Group (including the specific system labels from your database), Deltek Vantagepoint Analysis - &lt;databasename&gt;\_&lt;anguage&gt;, deleting it first if it already exists. User accounts that have been added to the User Accounts Role in the cube are backed up and will be restored when the Analysis Cube database is rebuilt.

7.  If there are errors creating the Analysis Cube database, review the error log identified in the error message and contact Deltek Support if you need assistance.

8.  The script creates an administrator (Service Accounts Role) and user (User Accounts Role) roles in the Analysis Cube database and adds the SQL Agent Service account, Process Server service account and the IIS Application Pool Identity to the Service Accounts role. The script restores any user accounts that were backed up in Step 7.

9.  After setup completes, add any users to the User Accounts Role that need direct access to the cube via Excel for creating Pivot Table reports.

10. The script starts the Full Refresh SQL Agent job, which populates the data warehouse and Analysis Cube with data from your Vantagepoint database. The Full Refresh may take a while to complete, depending on the size of your database. The job status updates every 30 seconds.

11. If there are errors in the refresh job, you can view the log:

      a.  Open SQL Server Management and connect to your SQL Server instance.

      b.  Expand **(+) SQL Server Agent**.

      c.  Expand **(+) Jobs**.

      d.  Right-click the **Full Refresh &lt;database&gt;\_&lt;language&gt; DW and Cubes** job and select **Properties**.

      e.  Select the **Steps** page, select Step 2, and then click the **Edit** button.

      f.  Click the **Advanced** page and then click the **View** button.

      If there is no log displayed: Close the **Property** pages, right-click the **Full Refresh** job again, select **View History**, locate the error identified with a red X, and then review the log message in the bottom pane. Contact Deltek Support if you need assistance.

12. When the Full Refresh job has completed, setup writes this log to the Vantagepoint\\logs\\&lt;date_timestamp&gt; folder.

13. If this is the first time that Analysis Cube setup has been run, the script saves the Analysis Cube configurations to the Vantagepoint database.

14. The script then applies User Defined Fields (marked for use in Analysis Cubes), User Defined Calculated Measures, Filters, and KPI's to the Analysis Cube database.

15. The script processes the Analysis Cube and checks the setup log for any errors.

### Procedure for a Silent Installation of Analysis Cubes

<div class="note note note_note"><strong>Note:</strong> Only a basic deployment of Analysis Cubes is supported with the SilentInstall switch. Deployments including Scalable, Clustered or Availability Groups require an interactive setup.</div>

<div class="word-special word-procedure Procedure">Follow the Silent Install instructions in the *Deltek Vantagepoint Installation and Maintenance Guide* to create the silent install XML file using the CreatePasswords.ps1 and the ResponseGenerator.ps1 files.</div>

#### Generate the password files

<div class="word-special word-procedure Procedure">To generate password files:</div>

1.  Open the Windows Powershell Console as an administrator.

2.  Make sure that your working directory is C:\\Program Files\\Deltek\\Vantagepoint\\Scripts.

3.  Run the CreatePwdFiles.ps1 script:

      **C:\\Program Files\\Deltek\\Vantagepoint\\Scripts&gt; .\\CreatePwdFiles.ps1**

      The script displays a prompt that asks if you are creating passwords for a silent installation of Analysis Cubes.

4.  Enter **Y** and then press ENTER.

5.  Enter and confirm these passwords:

      - **DSM User Download password**

      - **Weblink password**

      - **Vantagepoint application user password.** This is the password for a valid Vantagepoint application user account which is required to save cube configurations. This user and password must be for Vantagepoint application user (**not** a Windows Integrated user account)

      - **Process Server Service Domain Account password**

      The script uses these passwords to create the following .txt password files:

      - sdownload.txt

      - weblink.txt

      - appuser.txt

      - procSvcDomainAcct.txt

6.  Verify that the four generated password files are in the folder C:\\Program Files\\Deltek\\Vantagepoint\\Scripts.

#### Create the silent install XML file

You can create the response file in one of two ways:

- **Create the file manually**: Find the **cubesilentInstall.xml** file in C:\\Program Files\\Deltek\\Vantagepoint\\Scripts and use it as a guide to create the response file.

- **Use the ResponseGenerator.ps1 script to generate the file**: Follow the steps below.

<div class="word-special word-procedure Procedure">To generate the response file:</div>

1.  Open the Windows Powershell Console as an administrator.

2.  Make sure that your working directory is C:\\Program Files\\Deltek\\Vantagepoint\\Scripts.

3.  Run the ResponseGenerator.ps1 script**:**

      **C:\\Program Files\\Deltek\\Vantagepoint\\Scripts&gt; .\\ ResponseGenerator.ps1**

      The script displays a form with a **Switches** drop-down list.

4.  Select the **--SetupCube** switch from the drop-down list. (The drop-down list does not reference the IntelligenceSetupOption, just the sub-switches). The following fields are displayed:

      - **LoadReports**: Select **No** from the drop-down list.

      - **DSM Username / Password**: Enter your DSM username and click the **Password File** button to select the sDownload.txt password file created above.

      - **Vantagepoint Database Server**: Enter the name of the database server hosting your Vantagepoint database. Setup requires that the database server name entered be resolvable to the hostname or FQDN. If the database server hostname or FQDN is not resolvable based on the DNS configuration setup cannot continue.

      - **Weblink Password File**: Click the button and select the weblink.txt password file created above.

      - **Vantagepoint database name**: Enter the name of the Vantagepoint database for which you want to deploy Analysis Cubes. This database must exist in Weblink.

      - **Vantagepoint Application user name**: Enter the name of the Vantagepoint application user account that has rights to the Analysis Cubes application. This account must be a Vantagepoint user (not a Windows Integrated account).

      - **App User Password File**: Click the button and select the appuser.txt password file created above.

      - **Vantagepoint Process Server Name**: Enter the name of the server hosting the Vantagepoint process server service.

      - **Process Server Password File**: Click the button and select the procSvcDomainAcct.txt password file created above.

      - **Analysis Cube Language**: Select the language for which you want to deploy Analysis Cubes.

      - **Cube Rebuild Option**: If Analysis Cubes was previously been deployed for your database and the language chosen, a rebuild option is required. If this is a new deployment leave this option blank. The available options are:

        - **Option 1**: Apply System/Org label updates for Labels/Org names that have been changed in Vantagepoint.

        - **Option 2**: Apply Cube Configuration updates for cube configurations (KPI's, user defined fields, and Calculated Measures) that have been modified.

        - **Option 3**: Rebuild Analysis Cubes.

        - **Option 4**: Full Refresh of Analysis Cubes.

        For more information on these options, see [Chapter 4: Ongoing Analysis Cubes Configuration Maintenance](Chapter_4_Ongoing_Analysis_Cubes_Configuration_Maintenance.md).

5.  After you confirm that the information entered is correct, click the **File** menu, choose **Save**, and then enter a name for the silent install xml file.

<div class="word-special word-procedure Procedure">To run cube setup with the Silent Install switch:</div>

1.  From the installation directory Scripts folder, enter .\\DeltekVantagepoint.ps1 --SilentInstall &lt;path_to_silent_install.xml&gt;.

2.  The installer should run without asking for any prompts.

3.  When then installer runs, it creates a log file, which is stored in C:\\Program Files\\Deltek\\Vantagepoint\\Logs.

#### Setup Steps Performed by the Script

The same configuration steps outlined in the Basic Deployment of the SetupCube sub-switch are performed with the SilentInstall switch, minus any prompts.


### Procedure for Performing Multiple Silent Analysis Cube Updates

Assume that you have deployed Analysis Cubes for multiple databases (Production/Staging/Test) or you have deployed Analysis Cubes for a single database but with multiple languages. In addition, assume that you need to be able to perform a maintenance action on each of these Analysis Cube databases (ReApply System Labels, ReApply Cube Configuration or Rebuild Cubes).

There are two ways of performing multiple silent installation actions:

- Use the standard silent installation logic to create a multi-step response file.

- Use the DeltekVantagepointSilentInstallWrapper.ps1 script.

#### Option 1

To perform option 1, you would repeat step 4 to create additional "steps." For information, see *Generate the ResponseFile* section of the *Deltek Vantagepoint Installation and Maintenance Guide*.

The standard --SilentInstall switch used in conjunction with a response.xml file works well for a single Silent Installation action, or even multiple actions assuming there are no errors. In the event of an error, say in Step 2 of a multi-step response file, the installation stops and does not continue processing additional steps.

#### Option 2

To perform option 2, follow the steps below.

The DeltekVantagepointSilentInstallWrapper script is designed to execute multiple individual response files, one after the other, regardless of errors occurring in an individual step.

Script components:

1.  **DeltekVantagepointSilentInstallWrapper.ps1**: You execute this script instead of DeltekVantagepoint.ps1. This script calls the standard .\\DeltekVantagepoint.ps1 --SilentInstall switch for each response file listed in the SilentInstalls.txt file (below).

2.  **SilentInstalls.txt**: This file will have the names of each response file that will be executed (one response file on each line). For example, if you wanted to perform actions on 3 Analysis Cube databases, the contents of this file would include the name of 3 &lt;response&gt;.xml files, 1 for each action.

3.  **Individual response XML files**: These are the XML files (created by the ResponseGenerator.ps1 or manually), that describe the silent install action to perform and are listed in the SilentInstalls.txt file (above).

      Deltek recommends that you name the &lt;response&gt;.xml files in a way that includes the name of the database and the language (if applicable), as the wrapper script will output the success/failure of each action/response file.

4.  Additional silent installation files such as the password files, that are necessary to perform the silent installation. For more information, see *Silent Installation* in the *Deltek Vantagepoint Installation and Maintenance Guide*.

<div class="word-special word-procedure Procedure">To use the DeltekVantagepointSilentInstallWrapper.ps1 script:</div>

1.  Identify the silent installation actions that you want to complete.

2.  Using the ResponseGenerator.ps1 file, create the individual response files for the SetupCube sub-switch.

      It is important that the initial step number for each response file be **1** and the nextStep be **0**.

      In addition, it is important to identify the specific **cubeRebuild** option in the **ResponseGenerator Analysis Cube Rebuild Option** drop-down list, as follows:

       - For a new deployment, leave it blank.

       - For ReApply System Labels, select Option 1.

       - For ReApply Cube Configurations, select Option 2.

       - For Rebuild Analysis Cubes, select Option 3.

<div class="note note note_note"><strong>Note:</strong> You can use the ResponseGenerator to create the first &lt;response&gt;.xml and then copy and paste the information (changing the parameters appropriately) into the additional &lt;response&gt;.xml files.</div>

3.  After you fill in all fields for the SetupCube sub-switch, including creating any required password files, save the file with a name that will be recognizable, in a format like this:

      CubeSI\_&lt;databasename&gt;\_&lt;language&gt;.xml)

4.  In Notepad, enter the name of each response file with a carriage return after each one, and then save the file as **SilentInstalls.txt**.

      The contents of the file should be similar to the following:

       - CubeSI_VantagepointDemo40_en-US.xml

       - CubeSI_VantagepointDemo40_fr-CA.xml

       - CubeSI_VantagepointDemo40Test_en-US.xml

5.  Place the SilentInstalls.txt file and the response xml files in the \\Vantagepoint\\scripts directory.

<div class="word-special word-procedure Procedure">To execute the script:</div>

1.  Open a new PowerShell console window using **Run as Administrator**.

2.  Change the directory to your Vantagepoint\\scripts folder.

3.  Enter .\\DeltekVantagepointSilentInstallWrapper.ps1 and press ENTER to execute the script.


### Procedure for Uninstalling Analysis Cubes

<div class="word-special word-procedure Procedure">To run the script with the UninstallCube sub-switch:</div>

1.  From the installation directory Scripts folder, enter .\\**DeltekVantagepoint.ps1 -** IntelligenceSetupOption **UninstallCube**.

2.  When prompted, enter your Deltek Support username and password.

      The script checks prerequisites, verifies your access to Deltek Vantagepoint, checks your IIS prerequisites, and then prompts you for the name of your Vantagepoint database server in the form (Server\\Instance). The default value in brackets will be the database server entered during Vantagepoint setup, which is pulled from the DeltekVantagepointSettings.xml file.

3.  Either accept the default value or enter the name of your Vantagepoint database server and press ENTER.

      The script prompts for the password for Weblink, in order to provide a list of valid databases for which cubes can be uninstalled and then prompts you for the name of the database. The database must exist in Weblink and must be configured for the same database server entered in Step 6 above.

4.  Enter the name of the Vantagepoint database and press ENTER. If there is only one database it will be displayed in brackets as the default and you can press ENTER.

      The script identifies the available languages that have been previously deployed and prompts you for the language of the cubes to uninstall.

5.  Enter the language code (for example. fr-CA) and press ENTER. If the language is English (United States), you can press ENTER, as it is the default language in brackets.

      At this point the script has all of the information needed to perform the uninstall and proceeds to remove all deployed components for that language.

#### Setup Steps Performed by the Script

After you enter all of the information needed to run **DeltekVantagepoint.ps1 -** IntelligenceSetupOption **UninstallCube**, the script completes the following steps:

1.  The script removes cube related files for your database/language combination from the Vantagepoint web server.

2.  If your database server is separate from the web server, the script removes cube-related files for your database/language combination from the Vantagepoint database server.

3.  The script deletes the data warehouse database for the language specified.

4.  The script deletes the Analysis Cubes database for the language specified.

5.  The script deletes the Full SQL Agent cube refresh job for the language specified.

## Validate that the Analysis Cubes Configuration Completed Successfully

In SQL Server Management Studio, connect to your Database Engine and Analysis Services to confirm that:

- The Vantagepoint data warehouse, analysis cubes, and SQL Server agent jobs to refresh them are created.

- The Vantagepoint data warehouse and analysis cubes are populated correctly.

- The Vantagepoint analysis cubes are processed as part of the execution of the SQL Server agent job.

### Connect to Your Database Engine and Analysis Services

<div class="word-special word-procedure Procedure">To connect to your Database Engine and Analysis Services, complete the following steps:</div>

1.  Click Windows **Start » All Programs » Microsoft SQL Server &lt;version&gt; » SQL Server Management Studio**.

2.  On the Connect to Server dialog box, complete the following steps:

      a.  In the **Server Type** field, select **Database Engine**.

      b.  In the **Server Name** field, select your SQL server (and instance, if applicable).

      c.  Connect using a Windows account that is a member of the SQL Server sysadmin role.

3.  Click the **Connect** button.

4. In the **Connect** drop-down list on the Object Explorer window, select **Analysis Service**.

5. Select your Analysis Server as the server name.


### Confirm that the Vantagepoint Data Warehouse, Analysis Cubes, and SQL Server Agent Job Are Created

After you connect to your Database Engine and Analysis Services, confirm that the following items are added:

- The Vantagepoint data warehouse displays in the Databases folder of the SQL Server Database Engine in Microsoft SQL Server Management Studio.
  
  The name of the data warehouse is your Vantagepoint database name with DW appended to it. For example, if your Vantagepoint database is named "Vantagepoint" you should not also see a database named "VantagepointDW". For a language other than en-US, the language code will appended as well.

- The **Full Refresh &lt;*Vantagepoint database name*&gt;\_&lt;*language*&gt; and Cubes** job displays in the SQL Server Agent Jobs folder.

- The **Incremental Refresh &lt;*Vantagepoint database name*&gt;\_&lt;*language*&gt; and Cubes** job displays in the SQL Server Agent Jobs folder.

- **Deltek Vantagepoint Analysis - &lt;Vantagepoint database name&gt;** displays in the Databases folder of Analysis Services. For a language other than en-US, the language code will appended as well.

### Validate that the Vantagepoint Analysis Cubes Are Populated

<div class="word-special word-procedure Procedure">To confirm that the Vantagepoint analysis cubes are populated:</div>

1.  In the SQL Server Agent Jobs folder, right-click the **Full Refresh &lt;*Vantagepoint database name*&gt;\_&lt;*language*&gt; DW and Cubes** job, and select **View History** from the shortcut menu.

2.  On the Log File Viewer dialog box, you see a green check mark <img src="images/image1_20251205072652_828db7.gif" alt="Green Check Mark.gif" width="7px" height="6px" /> beside the date of the **&lt;*Vantagepoint database name*&gt;\_&lt;*language*&gt; DW and Cubes** job if the Vantagepoint data warehouse and data cubes were populated correctly.

      If you see a red <img src="images/image2_20251205072652_b428fc.gif" alt="Red X.gif" width="12px" height="11px" /> instead of a green check mark, refer to the troubleshooting information in [Chapter 3: Troubleshoot Analysis Cubes Deployments](Chapter_3_Troubleshoot_Analysis_Cubes_Deployments.md).

<div class="note note note_note"><strong>Note:</strong> The default schedule for the SQL agent job is to run every night at 12:00 am. If this schedule interferes with your nightly backup or any other processing or SQL agent scheduled jobs, you can modify the job to run at an appropriate time for your environment. Since this job is initiated via the Process Server, you need to modify the job in the Vantagepoint Refresh Queue or in the Refresh tab of <strong>Settings » General » Analysis Cubes</strong>.</div>

### Validate that the Vantagepoint Analysis Cubes are Processed

**To validate that the Vantagepoint Project and General Ledger cubes are processed:**

1.  In SQL Server Management Studio, connect to Analysis Services.

2.  In the Object Explorer pane, in Analysis Services, navigate to **Databases » Deltek Vantagepoint Analysis - &lt;Your Vantagepoint Database Name*&gt; » Cubes**.

3.  In the Cubes folder, right-click the Project OLAP Cube folder and select **Properties** from the shortcut menu.

      In the **Status** section of the Cube Properties -- Project Cube dialog box, the **State** displays **Processed**. The cubes must be in a processed state for you to be able to connect to them.

4.  If the state displays **Unprocessed**, connect to the SQL Database Engine using SQL Management Studio and manually run the SQL Agent job. To do this, right-click the job and click **Start Job at Step**.

5.  On the Start Job on dialog box, select step ID 1 and click **Start**.

6.  When the process completes successfully, click **Close** on the Start Jobs dialog box.

A successful completion of the SQL Agent refresh job automatically processes both the Project and General Ledger data cubes.

### Validate that the Vantagepoint Cubes can be Browsed

**To validate that the Vantagepoint Project and General Ledger cubes can be browsed:**

1.  In SQL Server Management Studio, connect to Analysis Services.

2.  In the Object Explorer pane, in Analysis Services, navigate to **Databases » Deltek Vantagepoint Analysis - &lt;*Your Vantagepoint Database Name*&gt; » Cubes**.

3.  In the Cubes folder, right-click the Project OLAP Cube folder and select **Browse** from the shortcut menu.

4.  Under the Project OLAP Cubes, expand Measures and then Revenue -Project.

5.  Drag the Revenue measure to the grid on the bottom right.

      If the cube is populated with data, the balance of the Revenue measure is displayed.

6.  To see the Revenue by Project Organization, expand the Projects dimension and drag the Projects by Organization hierarchy to the right.

      You should now see the Revenue measure by Project Organization.

Completing these steps shows that the cube has been browsed successfully and that the cube has been populated with data.

## Add Your Domain Users to the User Accounts Analysis Cube Role

The PowerShell deployment of the Analysis Cube automatically creates two roles in the Analysis database, a Service Accounts Role and a User Accounts Role. Make sure to add your domain users that need access to the Analysis Cubes to the User Accounts role. When the Analysis Cubes are rebuilt at any time in the future the script will backup and restore user accounts that currently exist in the User Account Role.

<div class="note note note_note"><strong>Note:</strong> Your network administrator may find it easier to create a domain group that contains all the necessary domain users. If this is the case, you can add the domain group instead of each individual domain user.</div>

## Script the Role

After you add your users to the User Accounts role, it is recommended that you script the creation of the role so that it can be easily recreated if needed.

This will be useful to have when you upgrade to a new version of Vantagepoint and need to rebuild the Vantagepoint data warehouse and data cubes. When you rebuild them, any Analysis Service roles that you previously created for them are lost.

<div class="word-special word-procedure Procedure">To save a role to a script:</div>

1.  Use SQL Server Management Studio to connect to Analysis Services.

2.  In Object Explorer, navigate to **Databases » Deltek Vantagepoint Analysis - &lt;Vantagepoint database name&gt; » Roles**.

3.  Right-click the role that you created for Analysis Cubes, and click **Script Role as** » **CREATE To** **»** **File** on the shortcut menu.

4.  On the Select a file dialog box, save the file as an .XMLA file type to a location on your server.

      An XMLA file is the Analysis Services equivalent of a database .sql script.

5.  When you rebuild the cubes and need to recreate the role, open the .XMLA file while you are connected to Analysis Services and execute it against your Analysis Services database.

      This script recreates the role name and the domain users that are members of the role. You then must reassign access to the data source and the data cubes as in steps 6 and 7 in the \"Create an Analysis Services Role for Your Domain Users\" section of this document.

You have completed the Analysis Cubes configuration.

<div class="note note note_note"><strong>Note:</strong> If you want users to be able to access Vantagepoint OLAP cubes via the Internet (from outside a corporate firewall), you must perform the additional configuration steps that are provided in <a href="Chapter_5_Configure_Analysis_Cubes_for_Internet_Accessibility.md">Chapter 5: Configure Analysis Cubes for Internet Accessibility</a>.</div>

## Multiple Vantagepoint Databases

The Vantagepoint PowerShell deployment scripts support deploying cubes for multiple Vantagepoint databases; you may have development, test, and production copies of the database for which you need to deploy cubes. After you complete the Analysis Cubes configuration instructions to create data cubes for one Vantagepoint database, repeat all the configuration steps to create data cubes for each database.

## Multiple Languages

### Vantagepoint Analysis Cubes for Each Language

To create custom reports in different languages, you must create separate Vantagepoint Analysis cubes for each language. To create analysis cubes for a different language, you repeat the configuration steps in this guide choosing the same Vantagepoint database but changing the language selection during the deployment script prompt as appropriate. When you create a custom report in Excel, you connect to the analysis cube with the desired language. For example, an English International analysis database that contains the data cubes has \_en-GB at the end of its name.

### Creating Custom Reports in Excel Against the Different Language Data Cubes

Users who connect to the language data cubes in Excel must perform an additional step so that the Vantagepoint dimensions, measures, and system labels display in the correct language in the Excel PivotTable Field List. For this additional step, users must add a locale identifier code to the connection string in the .odc file for each of the Project and General Ledger data cubes. You complete this step one time for each data cube. The steps are included here and in the Analysis Cubes section of the Vantagepoint Help system. The locale identifiers for currently supported languages are listed in the following table:

| Language for the data cubes | Locale Identifier \# |
|-----------------------------|----------------------|
| English (United States)     | Not required         |
| English (International)     | 2057                 |
| French (Canada)             | 3084                 |
| French (France)             | 1036                 |
| Spanish (International)     | 3082                 |
| Dutch (Netherlands)         | 1043                 |
| German (Germany)            | 1031                 |
| Portuguese (Brazil)         | 1046                 |

<div class="word-special word-procedure Procedure">To add a locale identifier code to the connection string in a data cube .odc file:</div>

1.  In Excel, connect to the Vantagepoint Project or General Ledger data cube. An .odc connection is creating during the connection process.

2.  Use Windows Explorer to navigate to the following location:

      c:\\Users\\&lt;username&gt;\\My Documents\\My Data Sources

3.  In the My Data Sources folder, right-click the .odc file for the connect to the language data cube.

      The file has the following naming convention:

      - Project data cube: &lt;*your Vantagepoint server name*&gt; Deltek Vantagepoint Analysis &lt;*your Vantagepoint database name*&gt;\_&lt;language&gt; Project OLAP Cube.odc

      - General Ledger data cube: &lt;*your Vantagepoint server name*&gt; Deltek Vantagepoint Analysis &lt;*your Vantagepoint database name*&gt;\_&lt;language&gt; General Ledger OLAP Cube.odc

        You must have Excel open and connected to a *Vantagepoint* language data cube for this file to display in Windows Explorer.

4.  On the shortcut menu, click **Edit in Notepad**.

5.  In Notepad, find the following odc: ConnectionString line:

      &lt;odc:ConnectionString&gt;Provider=MSOLAP.4;Integrated Security=SSPI;Persist Security Info=True;Data Source=&lt;Analysis Server&gt;;Initial Catalog=Deltek Vantagepoint Analysis - &lt;*your database name*&gt;\_&lt;language&gt; &lt;/odc:ConnectionString&gt;

6.  Add **;Locale Identifier=&lt;locale identifier #&gt;** to the end of the string as follows:

      &lt;odc:ConnectionString&gt;Provider=MSOLAP.4;Integrated Security=SSPI;Persist Security Info=True;Data Source=&lt;Analysis Server&gt;;Initial Catalog=Deltek Vantagepoint Analysis - &lt;your database name&gt;\_&lt;language&gt;**;Locale Identifier=&lt;locale identifier #&gt;**&lt;/odc:ConnectionString&gt;

      Note the semicolon before Locale Identifier=&lt;locale identifier #&lt;.

7.  Save and close the file in Notepad.

In Excel, the *Vantagepoint* dimensions, measures, and system labels in the PivotTable Field list for the data cube now display in the appropriate language.

### Producing Reports in Both the English United States and English International Languages

If you want to produce the same report in both the English United States language and another language, you can use one .xlsx Excel report file for both reports. However, if you have Vantagepoint system labels that are different for each language, any dimensions added to the report design that have a system label in their name are removed from the .xlsx file when you are connected to the other language and open the report. In that scenario, you need to add the missing dimension back to the report, or you could create a separate .xlsx Excel report file for each language.

An example of different systems labels is having "job" set up as the system label for WBS (work breakdown structure) 1 in the English United States Vantagepoint database and "project" set up as the system label for WBS 1 in the language Vantagepoint database.