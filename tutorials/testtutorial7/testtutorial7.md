---
title: Prepare Your Development Environment for SAP Fiori Elements
description: Set up your development environment with SAP Business Application Studio to create an SAP Fiori elements application based on the SAP Cloud Application Programming Model.
auto_validation: true
time: 20 minutes
tags: [ software-product-function>sap-fiori-elements, software-product-function>sap-fiori-tools, tutorial>beginner, software-product-function>sap-fiori, software-product-function>sap-business-application-studio, software-product-function>sap-cloud-application-programming-model ]
primary_tag: products>sap-fiori
contributors: [ Hitesh Parmar>https://github.com/hitesh-parmar, Joachim Fiess>https://github.com/jo-fiess ]
---
## Prerequisites
- You need a trial account on SAP Business Technology Platform. If you don't have one, follow the instructions in: [Get a Free Account on SAP BTP Trial](hcp-create-trial-account).
- Ensure that you have started SAP Business Application Studio in your SAP Business Technology Platform trial account. For detailed instructions: [Set Up SAP Business Application Studio for Development](appstudio-onboarding).

## Details
### You will learn
- How to set up SAP Business Application Studio for SAP Fiori elements application development
- How to create a project containing the service needed for generating your sample application

Click [here](https://cap.cloud.sap/docs/about/) for more information about the SAP Cloud Application Programming Model.

---

### Learn about the global accounts and subaccounts needed

The following figure shows the system landscape recommended for developing, operating and maintaining SaaS applications on SAP Business Technology Platform, ABAP Environment
![System Landscape](systemLandscape.png)
1. Global Account for Development
- 01 Develop - Holds the development system (DEV) used for continuous development and the correction system (COR) used to develop bug fixes and patches for already released versions of your add-on
- 02 Test - Contains a test system (TST) for testing the changes released from development and the quality assurance system (QAS) to test the bug fixes and patches for already released versions of your add-on  
- 03 Build/Assemble - Holds the system created for the add-on assembly. Software components are imported into this system, ATC checks executed, delivery packages for the add-on created
- 04 Build/Test - Holds the system created for add-on installation test performed after add-on build
- 05 Provide - Contains a production like system (AMT) to test multi-tenancy aspects of the SaaS solution (client separation, data isolation...)
- 06 Consume - Consumer subaccounts for facilitating production like tests
![Global Account For Development](GlobalAccountForDev.png)
2. Global Account for Production  
- 05 Provide - Provider subaccount for production use - Contains various services for SaaS enablement, ABAP system (AMT) provisioned via ABAP Solution service
- 06 Consume - Consumer subaccounts for production - Each of your customers is assigned a dedicated consumer subaccount
![GlobalAccountForProd](GlobalAccountForProd.png)
The Landscape Portal for SAP BTP ABAP Environment booster automates the setup of access to the Landscape Portal, including all configurations required to use the Build Product Version app. Upon execution, three subaccounts will be created: One subaccount will contain Landscape Portal and CI/CD subscriptions, while the other two can be used during the assembly and test installation stages of the product build process. For more information, see Landscape Portal for ABAP Environment Booster.

### Set Up Your Environment and Configure Access

[OPTION BEGIN [AI Launchpad]]

•	Open AI Launchpad.

•	Connect to your instance using your credentials.

•	Navigate to the desired Resource Group where you plan to deploy the orchestration.

For the detailed step follow the tutorial - Setup Generative AI Hub in SAP AI Launchpad

[OPTION END]

[OPTION BEGIN [Python SDK]]

**Installing sap-ai-sdk-gen**

To install the **SAP Cloud SDK for AI (Python) - generative package** in your system, open your terminal or command prompt and run the following command.

``` python
pip install sap-ai-sdk-gen
```

Once the package is installed, you need to configure proxy modules to use the large language models. We recommend setting these values as environment variables for AI Core credentials via a configuration file. The default path for this file is ~/.aicore/config.json.

Open Notepad and replace the placeholder values in the JSON file with your AI Core service keys, which you downloaded from BTP. Save the file by pressing Command + S. When prompted, navigate to ~/.aicore/ and save the file as config.json.

The configuration file should be:

![img](img/image005.png)

[OPTION END]

[OPTION BEGIN [JavaScript SDK]]

• [Create a service key](https://help.sap.com/docs/sap-ai-core/sap-ai-core-service-guide/create-service-key) for your AI Core service instance and copy the generated JSON object.  

• Set the copied service key as the `AICORE_SERVICE_KEY` environment variable in your local environment. Maintaining a single-line format will prevent parsing errors.

``` 
AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","serviceurls":{"AI_API_URL":"..."}}'
```

The SDK parses the service key from the environment variable to interact with the AI Core service. 


• Optionally, set the AICORE_HOME environment variable to override the default config path.

• Install the required packages:

``` 
  npm install @sap-ai-sdk/ai-api @sap-ai-sdk/orchestration dotenv
```

• For detailed setup and usage,refer to the official [GitHub repository](https://github.com/SAP/ai-sdk-js/tree/main?tab=readme-ov-file#sap-ai-sdkorchestration) of **SAP Cloud SDK for AI**.


• For detailed installation and usage of the **SAP Cloud SDK for AI (JavaScript)**, visit the official [GitHub repository](https://github.com/SAP/ai-sdk-js) and [Documentation](https://sap.github.io/ai-sdk/). This page provides comprehensive steps to set up, integrate and test the SDK effectively in your projects.
 
**Tip:** 

• Ways to load environment variables might vary based on the framework you are using.

• For example, while the SAP Cloud SDK for AI (JavaScript) uses the [dotenv](https://www.npmjs.com/package/dotenv) library to load environment variables, NextJS uses a [specific configuration](https://nextjs.org/docs/pages/building-your-application/configuring/environment-variables) to load them.

• Installing JavaScript Kernel for Jupyter Notebooks: If you want to use JavaScript in Jupyter Notebooks, you can refer to [Deno v1.37 blog post](https://deno.com/blog/v1.37) for detailed steps to install the Javascript kernel. Follow the instructions provided to set up the environment and enable JavaScript support in Jupyter.  

[OPTION END]

[OPTION BEGIN [Java SDK]]

• [Create a service key](https://help.sap.com/docs/sap-ai-core/sap-ai-core-service-guide/create-service-key) for your AI Core instance and copy the JSON object.

• Create a `.env` file in the **working directory from which you run the code**. Add the following line using the copied JSON:
  
```dotenv
AICORE_SERVICE_KEY={"clientid": "...", "clientsecret": "...", "url": "...", "serviceurls": { "AI_API_URL": "..." } }
```

• **IMPORTANT:** The value of `AICORE_SERVICE_KEY` must be a single line, so remove any line breaks from the service key JSON.

• This tutorial is designed for a Java [maven project](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html). Add the following dependencies to your project `pom.xml` file:

```xml
<dependency>
    <groupId>com.sap.ai.sdk</groupId>
    <artifactId>core</artifactId>
    <!-- Use the latest version here -->
    <version>${ai-sdk.version}</version>
</dependency>

<dependency>
    <groupId>com.sap.ai.sdk</groupId>
    <artifactId>orchestration</artifactId>
    <!-- Use the latest version here -->
    <version>${ai-sdk.version}</version>
</dependency>
```

• For other options of access configuration and detailed information on installation and usage of the **SAP Cloud SDK for AI (Java)**, visit the official [documentation platform](https://sap.github.io/ai-sdk/docs/java/overview-cloud-sdk-for-ai-java). This page provides comprehensive steps to set up and integrate the SDK effectively in your projects.

[OPTION END]
