## Here we will install
+ License and Infrastructure Manager (LIM)
+ ScanCentral DAST Components
+ Install Webinspect
+ Install .NET & Java Agents

<br/>

### LIM Installation
+ Install Hyper-V and containers from Server Manager
+ Download & Install Docker Engine — [Docker-URL](https://docs.docker.com/engine/install/binaries/)
+ Add docker path to environment variables
+ Installing IIS, ASP.NET, .NET Framework, and Hyper-V from Server Manager
  + Web Server (IIS)
  + IIS Management Scripts and Tools — under Management Tools
  + On the Features window, under .NET Framework 4.8 Features, select .NET Framework and ASP.NET, and Containers
  + On the Role Services window under Application Development, select ASP.NET
  + In the Edit Site Binding dialog box in IIS, add a host name for the HTTPS binding
+ Sign up at Docker Hub then send your docker ID to this mail ```mfi-fortifydocker@opentext.com``` to join docker group and pull LIM image
  + Pull a LIM Docker image to host machine
    + Open PowerShell and login to docker by ```docker login``` and then pull the image
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
+ Configure JSON Sample File (Exists inside ScanCentral DAST File with name ```SampleSettingsFile.json```)
  + Note: you will find out the SecureBase folder in this path ```C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\scancentral-dast-config.tar\2a8033980bab20337b8d9c0a177ac45252de4c93b235813b10971d24d0675f41\layer.tar\Files\app\``` with 7zip
  + Copy the ```DefaultData.zip``` to ```C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\```
+ Run Configuration CLI file
  +	Create folder, for example```C:\dast_config```
  +	Open cmd and write these 2 commands
    ```
    cd C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI
    ```
    ```
    DAST.ConfigurationToolCLI.exe configureEnvironment --mode New --settingsFile "C:\Users\Administrator\Downloads\ScanCentral_DAST_23.2\ScanCentral_DAST_23.2\ScanCentral DAST - CLI Config Tool\ConfigurationToolCLI\SampleSettingsFile.json" --outputDirectory "C:\dast_config"
    ```
  +	Then the artifacts will be saved in ```C:\dast_config``` folder
+ Use the PowerShell script to pull and launch the core ScanCentral DAST containers (DAST API, DAST Global Service, and DAST Utility Service)
  ```
  pull-and-start-containers.ps1
  ```
+ Log in to Fortify SSC and enable ScanCentral DAST in the ADMINISTRATION view
  + ```http://<DAST_API_Hostname>:<Port>/api/```
  + ```http://<DAST_API_IP_Address>:<Port>/api/```


```
pull-and-start-sensor-container.ps1
```
```
pull-and-start-twofactorauth-container.ps1
```
  + Copy the Master Token then open SSC -> ScanCentral -> DAST -> Two Factor Authentication -> + New 2FA SERVER
  + Write these in boxes input
    + my_2fa
    + ```https://[IP]:443/```
    + Paste Master Token

<br/>

### Install Webinspect
+ Right click on webinspect exe/msi to install, click Next, Next
+ Connect to DB Server with create new db and choose ```SQL Auth```
+ Connect to LIM to activate webinspect or activate it directly
+ Configure and start the WebInspect REST API
  +	Add Webinspect PATH to Environment variable
  +	From the Windows Search, click on Fortify Monitor
  +	Open Internet Explorer and click Enable
  +	Right-click on Fortify Monitor on hidden icons, and select Configure WebInspect API
  +	Configure the API Server settings
    +	Host (try to put SSC URL)
    +	Port
    +	Authentication, and Use HTTPs [None]
    +	Log Level (Search for difference)
+ Test API or click start
+ Installing and Configuring the DAST Sensor Service
+ Extract ```ScanCentral DAST – Sensor Service``` which exists at WebInspect folder in webinspect machine at this PATH ```C:\ScannerService```
  + Replace the ```appsettings.json``` file that the ScanCentral DAST Configuration Tool created by the existing file in ```C:\ScannerService```
  + Run this on cmd
    ```
    sc create ScannerWorkerService binpath= "C:\ScannerService \DAST.ScannerWorkerService.exe" start= auto depend= "WebInspect API" displayname= "WebInspect DAST Scanner Worker Service"
    ```
  + Restart
  + Open Windows Services Manager ```services.msc```
  + Right click on the scanner worker service ```ScannerWorkerService```
  + Configure the user account and password under which the service should run
  + Apply the changes
  + Start the service

<br/>

### Install .NET & Java Agents [Installed On Web Server]
+ Download & extract ```WebinspectAgent_xx.x.zip```


#### Install dotnet agent
+ Click on ```Fortify_WebInspect_Runtime_Agent_Dotnet_xx.x.windows_x64.exe```
+ Verify dotnet agent installation
  + Open cmd
  + ```cd <install_dir>\WebInspect_RuntimeAgt_Dotnet_xx.x\tools\```
  + ```IISControl.exe register restart```
    + Run a scan with Webinspect to see if the agent if detected


#### Install java agent
+ Extract ```Fortify_Webinspect_Runtime_Agent_Java.zip``` at ```C:\Fortify_WebInspect_Runtime_Agent_Java_xx.x```
+ Add jar file to Apache configuration
  + ```-javaagent: <install_dir>\lib\FortifyAgent.jar```
  + Restart Apache Tomcat



