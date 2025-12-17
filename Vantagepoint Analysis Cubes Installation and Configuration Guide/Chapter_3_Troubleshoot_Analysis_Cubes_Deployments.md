# Chapter 3: Troubleshoot Analysis Cubes Deployments

You must meet many pre-requisites and complete many individual steps to deploy Analysis Cubes successfully. In addition, there may be data-related issues during the process because data is copied and transformed from your Vantagepoint database to the data warehouse and then to the Analysis Services database. Failures can occur during any of these steps.

The following table provides some basic troubleshooting information.

<table style="width:99%;">
<colgroup>
<col style="width: 39%" />
<col style="width: 60%" />
</colgroup>
<thead>
<tr>
<th>Step Where You Encounter Problems</th>
<th>What to Do</th>
</tr>
</thead>
<tbody>
<tr>
<td>Pre-requisites</td>
<td>Review the pre-requisite requirements at the beginning of this guide.</td>
</tr>
<tr>
<td>Creation of data warehouse, refresh jobs or Analysis cubes database.</td>
<td><p>Ensure that you have upgraded the Vantagepoint database tier installation to the current version.</p>
<p>Review the VantagepointCubes_&lt;timestamp&gt;.txt file in the \Vantagepoint\Logs folder. If you need to open a Customer Care incident, be sure to include all logs in the \Vantagepoint\Logs\&lt;timestamp&gt; folder for the time of the script execution.</p></td>
</tr>
<tr>
<td>Apply System Labels to Cubes</td>
<td>Review the VantagepointCubesCreate_&lt;timestamp&gt;.txt file in<br />
the Vantagepoint Logs folder for errors. If you need to open a Customer Care incident, be sure to include all logs in the \Vantagepoint\Logs\&lt;timestamp&gt; folder for the time of the script execution.</td>
</tr>
<tr>
<td>Populate DW and <em>Vantagepoint</em> Cubes</td>
<td><p>Review the SQL Server Agent job Refresh log for errors. That log is located in the Vantagepoint\Logs\&lt;timestamp&gt; directory and is in the format <strong>VantagepointCubesRefreshLog&lt;timestamp&gt;.txt.</strong></p>
<p>If you need to open a Customer Care incident, be sure to include all logs in the \Vantagepoint\Logs\&lt;timestamp&gt; folder for the time of the script execution.</p></td>
</tr>
<tr>
<td>Apply Cubes Configurations</td>
<td><p>Review the VantagepointCubes_&lt;timestamp&gt;.txt file in the \Vantagepoint \Logs folder.</p>
<p>If you need to open a Customer Care incident, be sure to include all logs in the \Vantagepoint\Logs\&lt;timestamp&gt; folder for the time of the script execution.</p></td>
</tr>
</tbody>
</table>

## Problems and Solutions

Solutions to known problems are listed below:

<table style="width:99%;">
<colgroup>
<col style="width: 36%" />
<col style="width: 63%" />
</colgroup>
<thead>
<tr>
<th>Problem</th>
<th>Solution</th>
</tr>
</thead>
<tbody>
<tr>
<td>The SQL Agent Refresh job <strong>View</strong> button (which allows you to view the refresh log) does not allow the ability to see logs from previous runs.</td>
<td><p>If you need (or want) to be able to see the logs from multiple refresh job runs, select the <strong>Append output to existing entry in table</strong> check box in the Advanced table of the SQL Agent job step properties dialog.</p>
<p>If you need to obtain the log for a currently running job, use the following PowerShell script in the Windows PowerShell ISE:</p>
<p><pre class="word-code code-level-0 CodeText1"><span style="color: #FF4500;">$RefreshAgentJobName</span> <span style="color: #A9A9A9;">=</span> "Full Refresh &lt;database&gt;_&lt;language&gt; DW and Cubes"</pre></p>
<p><pre class="word-code code-level-0 CodeText1"><span style="color: #FF4500;">$DBServer</span> <span style="color: #A9A9A9;">=</span> "&lt;database_server&gt;"</pre></p>
<p><pre class="word-code code-level-0 CodeText1">(<span style="color: #0000FF;">Invoke-Sqlcmd</span> <span style="color: #000080;">-Query</span> "EXEC dbo.sp_help_jobsteplog @job_name = N'<span style="color: #FF4500;">$RefreshAgentJobName</span>', @step_id='1';" <span style="color: #000080;">-ServerInstance</span> "<span style="color: #FF4500;">$DBServer</span>" <span style="color: #000080;">-Database</span> "msdb" <span style="color: #000080;">-MaxCharLength</span> <span style="color: #800080;">10000000</span>)<span style="color: #A9A9A9;">.</span>log</pre></p></td>
</tr>
<tr>
<td><p>You receive and error similar to the following:</p>
<p><em>“WinRM cannot process the request. The following error occurred while using Kerberos authentication: Cannot find the computer &lt;database_server&gt;”</em></p>
<p><em>“The WinRM client cannot process the request. Default authentication may be used with an IP address under the following conditions:”</em></p></td>
<td>Setup was unable to resolve the database server name entered to a hostname or FQDN which is required for setup to continue. Ensure that the correct DNS entries are in place for proper resolution. For example, a Host (A) record for exist for the FQDN of the server (hostname.company.com) and if using a Custom DNS alias, an Alias (CNAME) record should be created.</td>
</tr>
<tr>
<td>You receive the following error after entering the Vantagepoint username and password required to save cube configurations:<br />
<br />
<span style="color: #A31515;">"error"</span>: <span style="color: #0451A5;">"invalid_clientId"</span>, <span style="color: #A31515;">"error_description"</span>: <span style="color: #0451A5;">"Client_id or Client_secret is invalid."</span></td>
<td>Database entries in Weblink have been modified and IIS has not been restarted since the changes were made. Restart IIS and continue setup. This will be fixed in a future release of Vantagepoint.</td>
</tr>
</tbody>
</table>