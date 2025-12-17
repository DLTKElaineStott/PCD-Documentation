# Appendix A

## Steps to Create a Scalable Analysis Cubes Deployment

This Appendix outlines the specific details to scale out your Analysis Cubes deployment. Refer to the [Procedure for Scalable Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_scalable_deployment) for specific information on the deployment of Analysis Cubes in a scalable deployment.

The following terminology will be helpful in understanding how scalability is achieved.

| Abbreviation | Meaning | Description |
|--------------|----------------|-----------------------------------------|
| **Vantagepoint DB** | Vantagepoint Database Server | This server hosts the Vantagepoint transaction database. |
| **Vantagepoint DW** | Vantagepoint Data Warehouse | This is a secondary database hosted in SQL Server which houses intermediate data from your Vantagepoint database which has been processed and transformed by the SQL Integration Services Packages via an ETL process. |
| **Vantagepoint ETL** | Vantagepoint Business Logic Extract, Transform, and Load | Vantagepoint ETL business logic is run by a SQL Server Agent jobs. |
| **Vantagepoint Agent** | SQL Server Agent job | This job is scheduled to refresh the Data Warehouse and Analysis Services data nightly by default. |
| **Vantagepoint AS** | SQL Server Analysis Services | Project and General Ledger OLAP Cubes. |

The following configurations are supported:

<table style="width:97%;">
<colgroup>
<col style="width: 16%" />
<col style="width: 16%" />
<col style="width: 16%" />
<col style="width: 16%" />
<col style="width: 33%" />
</colgroup>
<thead>
<tr>
<th>Configuration</th>
<th><p>Server 1</p>
<p>(<em>Vantagepoint</em> DB Tier)</p></th>
<th><p>Server 2</p>
<p>(<em>Vantagepoint</em> DW Tier)</p></th>
<th><p>Server 3</p>
<p>(<em>Vantagepoint</em> AS Tier)</p></th>
<th>Notes</th>
</tr>
</thead>
<tbody>
<tr>
<td><p><strong>Option #1</strong></p>
<p><strong>Non-scalable Single Server installation</strong></p></td>
<td><p>Vantagepoint DB</p>
<p>Vantagepoint DW</p>
<p>Vantagepoint ETL</p>
<p>Vantagepoint Agent</p>
<p>Vantagepoint AS</p></td>
<td>N/A</td>
<td>N/A</td>
<td>This is the default configuration where all components exist on the same server. The steps for this configuration are covered in the <a href="Chapter_2_Configure_Analysis_Cubes.md#procedure_for_basic_deployment">Procedure for Basic Deployment</a> and are not applicable for a scalable configuration.</td>
</tr>
<tr>
<td><strong>Required Services</strong></td>
<td><p>SQL Server</p>
<p>SQL Server Agent</p>
<p>SQL Server Integration Services</p>
<p>SQL Server Analysis Services</p></td>
<td>N/A</td>
<td>N/A</td>
<td></td>
</tr>
<tr>
<td colspan="5"></td>
</tr>
<tr>
<td><p><strong>Option #2</strong></p>
<p><strong>Two-server scalable configuration</strong></p></td>
<td>Vantagepoint DB</td>
<td><p>Vantagepoint DW</p>
<p>Vantagepoint ETL</p>
<p>Vantagepoint Agent</p>
<p>Vantagepoint AS</p></td>
<td>N/A</td>
<td>In this configuration all components except the Vantagepoint transaction database are created on a second server.</td>
</tr>
<tr>
<td><strong>Required Services</strong></td>
<td>SQL Server</td>
<td><p>SQL Server</p>
<p>SQL Server Agent Service</p>
<p>SQL Server Integration Services</p>
<p>SQL Server Analysis Services</p></td>
<td>N/A</td>
<td></td>
</tr>
<tr>
<td colspan="5"></td>
</tr>
<tr>
<td><p><strong>Option #3</strong></p>
<p><strong>Three-server scalable configuration</strong></p></td>
<td>Vantagepoint DB</td>
<td><p>Vantagepoint DW</p>
<p>Vantagepoint ETL</p>
<p>Vantagepoint Agent</p></td>
<td>Vantagepoint AS</td>
<td>In this configuration the Vantagepoint AS is moved from the DW to a third server.</td>
</tr>
<tr>
<td><strong>Required Services</strong></td>
<td>SQL Server</td>
<td><p>SQL Server</p>
<p>SQL Server Agent</p>
<p>SQL Server Integration Services</p></td>
<td>SQL Server Analysis Services</td>
<td></td>
</tr>
</tbody>
</table>

## Prerequisites for Scalability

- Ensure that you have the necessary SQL Server licenses for your deployment. Refer to the Microsoft SQL Server licensing guidelines or talk to your licensing representative if you have any questions.

- The Edition of SQL Server of the DW/AS or AS servers can be different from that used for the Vantagepoint Transaction Database server. Note, however, that if you have the Enterprise Edition of SQL Server for your Transaction Database server and deploy Standard Edition for the DW/AS or AS server, you will not have access to the functionality limited by Standard Edition (refer to the section [Microsoft SQL Server Edition Dependencies](Chapter_1_Analysis_Cubes_Prerequisites_and_Installation.html#microsoft_sql_server_edition_dependencies) in this guide for more information). Conversely, if your Vantagepoint Transaction Database Server is Standard Edition and you deploy the Enterprise Edition as the DW/AS or AS server, you will gain this functionality.

- Ensure that you have the necessary and supported SQL Server services installed on the various servers depending on your deployment model (refer to the chart above for information). The deployment scripts will verify your configuration.

- Your SQL Server services must be configured to run with domain accounts.

- You must register Service Principal Names (SPNs) for the domain service account running the SQL Server service on the Data Warehouse server. To configure the SPN's, execute the following commands:

  - Setspn --A MSSQLSvc/&lt;machine_name&gt; &lt;domain&gt;\\&lt;svc account&gt;

  - Setspn --A MSSQLSvc/&lt;FQDN&gt; &lt;domain&gt;\\&lt;svc account&gt;

  - Setspn --A MSSQLSvc/&lt;FQDN&gt;:port &lt;domain&gt;\\&lt;svc account&gt;

- Domain delegation is required for a scalable deployment of Vantagepoint. You will need to configure constrained delegation for the Data Warehouse service account to the SQL Service running on the Vantagepoint database server.

<div class="word-special word-procedure Procedure">To create a scalable configuration, complete the following steps:</div>

1.  If you have any existing Vision Analysis Cubes deployments on your Vantagepoint database server, run the UninstallCube switch to remove them.

2.  Using the SQL Server installation media, install the appropriate SQL Server components on the Vantagepoint DW tier (and Vantagepoint AS tier if implemented a three-server scale out deployment) as indicated in the chart above. Be sure to apply the supported service packs and cumulative updates on all servers.

3.  Complete the deployment using the SetupCube sub-switch run from your Vantagepoint web server following the steps outlined in [Procedure for Scalable Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_scalable_deployment).