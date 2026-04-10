# Chapter 4: Ongoing Analysis Cubes Configuration Maintenance (oops)

After you initially configure Analysis Cubes, whenever you make changes to Vantagepoint System or Organization labels or to Analysis Cubes configuration settings in Vantagepoint, you must repeat some Analysis Cubes configuration steps.

In addition, you may need to completely rebuild the cubes with each new Vantagepoint release if that release includes changes that impact Analysis Cubes, such as new database fields that are specific to Analysis Cube, updates to the Integration Services packages, or other Analysis Cubes-specific changes. The PowerShell deployment script --Upgrade switch will notify you what changes are needed for the Analysis Cubes (if any) and always review the Release Notes for information on whether a cube-related change or fix means that you must rebuild the cubes.

The following options are available via the PowerShell SetupCube sub-switch:

- Re-Apply System/Organization labels

- Re-Apply Analysis Cube configurations

- Rebuild Analysis Cubes

- Refresh Analysis Cube data (Full Refresh)

- Rebuild Integration Services packages

## System and Organization Label Changes in Vantagepoint

Whenever you make a System label change in Vantagepoint (**Settings » Labels and Lists » Labels**), or you make changes to your Organization labels (**Settings » Organization » Codes** in the desktop application), you must update the data cubes for the label changes to be reflected in the Analysis Cubes.

<div class="word-special word-procedure Procedure">To re-apply system and organization labels to the data cubes using the PowerShell deployment scripts, complete the following steps:</div>

1.  Follow the steps outlined in the [Procedure for Basic Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_basic_deployment).

2. When you get to Step 11 and you are prompted by setup that the cubes have already been deployed, enter 1 as the choice to Apply System Label updates.

<div class="note attention note_attention"><strong>Attention:</strong> If there are any errors applying System Labels you will receive a message indicating this and that you can obtain the detailed error message by reviewing the VantagepointCubesCreate\&lt;timestamp&gt;.log in the Vantagepoint Logs directory.

Any errors are likely due to duplicate system labels in Vantagepoint. You can review the Labels tab of Labels and Lists and the Organization names setup for duplicates. You will be unable to utilize the Analysis Cubes until these issues are resolved. Contact Deltek Support if you need assistance.</div>

## Analysis Cubes Configuration Changes in Vantagepoint

Anytime that you make changes to the following Analysis Cubes configuration in Vantagepoint, complete the PowerShell script steps below to update the Analysis Cubes:

<table style="width:99%;">
<colgroup>
<col style="width: 32%" />
<col style="width: 67%" />
</colgroup>
<thead>
<tr>
<th>Path in Vantagepoint</th>
<th>Configuration Step</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Settings » General » Analysis Cubes</strong></td>
<td><p>Complete Analysis Cubes configuration as needed:</p>
<ul>
<li><p>Select or hide the dimensions and measures to populate the data cubes.</p></li>
<li><p>Create, delete or modify key performance indicators (KPIs).</p></li>
<li><p>Create calculated measures.</p></li>
<li><p>Set up currency exchange information.</p></li>
</ul>
<p>See the Vantagepoint online Help for more information.</p></td>
</tr>
<tr>
<td><p>Screen Designer form in Vantagepoint:</p>
<ul>
<li><p>In a hub, select <strong>Design</strong> in <strong>Other Actions</strong> or</p></li>
<li><p><strong>Settings » General » Screen Designer</strong></p></li>
</ul>
<p><strong>Settings » General » User Defined Components</strong> in the desktop application</p></td>
<td><p>Create or select user defined fields to populate the data cubes.</p>
<p>See the Vantagepoint online Help for more information and instructions.</p>
<p>Note the following character limits for user defined field labels:</p>
<ul>
<li><p>Less than 95 characters in length.</p></li>
<li><p>No invalid characters.</p></li>
</ul>
<p>During the installation process, a message shows user defined fields that do not pass these requirements.</p></td>
</tr>
</tbody>
</table>

<div class="word-special word-procedure Procedure">To update the data cubes with the current Analysis Cubes configuration entered or updated in Vantagepoint:</div>

1.  Follow the steps outlined in the [Procedure for Basic Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_basic_deployment).

2.  When you get to Step 11 and you are prompted by setup that the cubes have already been deployed, enter 2 as the choice to Apply Cube Configuration updates.

## Rebuild Analysis Cubes

Whenever there are changes that necessitate that the Vantagepoint Analysis Cubes be rebuilt, such as a Vantagepoint Maintenance Release or major releases (such as 4.0, 4.5, and so on) that includes Analysis Cube related updates, complete the following steps to re-deploy Analysis Cubes:

<div class="word-special word-procedure Procedure">To rebuild Analysis Cubes:</div>

1.  Follow the steps outlined in the [Procedure for Basic Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_basic_deployment).

2.  When you get to Step 11 and you are prompted by setup that the cubes have already been deployed, enter 3 as the choice to Rebuild Analysis Cubes.

## Update (Refresh) the Data Warehouse (DW) and the Vantagepoint Cubes

<div class="note attention note_attention"><strong>Attention:</strong> For a Full Refresh of the data, Deltek recommends that you complete this step after business hours when users are not connected to the <em>Vantagepoint</em> transactional database as information added to the database during the cube refresh process can result in data synchronization issues.</div>

The SQL Agent Refresh Job is configured to write a detailed output log to the MSDB database and will also automatically write this log to the Vantagepoint Logs folder. The name of the file will be in the following format: &lt;Database Name&gt; &lt;Culture&gt;\_Refresh.log.

If for some reason the detailed output log is not written to the Vantagepoint logs folder, you can view the log from the MSDB database using the following steps.

<div class="word-special word-procedure Procedure">To view the log:</div>

1.  Follow the steps outlined in the [Procedure for Basic Deployment](Chapter_2_Configure_Analysis_Cubes.html#procedure_for_basic_deployment).

2.  When you get to Step 11 and you are prompted by setup that the cubes have already been deployed, enter 4 as the choice to perform a Full data refresh.

## Rebuild Integration Services Packages

The Vantagepoint deployment script --Upgrade switch will detect and perform any required updates to the Integration Services Package files. Should an error occur in this step during the upgrade process, you can run the -- IntelligenceSetupOption UpdateCubeDtsxPackages switch, which will perform the same updates as those performed by the --Upgrade switch.