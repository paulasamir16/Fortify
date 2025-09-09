## Here we will install
+ License and Infrastructure Manager (LIM)
+ ScanCentral DAST Components
+ Install Webinspect
+ Install .NET & Java Agents

<br/>

### LIM Installation
+ Install Hyper-V and containers from Server Manager
+ Download & Install Docker Engine — [Reference](https://docs.docker.com/engine/install/binaries/)
+ Add docker path to environment variables
+ Installing IIS, ASP.NET, .NET Framework, and Hyper-V from Server Manager
  + Web Server (IIS)
  + IIS Management Scripts and Tools — under Management Tools
  + On the Features window, under .NET Framework 4.8 Features, select .NET Framework and ASP.NET, and Containers
  + On the Role Services window under Application Development, select ASP.NET
  + In the Edit Site Binding dialog box in IIS, add a host name for the HTTPS binding
+ Sign up at Docker Hub then send your docker ID to this mail ```mfi-fortifydocker@opentext.com``` to join docker group and pull LIM image
  + Pull a LIM Docker image to host machine
    + Open **PowerShell** and login to docker by ```docker login``` and then pull the image
      ```
      docker pull fortifydocker/lim:<version>

      Example:
      docker pull fortifydocker/lim:23.2
      ```
  + Create a directory on host machine that is easily identifiable. For example: ```C:\lim```
  + Create and configure the Environment File ```LimDocker.evn``` inside ```C:\lim``` folder
    + File Example:
      ```
      #!-- LIM Docker env file. --!
      LimUseSSL=true
      LimAdminWebSiteName=LIM.Admin
      LimServiceSiteName=LIM.Service
      LimApiSiteName=LIM.API
      LimDirectory=c:\lim
      certpath=c:\lim\limcert.pfx
      certpassword=limcert
      LimAdminUsername=limuser
      LimAdminPassword=limuser!1
      LimAdminEmail=limadmin@limadmin.com
      LimAdminFriendlyName=LimAdmin!
      LimLogsDirectory=c:\lim\logs\
      ```
  + Running the Windows Container
    + The following example uses Docker CLI options to run the container without SSL
        ```
        docker run -v c:/lim:c:/lim -d -p 80:80 --restart always --env-file c:\lim\LimDocker.env --memory=8g --cpus=2 --name lim fortifydocker/lim:23.2
        ```
        ```
        docker container ls -a
        ```
    + If the ```LimDocker.env``` file includes the setting ```LimUseSSL=true``` and IIS is installed on the host where the Docker container will be running, then the following example runs the container with SSL
      ```
      docker run -v c:/lim:c:/lim -d -p 8443:443 --restart always --env-file c:\lim\LimDocker.env --memory=8g --cpus=2 --name lim fortifydocker/lim:23.2
      ```
      + If IIS is not installed on the host where the Docker container will be running, then the following example runs the container with SSL
      ```
      docker run -v c:/lim:c:/lim -d -p 443:443 --restart always --env-file c:\lim\LimDocker.env --memory=8g --cpus=2 --name lim fortifydocker/lim:23.2
      ```
    + Access LIM with this URL
      + ```http://<IP_Address>:<Port>/LIM.Admin```
    + Active and update LIM
      + To Activate LIM
        + Select ADMIN -> SERVER CONFIGURATION -> Activation
        + Click Activate
      + To update the application
        +	Select ADMIN -> SERVER CONFIGURATION -> Activation
        +	Click Update
        +	Adding an Administrator
    + To add an administrator
      +	Click ADMIN -> USERS
      +	Click ADD
      +	Type a User Name
      +	Type a Login Name
      +	Type an Email Address for the administrator
      +	To enable the LIM to send email notification of certain events to the administrator, select Receives Email
      +	Enter and confirm a Password
      +	To send the administrator email notification of the new account, select Send Welcome Email
      +	Adding a License
    + To add a license to the LIM database
      +	On the PRODUCT LICENSES page, click ADD LICENSE
      +	Enter (type or paste) the Activation Token associated with the product license sent to you by OpenText
      +	(Optional) Enter a Description for the license
      +	Click OK
    +	Adding a License Pool
      +	To create a license pool
        +	Click ADD LICENSE POOL
        +	Enter the POOL NAME
        +	Enter a POOL DESCRIPTION
        +	Enter and confirm the POOL PASSWORD
        +	Click OK

<br/>

### Install ScanCentral DAST Components
+ Download DAST file from SLD ```ScanCentral DAST <version>```
+ [Creating and Using a Settings File](https://www.microfocus.com/documentation/fortify-ScanCentral-DAST/2320/SC_DAST_Help_23.2.0/index.htm#DynCLI/DynCLI_Settings.htm?TocPath=Configuring%2520the%2520ScanCentral%2520DAST%2520Environment%257CCreating%2520and%2520Using%2520a%2520Settings%2520File%257C_____0)
  + Put the file inside ScanCentral DAST folder ```C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\``` with name ```SampleSettingsFile.json```)
  + [Example](https://drive.google.com/file/d/10XPsS2DwpnNE6_Lbni9m0aYf1YfacGqN/view?usp=drivesdk)
  + Note: you will find out the SecureBase folder in this path ```C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\scancentral-dast-config.tar\2a8033980bab20337b8d9c0a177ac45252de4c93b235813b10971d24d0675f41\layer.tar\Files\app\``` you can extract it with 7zip
  + Copy the ```DefaultData.zip``` to ```C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\```
+ Run Configuration CLI file
  +	Create folder, for example```C:\dast_config```
  +	Open PowerShell and write these two commands
    ```
    cd "C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\"
    ```
    ```
    .\DAST.ConfigurationToolCLI.exe configureEnvironment --mode new --settingsFile "C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\SampleSettingsFile.json" --outputDirectory "C:\dast_config"
    ```
    + Note: The second one takes a lot of time about 2:30 hours.
  +	Then the artifacts will be saved inside ```C:\dast_config``` folder
+	Extract this folder ```dast-windows-start```
+ Use the PowerShell script to pull and launch the core ScanCentral DAST containers (DAST API, DAST Global Service, and DAST Utility Service)
  ```
  cd "C:\dast_config\dast-windows-start"
  ```
  ```
  pull-and-start-containers.ps1
  ```
  ```
  pull-and-start-sensor-container.ps1
  ```
  ```
  pull-and-start-twofactorauth-container.ps1
  ```
+ Make sure if they are pulled and running
  + ```docker container ls -a```
+ Log in to Fortify SSC and enable ScanCentral DAST in the ADMINISTRATION view
  + ```http://<DAST_API_Hostname>:<Port>/api/```
  + ```http://<DAST_API_IP_Address>:<Port>/api/```
+ Copy the ```Master Token``` then open SSC -> ScanCentral -> DAST -> Two Factor Authentication -> + New 2FA SERVER
+ Write these in boxes input
  + my_2fa
  + ```https://<IP>:443/```
  + Paste the ```Master Token```

<br/>

### Install Webinspect
+ First, install SQL Server as we did with SSC database
+ Download the WebInspect package from portal (https://sld.microfocus.com/mysoftware/download/downloadCenter)
+ Extract the zip folder
+ Double-click on ```webinspect msi``` file to install and click **Next**, then accept the End‑User License Agreement and click **Next**
+ Leave the default Destination Folder as configured and click **Next**
+ Sensor Configuration **(optional)**
  + Enter the Enterprise Manager URL (The URL of Fortify WebInspect Enterprise manager)
  + In the Sensor Authentication group, enter the following Windows account credentials for this sensor
    + In the User Name box, type the sensor user name
    + In the Password and Confirm Password boxes, type the password for the sensor user
+ Click **Next**, then **Install**
+ Make sure that **Launch OpenText DAST <version>** is enabled and click **Finish**
+ SQL Server Express Is Not Installed
  + Choose **Run WebInspect now and enter remote SQL Server credentials**
  + Configure SQL Server
    + Put the Server name (Device Name)
    + Log on to the server (Choose one of these)
      + Use Windows Authentication
      + Use SQL Server Authentication
        + Username
        + Password
    + Connect to a database
      +  Enter a database name and then click on Create
    + Click **Ok**, then **Ok** and then **Ok** again
+ Click **Activate Now**
  + You have three methods to activate webinspect
    + Connect directly to OpenText corporate license server — (The Activation-Token is sent to you across mail)
      + Enter the Activation-Token
      + Disable Network Proxy is you don't have one
      + User Information
        + Company Name
        + Email
        + First Name
        + Last Name
    + Install License File — (Download the license from the portal)
      + Activation Token
      + License Request File
        + Click **File** and save the file with the chosen name at the default path, then click **Next**
        + Go to (https://licenseservice.fortify.microfocus.com/OfflineLicensing.aspx) and choose **Generate by a Fortify WebInspect Install** then click **Next**
        + Click **Browse** and choose the file that you saved, then click **Process Request File**
        + Click Retrieve Response File and locate a path to save the LicenseResp.xml file then click **Save**
        + Click Next, then click Finish
    + Connect to Fortify License and Infrastructure Manager — (Get Activation-Token from LIM)
      + License and Infrastructure Manager
        + URL — (LIM URL)
        + Pool Name
        + Password
      + Network Authorization — (LIM Credentials)
        + User Name
        + Password
      + Network Proxy — (Disable it you don't have one)
      + Click **Next** and then choose one of these
        + Connected License — WebInspect run only when the computer is able to contact the LIM
        + Detached License — WebInspect run anywhere until the license reach the expiration date
      + Click **Next**, then click **Finish**

<br/>

### Configuration
+ **Configure and start the WebInspect REST API**
  +	From the Windows Search, search for **Fortify Monitor**
  +	Right-click on **Fortify Monitor** from **Show hidden icons**, and select **Configure WebInspect API**
    +	**Note:** If you don't find it, you can search for **Fortify Monitor** from **Windows Search**
    +	Configure the API Server settings
      +	Host (The default setting is **+** — This is a wild card)
        +	You can put the URL that you can only access the API from it
      +	Port — (Change it to ```8443```)
      +	Authentication — (None, Basic, Windows, Client Certificate)
        +	For Basic, click **Edit passwords...** and edit the ```username1:password1``` to your username & password such as ```paul:paulasamir``` then click ```Ctrl+S``` to save the file, finally close the file
      +	Enable **Use HTTPS**
        +	Open PowerShell and put this script
          ```
          $rootcertID = (New-SelfSignedCertificate -DnsName "DO NOT TRUST - WIRC Test Root CA","localhost", "$($env:computername)" -CertStoreLocation "cert:\LocalMachine\My").Thumbprint
          $rootcert = (Get-Item -Path "cert:\LocalMachine\My\$($rootcertID)")

          $trustedRootStore = (Get-Item -Path "cert:\LocalMachine\Root")
          $trustedRootStore.open("ReadWrite")
          $trustedRootStore.add($rootcert)
          $trustedRootStore.close()
          
          netsh http add sslcert ipport=0.0.0.0:8443 certhash=$($rootcertID) appid="{160e1003-0b46-47c2-a2bc-01ea1e49b9dc}"
          ``` 
      +	Log Level — (Info, Debug, Warn, Critical)
        +	Choose one of them according to your requirements
        +	**Note:** The log files are located under **Applications and Services Logs > WebInspect API**
    + Click **Test API** or click **Start**

+ **Installing and Configuring the DAST Sensor Service**
  + Extract ```ScanCentral DAST – Sensor Service``` folder which is located at WebInspect folder in webinspect machine at this PATH ```C:\ScannerService```
    + Replace the ```appsettings.json``` file which the ScanCentral DAST Configuration Tool created where ScanCentral DAST install by the existing ```appsettings.json``` file at ```C:\ScannerService``` folder
    + Open cmd and run this command
      ```
      sc create ScannerWorkerService binpath= "C:\ScannerService \DAST.ScannerWorkerService.exe" start= auto depend= "WebInspect API" displayname= "WebInspect DAST Scanner Worker Service"
      ```
  + Open **Windows Services Manager** with ```Win+R``` and write ```services.msc```
  + Right-click on the scanner worker service ```ScannerWorkerService```
    + Nevigate to Log On and choose **This account:** then click **Browse** and choose the user and fill in the **Password** and **Confirm password**
    + Click **Apply** then click **OK**
  + Finally, start the service and make it run Automatically

+ **Configure Enterprise Server Sensor** — This is used for integrating **Fortify WebInspect** into **Fortify WebInspect Enterprise** as a sensor
  + From the **Windows Search**, search for **Fortify Monitor**
  +	Right-click on **Fortify Monitor** from **Show hidden icons**, and select **Configure Sensor**
    +	Manager URL — (This is the Fortify WebInspect Enterprise URL)
    +	Sensor Authentication - (WebInspect Enterprise machine credentials)
      +	User Name
      +	Password
    + Click **Test**
    +	Don't **Enable Proxy** if you don't have one
    +	Click **Start** then click **OK**

<br/>

### Install .NET & Java Agents (Installed on Web Server)
+ Download & extract ```WebinspectAgent_xx.x.zip```

#### Install dotnet agent
+ Right-click ```Fortify_WebInspect_Runtime_Agent_Dotnet_xx.x.windows_x64.exe```
  + Click **Next**
  + Accept the license and click **Next**
  + Choose the floder location and then click **Next**
  + Click **Next** then click **Finish**
  + 
+ Verify **dotnet agent** installation
  + Open cmd
    + ```cd <install_dir>\WebInspect_RuntimeAgt_Dotnet_xx.x\tools\```
    + ```IISControl.exe register restart```
    + Run a scan with Webinspect to see if the agent if detected

#### Install java agent
+ Extract ```Fortify_Webinspect_Runtime_Agent_Java.zip``` at ```C:\Fortify_WebInspect_Runtime_Agent_Java_xx.x```
+ Add ```FortifyAgent.jar``` jar file to Apache configuration
  + ```-javaagent: <install_dir>\lib\FortifyAgent.jar```
  + Restart the Apache Tomcat


## Upgrade DAST Components
+ Coming soon

<br/>

## Upgrade LIM
+ Coming soon

