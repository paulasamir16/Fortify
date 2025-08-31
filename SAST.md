
## Here we will install
+ ScanCentral SAST Controller
+ SAST SCA Sensor & Client
+ Plugins
+ Audit Assistant (AA)

<br/>

### Install ScanCentral SAST Controller
+ Download & extract the contents of the ```Fortify_ScanCentral_Controller_<version>_x64.zip``` in ```C:\Programs Files\Fortify```
+ Make sure that the JRE_HOME and JAVA_HOME environment variables are correctly configured. [In System Variables]
  + Enter ```JAVA_HOME``` as variable name and ```C:\Program Files\Java\jdk-11``` as Value
  + Enter ```JRE_HOME``` as variable name and ```C:\Program Files\Java\jre-11``` as Value
+ Make sure that the CATALINA_HOME environment variable is either empty or set up to point to the ```<controller_install_dir>\tomcat``` directory
+ Navigate to the ```<controller_install_dir>\tomcat\bin``` directory, then run the following
  + ```service.bat install ScanCentralController```
+ Configuring Java Memory for the Service
  + Run ```tomcat9w.exe``` from ```<controller_install_dir>\tomcat\bin``` directory
  + In the Apache Tomcat Properties window, click the ```Java``` tab, and then set the ```Maximum memory pool``` value ----> 4096
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
  + ssc_url to ```http://192.168.1.67:8080/ssc```
  + ssc_scancentral_ctrl_secret
    + Don’t use #, use a simple secret such as Paul123
  + this_url to ```https://<controller_host>:8443/scancentral-ctrl```
  + lim_server_url to ```http://192.168.1.70/LIM.Admin```
  + lim_license_pool
  + lim_license_pool_password
+ Restart Tomcat ScanCentral service
+ Review these
  +	About the pool_mapping_mode Property — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/pool-map-mode.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257CConfiguring%2520the%2520%2520Controller%257C_____1)
  +	Encrypting the Shared Secret on the Controller — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/encrypt_pwds-ctrl.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257CConfiguring%2520the%2520%2520Controller%257C_____2)
  +	Avoiding Read Timeout Errors — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/avoid-timeout.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257CConfiguring%2520the%2520%2520Controller%257C_____3)
+ Start the controller
  + Open cmd
    + ```cd <controller_install_dir>/tomcat/bin```
      + ```startup.bat```
+ Review Fortify ScanCentral SAST API — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#controller/sc-api.htm?TocPath=About%2520the%2520Fortify%2520ScanCentral%2520SAST%2520Controller%257C_____8)
+ Connect ScanCentral Controller with SSC
  + Open Administration -> Configuration -> ScanCentral SAST
    + ScanCentral Controller URL
    + SSC and ScanCentral Controller shared secret
+ Verify the connection between ScanCentral Controller and SSC
  + Open ScanCentral -> SAST -> Controller
    + You must find information about ScanCentral Controller and if you find this message “there is no scancentral controller information to display” then the connection failed and you should investigate the issue.

<br/>

### Install SCA as a ScanCentral SAST Sensors
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
    +	Then edit SSC Settings
+ Edit <sca_install_dir>\Core\config\worker.properties
  + worker_auth_token
+ Open cmd to setup the sensor service
  ```
  cd <sca_install_dir>\bin\scancentral-worker-service
  ```
  + If you want to use a plaintext password
  ```
  setupworkerservice.bat <sca_version> <controller_url> <shared_secret>

  Example:
  ???
  ```
  + If you want to use an encrypted password
  ```
  setupworkerservice.bat <sca_version> <controller_url> "<encrypted_shared_secret>" <path_to_pwtool.keys_file>

  Example:
  ???
  ```
+ Start the sensor
  + Set service to Automatic and LogOn as Local System then enable Allow service to interact with desktop.
  + Start FortifyScanCentralWorkerService service
    + ```net start <service display-name>``` Or ```sc start <service-name>```
+ Review Configuring Sensors — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/2320/SC_SAST_Help_23.2.0/index.htm#sensors/config-sensors.htm?TocPath=About%2520Fortify%2520ScanCentral%2520SAST%2520Sensors%257CConfiguring%2520Sensors%257C_____0)_
+ Add certificate to SCA Adding Trusted Certificates — [Reference](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/2320/SCA_Help_23.2.0/index.htm#install/PostInstall/add-trusted-certs.htm?TocPath=Installing%2520Fortify%2520Static%2520Code%2520Analyzer%257CPost-Installation%2520Tasks%257C_____7)

<br/>

### Install ScanCentral SAST Clients
> [!NOTE]
> There are two methods to install SAST Clinets
> + Installed beside SCA
> + Installed without SCA

#### Installed beside SCA
+ Enter ```<sca_install_dir>/Core/config``` and open ```client.properties```
+ Set the same value for the ```client_auth_token``` that you set for the ```client_auth_token``` on the Controller [in the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/config.properties``` file]

<br/>

#### Installed without SCA
+ Download & extract the contents of the ```Fortify_ScanCentral_Client_<version>_x64.zip```
+ Add the ```<client_install_dir>/bin``` to your PATH environment variable
+ Add the ```JAVA_HOME``` environment variable
  + Important! If you have a Java 8 project that fails to build because Fortify ScanCentral SAST requires Java 11 or later to run, set the ```SCANCENTRAL_JAVA_HOME``` environment variable to point a supported version of Java.
+ Open ```<client_install_dir>/Core/config/client.properties``` in a text editor
+ Set the same value for the ```client_auth_token``` property that you set for the ```client_auth_token``` property on the Controller [in the ```<controller_install_dir>/tomcat/webapps/scancentral-ctrl/WEB-INF/classes/config.properties``` file]
+ Save the file

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
#### Plugins for Eclipse [Fortify SCA, Fortify Security Assistant, Fortify Remediation Plugin]
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


##### Fortify Remediation Plugin [Also, you can install int from Eclipse Marketplace]
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

#### Plugins for Visual Studio [Fortify SCA, Fortify Security Assistant, SAST]
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
+	Create 2 token [CIToken, ScanCentralCtrlToken] from SSC for SSC and ScanCentral SAST, then edit settings in Jenkins to integrate with them
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


#### Bamboo






