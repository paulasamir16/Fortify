
## Here we will install SAST Components
+ ScanCentral SAST Controller
+ SAST Static Code Analyzer (SCA) Sensor & Client
+ Fortify Plugins for IDEs
+ Fortify Plugins for CI/CD Tools
+ Audit Assistant (AA)

<br/>

### Install ScanCentral SAST Controller
+ Download & extract the contents of the ```Fortify_ScanCentral_Controller_<version>_x64.zip``` in ```C:\Program Files\Fortify```
+ Make sure that the ```JRE_HOME``` and ```JAVA_HOME``` environment variables are correctly configured into ```System Variables```
  + Enter ```JAVA_HOME``` as variable name and ```C:\Program Files\Java\jdk-11``` as Value
  + Enter ```JRE_HOME``` as variable name and ```C:\Program Files\Java\jre-11``` as Value
+ Make sure that the ```CATALINA_HOME``` environment variable is either empty or set up to point to the ```<controller_install_dir>\tomcat``` directory
+ Navigate to the ```<controller_install_dir>\tomcat\bin``` directory, then run the following
  + ```service.bat install ScanCentralController```
+ Configuring Java Memory for the Service
  + Run ```tomcat9w.exe``` from ```<controller_install_dir>\tomcat\bin``` directory
  + In the Apache Tomcat Properties window, click the ```Java``` tab, and then set the
    + ```Initial memory pool``` value to ```2048```
    + ```Maximum memory pool``` value to ```4096```
  + Restart the service
+ Create secure connection with Apache Tomcat
  ```
  keytool -genkey -alias “mykey” -keyalg RSA -keystore C:\mykeystore
  ```
  ```
  keytool -export -alias “mykey” -keystore C:\mykeystore -file "mycert.cer"
  ```
+ Add the following connector to the ```server.xml``` file in the ```tomcat/conf``` directory
  ```
  <Connector port="8443" maxThreads="200"
  scheme="https" secure="true" SSLEnabled="true"
  keystoreFile="<mykeystore>" keystorePass="<mypassword>"
  clientAuth="false" sslProtocol="TLS"/>
  ```
+ Open ```config.properties``` inside ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes``` and edit
  + worker_auth_token
  + client_auth_token
  + max_upload_size
  + ssc_url to ```http/https://<hostname/IP>:<port>/ssc```
  + ssc_scancentral_ctrl_secret
  + this_url to ```http/https://<controller_host/IP>:<port>/scancentral-ctrl```
  + lim_server_url to ```http/https://<hostname/IP>/LIM.Admin```
  + lim_license_pool
  + lim_license_pool_password
  + client_auto_update=true
    + Use this if you need to get version updates for clients and sensors from Controller — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2520/sc-sast-ugd-html-25.2.0/index.htm#upgrades/auto-updates.htm)
  + pool_mapping_mode — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/pool-map-mode.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257CConfiguring%2520the%2520%2520Controller%257C_____1)
+ Restart Tomcat ScanCentral service

> [!NOTE]
> + If you want to encrypt the Shared Secret on the Controller, follow instructions from this [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/encrypt_pwds-ctrl.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257CConfiguring%2520the%2520%2520Controller%257C_____2)

+ Start the controller
  + Open cmd
    + ```cd <controller_install_dir>/tomcat/bin```
      + ```startup.bat```
> [!NOTE]
> If you want to stop the Controller run ```shutdown.bat```


#### Configuration
+ **Connect ScanCentral Controller with SSC**
  + Open Administration -> Configuration -> ScanCentral SAST
    + ScanCentral Controller URL
    + SSC and ScanCentral Controller shared secret
  + Verify the connection between ScanCentral Controller and SSC
    + Open ScanCentral -> SAST -> Controller
      + You must find information about ScanCentral Controller and if you find this message “there is no scancentral controller information to display” then the connection failed and you should investigate the issue
+ **Configuring the log level on the Controller**
  + Open the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/log4j2.xml``` file in a text editor
  + Locate one of the following strings
    ```
    <Logger name="com.fortify.cloud" level="info" additivity="false">
    <Logger name="com.fortify.cloud.ctrl.service" level="info" additivity="false">
    ```
  + For a more detailed level of logging, change the level as shown in the following example
    ```
    <Logger name="com.fortify.cloud" level="debug" additivity="false">
    ```
  + To apply the changes, restart the Controller
    + ```sc start SCanCentraller``` or from ```services.msc```

> [!NOTE]
> ScanCentral Controller supports two types of logging level
> + info — An overview of what the application is doing
> + debug — It contains detailed diagnostic information so, it's useful for troubleshooting

<br/>

+ **Avoiding read timeout errors during attempts to upload large log files**
  + To configure the connection timeout between the Controller and Fortify Software Security Center
    + On the Controller, open the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/config.properties``` file in a text editor
    + Increase the value of the ```ssc_restapi_connect_timeout``` and ```ssc_restapi_read_timeout``` properties to an acceptable threshold (in milliseconds)
    + Save the changes
  + To configure the connection timeout between the Controller and a sensor
    + On the sensor machine, open the ```<sast_install_dir>/Core/config/worker.properties``` file in a text editor
    + Uncomment the ```restapi_connect_timeout``` and ```restapi_read_timeout``` properties, and then set the value of each to an acceptable threshold (in milliseconds)
      + **Note:** you may not find these properties, so you can simply add them at the end of the file
    + Save the changes
  + To configure the connection timeout between the Controller and a client
    + On the client machine, open the ```<client_install_dir>/Core/config/client.properties``` file in a text editor
    + Uncomment the ```restapi_connect_timeout``` and ```restapi_read_timeout``` properties, and then set the value of each to an acceptable threshold (in milliseconds)
    + Save the changes


+ Review Fortify ScanCentral SAST API — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/sc-api.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257C_____8)


<br/>

### Install SCA as a ScanCentral SAST Sensors — Used by ```worker``` command
+ Download and extract ```Fortify_SCA_<version>_windows_x64```
+ Click on ```Fortify_SCA_<version>_windows_x64.exe```
+ Open cmd
  ```
  cd <sca_install_dir>\bin\
  ```
  ```
  fortifyupdate
  ```
  ```
  scapostinstall
  ```
    +	Then edit SSC Settings like SSC URL, Username, and get updates from SSC
+ Edit ```<sca_install_dir>\Core\config\worker.properties```
  + worker_auth_token
+ Open cmd to setup the sensor service
  ```
  cd <sca_install_dir>\bin\scancentral-worker-service
  ```
  + If you want to use a plaintext password
  ```
  setupworkerservice.bat <sca_version> <controller_url> <shared_secret>
  ```
  + If you want to use an encrypted password
  ```
  setupworkerservice.bat <sca_version> <controller_url> "<encrypted_shared_secret>" <path_to_pwtool.keys_file>
  ```
+ Start the sensor
  + Set service to Automatic and LogOn as Local System then enable Allow service to interact with desktop.
  + Start FortifyScanCentralWorkerService service
    + ```net start <service display-name>``` or ```sc start <service-name>``` or from ```services.msc```

#### Configuration
+ **Configuring Job Cleanup Timing on Sensors**
  + Navigate to the ```<sca_install_dir>/Core/config``` directory, and then open the ```worker.properties``` file in a text editor to add these properties
    + worker_cleanup_age
      + It's the age job files must be before they are removed from the sensor working directory — Default value (hours) is 168 (one week)
    + worker_cleanup_interval
      + It's frequency with which the cleanup process runs — Default value (hours) is 1 hour
  + Save and close your ```worker.properties``` file
  + Restart the sensor
+ 

<br/>

#### Additional Configuration
+ **[Setting the Maximum Run Time for Scans](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#sensors/max_scan_runtime.htm?TocPath=About%2520Fortify%2520ScanCentral%2520SAST%2520Sensors%257CConfiguring%2520Sensors%257C_____2)**
+ **[Encrypting the Shared Secret on a Sensor](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#sensors/encrypt-pwds-sensors.htm?TocPath=About%2520Fortify%2520ScanCentral%2520SAST%2520Sensors%257CConfiguring%2520Sensors%257C_____1)**
+ **[Changing Sensor Expiration Time](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#sensors/expiration.htm?TocPath=About%2520Fortify%2520ScanCentral%2520SAST%2520Sensors%257CConfiguring%2520Sensors%257C_____3)**
+ **Add certificate to SCA Adding Trusted Certificates — [Reference](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/2320/SCA_Help_23.2.0/index.htm#install/PostInstall/add-trusted-certs.htm?TocPath=Installing%2520Fortify%2520Static%2520Code%2520Analyzer%257CPost-Installation%2520Tasks%257C_____7)**

<br/>

### Install ScanCentral SAST Clients
> [!NOTE]
> There are two methods to install SAST Clients
> + Installed as an embedded client which is part of the Fortify Static Code Analyzer (SCA) — Used by ```start``` command
> + Installed as a standalone client, which is independent of Fortify Static Code Analyzer (SCA)

#### Embedded Client
+ Enter ```<sca_install_dir>/Core/config``` and open ```client.properties```
+ Set the same value for the ```client_auth_token``` that you set for the ```client_auth_token``` on the Controller (in the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/config.properties``` file)

<br/>

#### Standalone Client
+ Download & extract the contents of the ```Fortify_ScanCentral_Client_<version>_x64.zip```
+ Add the ```<client_install_dir>/bin``` to your PATH environment variable
+ Add the ```JAVA_HOME``` environment variable
  + Important! If you have a Java 8 project that fails to build because Fortify ScanCentral SAST requires Java 11 or later to run, set the ```SCANCENTRAL_JAVA_HOME``` environment variable to point a supported version of Java.
+ Open ```<client_install_dir>/Core/config/client.properties``` in a text editor
+ Set the same value for the ```client_auth_token``` property that you set for the ```client_auth_token``` property on the Controller (in the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/config.properties``` file)
+ Save the file

> [!NOTE]
> + A standalone client, which does not require Fortify Static Code Analyzer to be installed, can create a package of the code with its dependencies to send to the Controller for translation and scanning.
> + Sensor functionality depends on Fortify Static Code Analyzer. So, you can have a standalone client, but not a standalone sensor.

#### Configuration
+ Enabling debugging on clients and sensors
  + Open cmd
    ```
    cd <sca_install_dir>\bin\
    ```
    ```
    scancentral ‑debug ‑url <controller_url> worker
    ```
    ```
    scancentral ‑debug ‑url <controller_url> start
    ```

<br/>

### Audit Assistant (AA) Deployment
+ Login to [Fortify Audit Assistant](https://analytics.fortify.com/login) and create a token, and a policy
+ Open SSC -> Administrator -> Configuration -> Audit Assistant (AA)
+ Enable AA and put the token that you got from the link above
+ Choose the policy and Enable AA auto-apply, and auto-predict

#### Configuring Audit Assistant Custom Tags
+ This is a message that clarifies the status of the issue and it is something between these 3 things
  + Not an issue
  + Not predicted
  + Exploitable
+ And you can make this configuration through
  + SSC -> Administration -> Templates -> Custom Tags -> Edit Analysis

<br/>

### Install Plugins
#### Plugins for Eclipse (Fortify SCA, Fortify Security Assistant, Fortify Remediation Plugin)
##### Fortify SCA
+	Help -> Install New Software
+	Click Add and choose Local then locate the Eclipse plugin folder
+	Select All
+	Next -> Finish
+	Accept restart eclipse after finish the installation


##### Fortify Security Assistant
+	Help -> Install New Software
+	Click Add and choose Archive then locate the Security Assistant plugin zip file
+	Select All
+	Next -> Finish
+	Accept restart eclipse after finish the installation


##### Fortify Remediation Plugin (Also, you can install int from Eclipse Marketplace)
+	Help -> Install New Software
+	Click Add
+	In the Location box, type ```https://tools.fortify.com/ssceclipseplugin```
+	Click Add
+	Click Select All and then click Next
+	Click Finish
+	Click Restart

> [!NOTE]
> To verify License in Eclipse
> + Go to Fortify -> Manage License

<br/>

#### Plugins for Visual Studio (Fortify SCA, Fortify Security Assistant, SAST)
+	Open Visual Studio -> Extensions -> Manage Extensions
+	Search for Fortify
+	Install the 2 plugins
  +	SAST
+	Eclipse -> Fortify -> Options -> SAST Configuration
+	Get the token key from SSC -> Administration -> Users -> Token Management -> NEW -> ToolsConnectToken

<br/>

#### Plugin for IDEA or Android Studio
+	Start IntelliJ IDEA or Android Studio
+	Choose Plugin -> Settings
+	Select Install Plugin from Disk, browse to the ```<tools_install_dir>/plugins/IntelliJAnalysis``` directory, and then select ```Fortify_IntelliJ_Analysis_Plugin_<version>.zip```
+	Click OK the restart

<br/>

### Bug Trackers in SSC
+ System can integrate with Jira, Bugzilla, ALM, and Azure DevOps Server
  + SSC -> Administrator -> Plugins -> Bug Tracking Plugins
  + NEW -> Choose Plugin from ```<ssc_install_dir>/<ssc_install_WAR_Tomcat>/Plugins/<BugTrackerPlugin_Name>```

<br/>

### Fortify Plugin for CI/CD Tools (Jenkins, GitHub, GitLab, Azure DevOps, Bamboo)
#### Fortify Plugin for Jenkins
+	Install Fortify from Manage Jenkins > Manage Plugins -> Available tab -> In the Filter box, type Fortify
+	Create 2 token (CIToken, ScanCentralCtrlToken) from SSC for SSC and ScanCentral SAST, then edit settings in Jenkins to integrate with them
+	Install Missing Maven Plugins

<br/>

#### GitHub
+ Create a personal access token with enable these points
+ GitHub -> Your login -> Settings -> Developer settings -> Personal access tokens -> Tokens (classic) -> Generate new token -> Generate new token (classic)
  + Repo                 -------------->    Full control of private repositories
  + workflow             -------------->  	Update GitHub Action workflows
  + admin:repo_hook      -------------->	  Full control of repository hooks
  + admin:org_hook       -------------->  	Full control of organization hooks
  + gist                 -------------->  	Create gist
  + notifications        -------------->	  Access notifications
  + delete_repo          -------------->	  Delete repositories
  + project              -------------->	  Full control of projects
+ Save the token in text file
+ Configuring Webhook on GitHub
+ In the GitHub Repository -> Settings -> Webhooks -> Add webhook -> Payload URL [(Jenkins URL)/github-webhook/] -> application/json -> Let me select individual events [Pull requests] -> Add webhook


##### Configure Jenkins to use GitHub
+ Jenkins -> Manage Jenkins -> Configure System -> Add GitHub Server -> GitHub Server -> fill inputs -> + Add -> Jenkins -> Kind [Secret text] -> fill inputs -> Add -> Choose the Credentials -> Test the connection -> Save

<br/>

#### GitLab
+ Create a project access token in GitLab
  + In Repo page, Click on Settings -> Access Tokens -> Enable the 'api' -> Create project access token -> Copy the token
+ Create a Personal Access Token
  + Click on your account icon -> Preferences -> Access Tokens -> Enable the 'api' -> Create personal access token -> Copy the token
+ Integrate Jenkins with GitLab
  + In Repo page, Click on Settings -> Integrations -> Jenkins -> Enable (Merge request, Tag push) -> Disable the SSL verification -> Test settings -> Save Changes

##### Configure Jenkins to use GitLab
+ Install the GitLab plugin
  + In Jenkins, Click on Manage Jenkins -> Manage Plugins -> Enable GitLab (Build Triggers) -> Click Download now and install after restart -> Restart
+ Configure GitLab
  + Manage Jenkins -> Configure System -> In GitLab section -> +Add -> Jenkins -> GitLab API token [Put ur personal token] -> Add -> Test Connection -> Save


#### Azure DevOps
+ [Reference1](https://www.youtube.com/watch?v=7nUytfCmBAY&t)
+ [Reference2](https://share.cds.dominknow.one/735e6bb728ef9f7c4444571ab4bcfa28/d035c126-6b3d-3691-ae1b-a7f21d3f5d99/9CFA50DF-9547-0045-3963-77518E4ABE84/index.html)

#### Bamboo
+ [Reference1](https://www.youtube.com/watch?v=6-6ZGZbFHDw)
+ [Reference2](https://www.youtube.com/watch?v=4Wd5ChngGMI&t)
+ [Reference3](https://www.microfocus.com/documentation/fortify-plugin-for-bamboo/13/Bamboo_Plugin_Guide_1.3.pdf)



## Upgrading SAST Components
+ Coming soon

