## What is Fortify?
+ Fortify is a comprehensive application security (AppSec) platform developed by MicroFocus (OpenText now).
+ It designed to help organizations identify, assess, and remediate vulnerabilities in their software. It offers a range of tools and services, including static and dynamic application security testing (SAST and DAST), software composition analysis (SCA), and application security management.
+ Fortify aims to integrate security into the software development lifecycle (SDLC) and empower DevSecOps practices.

<br/>

## Key Components and Features
> There are 3 main features offered by OpenText Fortfiy

**Dynamic Application Security Testing (DAST)**
+ It helps organizations identify and remediate security vulnerabilities in their web applications. Fortify DAST conducts dynamic analysis by simulating attacks against running web applications to detect common security flaws such as SQL injection, cross-site scripting (XSS), insecure authentication mechanisms, and more.

**Static Application Security Testing (SAST)**
+ It's a method used to analyze and evaluate the security of software by examining the source code of an application. Unlike dynamic testing, which examines running applications, SAST tools work on static (non-running) code.
+ It helps developers and security teams identify vulnerabilities early in the software development life cycle (SDLC) by scanning source code for security issues — before the application is ever run.

**Software Composition Analysis (SCA)**
+ Helps organizations manage the security risks associated with third-party and open-source components.

<br/>

> Good, Let's talk about components of every feature
### Dynamic Application Security Testing (DAST)
+ [OpenText™ ScanCentral DAST](https://www.microfocus.com/documentation/fortify-ScanCentral-DAST/)
  + ScanCentral DAST Global Service
  + ScanCentral DAST API
  + ScanCentral DAST Utility Service
  + ScanCentral DAST database
  + OpenText DAST sensor
+ OpenText™ Dynamic Application Security Testing (OpenText DAST) [Fortify WebInspect](https://www.microfocus.com/documentation/fortify-webinspect/)
+ [Fortify WebInspect Enterprise](https://www.microfocus.com/documentation/fortify-webinspect-enterprise/)

<br/>

### Static Application Security Testing (SAST)
+ OpenText™ Static Application Security Testing (OpenText SAST) [Static Code Analyzer (SCA)](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/)
+ [OpenText™ Fortify ScanCentral SAST](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/)
  + Fortify ScanCentral SAST Controller
  + Fortify ScanCentral SAST sensors
  + Fortify ScanCentral SAST client
+ [Tools](https://www.microfocus.com/documentation/fortify-static-code-analyzer-and-tools/2520/sast-tgd-html-25.2.0/index.htm#GetStarted/sca-apps-tools.htm?TocPath=Getting%2520Started%257C_____2)
  + sourceanalyzer
  + Audit Workbench (AWB)
  + Audit Assistant (AA)
  + IDE Plugins
    + SCA Plugin [Eclipse, IntelliJ, and Visual Studio, VS Code]
    + Fortify Security Assistant Extension [Eclipse, IntelliJ, and Visual Studio, Android Studio]
  + Fortify Scan Wizard
  + Fortify Custom Rules Editor
  + FPRUtility
  + fortifyclient

<br/>

### Software Composition Analysis (SCA)



<br/>

### [Fortify Software Security Center (SSC)] [SSC Documentation](https://www.microfocus.com/documentation/fortify-software-security-center/)
+ It's a browser-based application that provides a set of capabilities across the software development life cycle to automate detection of security vulnerabilities in applications. It helps your security and development teams work together to resolve security flaws quickly and accurately by making correlated data available from the following products:
  + OpenText™ Static Application Security Testing (OpenText SAST)
  + OpenText™ Fortify ScanCentral SAST
  + OpenText™ ScanCentral DAST
  + Fortify WebInspect Enterprise
  + OpenText™ Core Software Composition Analysis (OpenText Core SCA)
  + Sonatype

<br/>

> Additional Components
#### Fortify on Demand (FoD)
+ An AppSec as a service offering that provides access to Fortify's tools and services.

#### Application Security Aviator
+ An AI code security tool that executes fast auditing, identification, and automated code fix suggestions for SAST vulnerabilities with the power of AI.
+ It embeds into your dev environment, using AI code analysis to continuously monitor code for security issues, and provides contextual explanations and tailored code fix suggestions.




