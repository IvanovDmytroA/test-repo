---
parser: v2
auto_validation: true
time: 10
primary_tag: software-product>sap-ai-core
tags: [ tutorial>intermediate, software-product>sap-ai-core ]
author_name: Dmytro Ivanov
author_profile: https://github.com/IvanovDmytroA
---

# Get an Overview of Building and Maintaining SaaS Applications on SAP BTP, ABAP Environment
<!-- description -->  Overview of developing and deploying custom SaaS applications on SAP Business Technology Platform, ABAP Environment

## Prerequisites
- You've purchased entitlements that are necessary for the account setup. See [Prepare](https://help.sap.com/docs/BTP/65de2977205c403bbc107264b8eccf4b/3bf575a3dc5043f895f8bd411d2a86a1.html?locale=en-US&version=Cloud#loio4338854e3133407abb47d3a281dbd1e1)
- You've registered a namespace at SAP, for example /NAMESPC/. See [Register a Namespace](https://help.sap.com/docs/BTP/65de2977205c403bbc107264b8eccf4b/3bf575a3dc5043f895f8bd411d2a86a1.html?locale=en-US&version=Cloud#loiocc5a3c6f78cf4889960c314dd09a5060)
- You've registered your add-on at SAP, for example /NAMESPC/PRODUCTX. See [Register a Product](https://help.sap.com/docs/BTP/65de2977205c403bbc107264b8eccf4b/dc15fb4ebab5453fa4641b98190b1f85.html?locale=en-US&version=Cloud)
- You've created a technical communication user, for example with the credentials ID `TechUserAAKaaS`.
- You've created a technical platform user as a space member in Cloud Foundry, for example with the credentials ID `CFPlatform`.
- You've configured an ASP\_CC destination for cloud controller access in the 05 Provide subaccount in the global account for development based on the credentials of the technical Cloud Foundry platform user. See [Create a Destination for the Cloud Foundry Cloud Controller Access](https://help.sap.com/docs/BTP/65de2977205c403bbc107264b8eccf4b/35b5acbb32024aa6b90a22e9f957a9f6.html?locale=en-US&version=Cloud).
## You will learn
- About SaaS enablement of an add-on product on SAP Business Technology Platform, ABAP Environment
- The End-End process for building and deploying an add-on product on ABAP Environment and providing it as multitenant application
## Intro

This tutorial is intended as an overview of the necessary steps, and provides a sneak-peak into the overall process. Detailed steps on how to achieve this will be covered in further tutorials of this mission.

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

``` python
AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","serviceurls":{"AI_API_URL":"..."}}'
```

The SDK parses the service key from the environment variable to interact with the AI Core service. 


• Optionally, set the AICORE_HOME environment variable to override the default config path.

• Install the required packages:

``` python
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