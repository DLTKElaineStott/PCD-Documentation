# Chapter 5: Configure Analysis Cubes for Internet Accessibility

If you want users to be able to access the Vantagepoint Analysis cubes via the Internet (from outside a corporate firewall), complete the configuration steps in this section. Using these 
instructions, you will configure Internet Information Services (IIS) to allow Microsoft Excel to access Analysis Services via HTTPS.

You must complete the instructions in the previous sections of this guide to configure Analysis Cubes before you complete the instructions in this section to configure Analysis Cubes for Internet 
accessibility.

## Install and Configure the OLAP Data Pump

You can perform the following steps on any server running IIS that has been configured to be allowed access from the Internet and configured to have access to the Analysis Services database. In 
many cases, this may be the Vantagepoint Web/application server.

<div class="note attention note_attention"><strong>Attention:</strong> Windows Integrated Authentication will not work with Excel if users are not authenticated to the domain. Therefore, for this 
to work for Internet users, the OLAP virtual directory must be configured for Basic Authentication. With Basic Authentication, users still enter their domain credentials, but they are passed in 
clear text. For this reason, you must secure the Web site that is hosting the OLAP virtual directory with an SSL certificate.</div>

The OLAP Data Pump component is loaded into IIS and serves as an ISAPI extension to pump data from the client to an Analysis Services server and back.

You must configure the OLAP Data Pump if you are configuring the Analysis Cubes to be accessed by Excel for Internet users.

The following steps guide you through installing and configuring the Microsoft OLAP Data Pump.

### Step 1: Get Binaries

Copy the contents of the %SSAS Installation folder%\\OLAP\\bin\\isapi folder on the Analysis Services server into the folder that you want to serve as the base for the virtual directory in IIS on 
the web server which will host the OLAP data pump. This web server may or may not be your Vantagepoint web server.

In this example, we copy all the files from the C:\\Program Files\\Microsoft SQL Server\\MSAS&lt;version&gt;.&lt;Instance Name&gt;\\OLAP\\bin\\isapi folder into the C:\\inetpub\\wwwroot\\olap 
folder. Note that your SQL path will be different, depending on the version of SQL Server and instance name.

The following guidelines apply:

- Because of IIS limitations, the path to your directory must not contain spaces.

- If you plan to run the HTTP pump on a different server than the Analysis Services server, you must also install OLEDB for Analysis Redistributable package for your version and platform of SQL 
Analysis Services on the Web server hosting the OLAP Data Pump. See the Configure Settings if Data Pump and SSAS Are on Different Servers section under [Step 6: Get it All Together](#step_6_get_it_all_together) for more information.

### Step 2: Create an Application Pool

<div class="word-special word-procedure Procedure">To create an application pool:</div>

1.  Click **Windows Administrative Tools » Internet Information Services** to open the IIS Manager.

2.  In the IIS console, expand the **Server Name** node.

3.  Right-click **Application Pools**, and click **Add Application Pool** on the shortcut menu.

4.  On the Add Application Pool dialog box, complete the following:

      - In the **Name** field, enter a name for the application pool. In this example, it is **olap**.

      - In the **.Net Framework version** field, select **.NET Framework v4.0.30319**.

      - In the **Managed pipeline mode** field, accept the default, **Integrated**.

### Step 3: Create a Virtual Directory

<div class="word-special word-procedure Procedure">To create a virtual directory:</div>

1.  In the Connections pane of the IIS console, expand **Sites**, and then expand **Default Web Site** (or the site name that you use).

2.  Right-click the Web site, and click Add Application on the shortcut menu.

3.  On the Add Applications dialog box, complete the following:

      - In the **Alias** field, enter the name for the virtual directory. In this example, it is **olap**.

      - The content directory in the **Physical path** field must point to the folder that you created.

4.  Click **OK** to create the application.

5.  In the Connections pane, click the **OLAP** virtual directory, and double-click **Handler Mappings** in the /OLAP Home pane.

6.  In the Actions pane on the right, click **Edit Feature Permissions.**

7.  On the Edit Feature Permissions dialog box, select the **Read** and **Script** permissions check boxes if they are not selected, and click **OK**.

8.  In the Actions pane, click **Add Script Map...**.

9.  On the Edit Script Map dialog box, complete the following, and then click **OK**.

      - In the **Request path** field, enter **\*.dll**.

      - In the **Executable** field, browse to and select the location of the msmdpump.dll file. For multiple instances, be sure to point to the appropriate physical instance folder.

      - In the **Name** field, enter **OLAP**.

10. On the Add Script Map dialog box, click **Yes** to add the ISAPI extension and save.

11. To see the ISAPI extension that was added, click the **Server Name** node in the IIS console, and double-click **ISAPI & CGI Restrictions**.

12. In the ISAPI and CGI Restrictions pane, double-click **OLAP Data Pump**.

### Step 4: Configure Security for Excel Access over the Internet

Before you begin the following procedure to configure security for Excel access over the internet, enable Basic Authentication in your IIS configuration if it is not currently enabled. To do so, 
go to **Server Manager » Roles » Web Server (IIS) » Add Role Services**.

<div class="word-special word-procedure Procedure">To configure security for Excel access over the Internet:</div>

1.  In the Connections pane of IIS Manager, select the **OLAP** virtual directory.

2.  In the /OLAP Home pane, double-click **Authentication**.

3.  In the Authentication pane, select **Basic Authentication**.

4.  In the Actions pane, click **Enable** and then click **Edit**.

5.  On the Edit Basic Authentication Settings dialog box, configure the default domain:

6.  If it is appropriate, disable Anonymous Authentication and Windows Authentication.

### Step 5: Select the Target Analysis Services Server

Every instance of the OLAP Data Pump uses its own configuration file.

Open the msmdpump.ini file in your folder. The contents of the file should look like the following:

```
<ConfigurationSettings>
<ServerName>localhost</ServerName>
<SessionTimeout>3600</SessionTimeout>
<ConnectionPoolSize>100</ConnectionPoolSize>
</ConfigurationSettings>
```
The only setting of interest, at this point, is **&lt;ServerName&gt;**. If the Analysis Services instance to which you must provide access is located on the local machine and installed as a 
default instance, you have no reason to change this setting. If it is not located and installed in this way, you must specify the machine name and instance name (mymachine\\inst1).

The key setting at this point is **&lt;ServerName&gt;**. If the Analysis Services instance to which you must provide access is located on the local machine and installed as a default instance, do 
not change this setting. Otherwise, you must specify the machine name and instance name:

`<ServerName>mymachine\inst1</ServerName>`

### Step 6: Get it All Together

At this point, your HTTP pump is configured, and you can connect from your application.

If your application provides a way to specify the server name, replace your server name with the path to your virtual directory concatenated with **msmdpump.dll**. For example:

`https://<IISServerFQDN>/<olapVirtual>/msmdpump.dll`

After you install the Microsoft OLAP Data Pump, complete the following tasks:

- Configure settings, if the data pump and OLAP are not on the same server.

- Test the data pump configuration.

#### Configure Settings if Data Pump and SSAS Are on Different Servers

If the data pump and SSAS are not on the same server, install the following prerequisite software components:

- Download and install the Analysis Services OLE DB drivers:

  <https://docs.microsoft.com/en-us/analysis-services/client-libraries?view=asallproducts-allversions>

  Make sure to choose the MSOALP (AMD64) link.

- Modify the **ServerName** setting in the msmdpump.ini file to point to the location of the SSAS server. Change **localhost** to the name of your SSAS server, including the instance name if 
applicable:

  ```
  <ConfigurationSettings>
  <ServerName>localhost</ServerName>
  <SessionTimeout>3600</SessionTimeout>
  <ConnectionPoolSize>100</ConnectionPoolSize>
  </ConfigurationSettings>
  ```
#### Test the Data Pump Configuration

Complete the following actions to test the data pump configuration:

- Test the URL.

- Test the connection using SQL Management Studio.

- Test the connection using Excel.

##### Test the URL

**To test the URL to ensure that the data pump is set up correctly, complete the following steps:**

1.  Access the data pump URL (https://&lt;IISServerFQDN&gt;/&lt;OLAPVirtual&gt;/msmdpump.dll).

2.  Go to Internet Explorer Advanced Settings.

3.  Clear the **Show friendly HTTP error messages** check box. The following information displays:

  ```
  <soap:Envelope xmlns:soap=\"http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
  <soap:Fault xmlns="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>XMLAnalysisError.0xc10e0002</faultcode>
  <faultstring>Parser: The syntax for 'GET' is incorrect.</faultstring>
  <detail>
  <Error ErrorCode="3238920194" Description="Parser: The syntax for 'GET' is incorrect." Source="Unknown" HelpFile="" />
  </detail>
  </soap:Fault>
  </soap:Body>
  </soap:Envelope>
  ```
Though the information above reflects an error, this message indicates that the data pump is configured correctly. If the information above does not display in the browser, check the Application 
Event log on the server for errors.

***Test the Connection Using SQL Server Management Studio***

You can use HTTP and SQL Server Management Studio to test the data pump connection to the Vantagepoint Analysis Cubes.

However, you cannot test the connection using SSMS if you chose Windows Integrated Authentication for the IIS virtual directory. In that case, you can temporarily set the IIS virtual directory 
permissions for Anonymous Access and test the configuration. Remember to change the Anonymous Access account to a user account that has read access to the Vision Analysis Cubes. After you 
complete the test, enable Windows Integrated Authentication again.

***Test the Connection Using Excel***

You can use HTTP and Microsoft Excel to test the data pump connection to Analysis Cubes. However, you cannot test the connection using Excel if you chose Windows Integrated Authentication for the 
IIS virtual directory. In that case, you can temporarily set the IIS virtual directory permissions for Anonymous Access and test the configuration. Remember to change the Anonymous Access account 
to a user account that has read access to the Vantagepoint Analysis Cubes. After you complete the test, enable Windows Integrated Authentication again.

## Configure the Data Source in Excel

After you configure IIS per the [Install and Configure the OLAP Data Pump](#install_and_configure_the_olap_data_pump) section, Internet users can use the URL when they configure the data source 
in Excel.

<div class="word-special word-procedure Procedure">To configure the data source in Excel:</div>

1.  In Excel, click the Data tab, and then click **From Other Sources » From Analysis Services**.

      On the Connect to Database Server page, enter the following information and click **Next**:

      - In the **Server name** field, enter the URL (**https://&lt;IISServerFQDN&gt;/olap/msmdpump.dll**).

      - Select the **Use the following User Name and Password** option.

      - In the **User Name** field, enter the user name (in form of **domain\\user**).

2.  On the Select Database and Table page, select the database from the drop-down list, select the cube that you want to connect to, and then click **Next**.

3.  On the Save Data Connection File and Finish page, select the **Save password in file** check box and click **Finish**.

      When you select the **Save password in file** check box, your domain password is saved in clear text in the data source connection file, which is a potential security risk. However, not 
selecting the check box will prompt you to re-enter the password.

4.  On the Import Data dialog box, click **OK**.

      If you did not select the **Save password in file** check box in Step 4, you may receive an "**Installation of the data source failed"** error.

5.  Click **OK**.

6.  On the Multidimensional Connection page, re-enter your password and click **Next**.

7.  On the Select the database that you want to work with page, click **Finish** to begin using the cube data in Excel.