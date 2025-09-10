## What is Fortify?
+ Fortify is a comprehensive application security (AppSec) platform developed by MicroFocus (OpenText now).
+ It designed to help organizations identify, assess, and remediate vulnerabilities in their software. It offers a range of tools and services, including static and dynamic application security testing (SAST and DAST), software composition analysis (SCA), and application security management.
+ Fortify aims to integrate security into the software development lifecycle (SDLC) and empower DevSecOps practices.

<br/>

## Key Components and Features
### There are three main features offered by OpenText Fortify

**Dynamic Application Security Testing (DAST)**
+ It helps organizations identify and remediate security vulnerabilities in their web applications. Fortify DAST conducts dynamic analysis by simulating attacks against running web applications to detect common security flaws such as SQL injection, cross-site scripting (XSS), insecure authentication mechanisms, and more.

**Static Application Security Testing (SAST)**
+ It's a method used to analyze and evaluate the security of software by examining the source code of an application. Unlike dynamic testing, which examines running applications, SAST tools work on static (non-running) code.
+ It helps developers and security teams identify vulnerabilities early in the software development life cycle (SDLC) by scanning source code for security issues — before the application is ever run.

**Software Composition Analysis (SCA)**
+ Helps organizations manage the security risks associated with third-party and open-source components.

<br/>

## Good, Let's talk about components of every feature
### Dynamic Application Security Testing (DAST)
+ [OpenText™ ScanCentral DAST](https://www.microfocus.com/documentation/fortify-ScanCentral-DAST/)
  + ScanCentral DAST Global Service
  + ScanCentral DAST API
  + ScanCentral DAST Utility Service
  + ScanCentral DAST database
  + OpenText DAST sensor
+ OpenText™ Dynamic Application Security Testing (OpenText DAST) — [Fortify WebInspect](https://www.microfocus.com/documentation/fortify-webinspect/)
+ [Fortify WebInspect Enterprise](https://www.microfocus.com/documentation/fortify-webinspect-enterprise/)

<br/>

### Static Application Security Testing (SAST)
+ **OpenText™ Static Application Security Testing (OpenText SAST) — [Static Code Analyzer (SCA)](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/)**
+ **[OpenText™ Fortify ScanCentral SAST](https://www.microfocus.com/documentation/fortify-software-security-center/)**
  + **Fortify ScanCentral SAST Controller**
    + It's a standalone server that sits between the Fortify ScanCentral SAST clients, sensors, and optionally, Fortify Software Security Center. The Controller accepts scan requests issued by the clients and assigns them to an available sensor. A sensor returns scan results to the Controller, which stores them temporarily.
  + **Fortify ScanCentral SAST sensors**
    + They are computers set up to receive scan requests and analyze code using Fortify Static Code Analyzer.
  + **Fortify ScanCentral SAST client**
    + Enables you to offload OpenText SAST analysis to Fortify ScanCentral SAST, which can perform remote translation and scan of your applications.
+ **[Tools and Commands](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/2520/sast-tgd-html-25.2.0/index.htm#GetStarted/sca-apps-tools.htm?TocPath=Getting%2520Started%257C_____2)**
  + **sourceanalyzer ```<sca_install_dir>/bin/```**
    + It involves two main stages:
      + Translation: The source code is translated into an intermediate representation Normalized Syntax Tree (NST files) that Fortify's analysis engines can understand.
      + Analysis: Fortify's analysis engines process the translated code to identify vulnerabilities based on a comprehensive knowledge base of secure coding rules and algorithms.
  + **scancentral ```<sca_install_dir>/bin/```**
    + It's a tool used to interact with SAST Client and Sensor to translate and scan projects and also interacts with SSC to upload scan results.
  + **packagescanner ```<sca_install_dir>/bin/```**
    + It takes a project package, which is a file created by the scancentral package command, and then performs a static analysis scan on that package locally using a locally installed Fortify Static Code Analyzer (SCA) instance. This is particularly useful for quickly analyzing code before a full upload and analysis on the central controller.
    + Also, you can use the tool to perform only the translation phase with ```--no-scan``` option on the project package and then submit it to the Controller for the analysis phase later. This provides flexibility in the scanning workflow.
      ```
      Example:

      scancentral package -o MyProjPackage.zip
      packagescanner -package MyProjPackage.zip -b xyz --no-scan
      scancentral -sscurl <ssc_url> -ssctoken <token> start -b xyz -scan
      ```
  + **fcli**
    + It's a command-line utility for interacting with various Fortify products
      + [Reference1](https://www.youtube.com/watch?v=_jQuOrP2viY)
      + [Reference2](https://fortify.github.io/fcli/)
      + [Reference3](https://github.com/fortify/fcli)
      + [Reference4](https://github.com/janwienand/fcli-for-ssc-and-scancentral-sast/)
  + **Audit Workbench (AWB)**
    + Provides a graphical user interface for OpenText SAST analysis results that helps you organize, investigate, and prioritize analysis results so that developers can fix security flaws quickly.
  + [**Audit Assistant (AA)**](https://www.microfocus.com/documentation/fortify-audit-assistant/)
    + It helps you to predict which of the issues returned from OpenText Fortify Static Code Analyzer represent true vulnerabilities and which do not.
    + AA uses machine learning to prioritize issues based on historical audits.
  + **IDE Plugins**
    + SCA Plugin (Eclipse, IntelliJ, and Visual Studio, VS Code)
    + Fortify Security Assistant Extension (Eclipse, IntelliJ, Visual Studio and Android Studio)
    + Remediation Plugin (Eclipse, IntelliJ and Android Studio)
  + **Fortify Scan Wizard**
    + 	Provides a graphical user interface that enables you to prepare a script to scan your code with OpenText SAST (either locally or remotely using Fortify ScanCentral SAST) and then optionally upload the results to Fortify Software Security Center
  + **Fortify Custom Rules Editor**
    + Provides a graphical user interface to create and edit custom rules
  + **FPRUtility**
    + Command-line tool that enables you to
      + Merge audited projects
      + Verify FPR signatures
      + Display information from an FPR file including
        + Any errors associated with the analysis
        + Number of issues
        + Filtered lists of issues in different formats
        + Lines of code for analyzed files
        + List of analyzed functions
        + Mappings for a migrated project
      + Combine or split source code files and audit projects into FPR files
      + Alter an FPR
  + **fortifyclient**
    + Command-line utility to create Fortify Software Security Center authentication tokens and securely transfer objects to and from Fortify Software Security Center.


> [!NOTE]
> + Mobile Build Session (MBS) enables translation on one machine and scanning on another, improving scalability

<br/>

### Software Composition Analysis (SCA)
+ OpenText Fortify offers SCA scan across two products
  + For Cloud Services (Fortify on Demand), they offer OpenText Debricked SCA product
    + [Fortify Source Components Analysis scan — (Debricked SCA)](https://support.cyberreshelp.com/hc/en-us/articles/16745069052695-Fortify-Source-Components-Analysis-scan-Debricked-SCA-how-to-initiate-it-and-how-to-integrate-with-Fortify-SSC)
    + [Debricked Integration into Fortify Software Security Center (SSC)](https://www.youtube.com/watch?v=tDGg7cRqg7Y)
  + For On-Premise Services, they offer an integration with Sonatype
    + [Sonatype](https://support.cyberreshelp.com/hc/en-us/articles/15963630854295-Fortify-Source-Components-Analysis-scan-Sonatype-SCA-how-to-initiate-it-and-how-to-integrate-with-Fortify-SSC)

<br/>

### Fortify Software Security Center (SSC) — [Reference](https://www.microfocus.com/documentation/fortify-software-security-center/)
+ It's a browser-based application that provides a set of capabilities across the software development life cycle to automate detection of security vulnerabilities in applications. It helps your security and development teams work together to resolve security flaws quickly and accurately by making correlated data available from the following products:
  + OpenText™ Static Application Security Testing (OpenText SAST)
  + OpenText™ Fortify ScanCentral SAST
  + OpenText™ ScanCentral DAST
  + Fortify WebInspect Enterprise
  + OpenText™ Core Software Composition Analysis (OpenText Core SCA)
  + Sonatype

<br/>

### [Fortify License and Infrastructure Manager (LIM)](https://www.microfocus.com/documentation/fortify-core-documents/2520/LIM_Guide_25.2.0.pdf)
+ It enables you to centrally manage the concurrent licenses for your OpenText Fortify products.

<br/>

### Additional Components
#### [Fortify on Demand (FoD)](https://www.microfocus.com/documentation/fortify-on-demand/)
+ An AppSec as a service offering that provides access to Fortify's tools and services.

#### [Application Security Aviator](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/2520/sast-aviator-ugd-html-25.2.0/index.htm)
+ It's a cloud-based enterprise service security tool that executes fast auditing, identification, and automated code fix suggestions for SAST vulnerabilities with the power of AI.
+ It embeds into your dev environment, using AI code analysis to continuously monitor code for security issues, and provides contextual explanations and tailored code fix suggestions.

#### Sonatype
+ Fortify integrate with Sonatype product to make the Fortify Source Components Analysis (SCA) scan
+ [How to integrate Sonatype with Fortify](https://help.sonatype.com/en/sonatype-fortify-ssc.html)
  + [Jar-File](https://marketplace.opentext.com/cybersecurity/content/sonatype-for-fortify-ssc)

<br/>

## Installation Best Practice
+ For a good performance, you should install the Fortify Components on separate virtual machines
  + Fortify Software Security Center (SSC)
  + LIM and DAST Components
  + Database for SSC and DAST
  + WebInspect
    + Can be installed on Penetration testing team machines
  + WebInspect Enterprise
    + Can be installed on manager/team-lead of Penetration testing team
  + SAST Controller
  + SCA sensor, client and Plugins
    + Can be installed on developer and CI/CD tools machines
