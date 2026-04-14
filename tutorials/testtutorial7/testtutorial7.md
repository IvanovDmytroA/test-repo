---
title: Set Up a CI/CD Pipeline for SAP BTP, Kyma Runtime 7
description: This section describes how to configure and run a predefined continuous integration and delivery (CI/CD) pipeline that automatically tests, builds, and deploys your code changes to speed up your development and delivery cycles.
keywords: cap 
parser: v2
auto_validation: true
time: 60
tags: [ tutorial>beginner, software-product-function>sap-cloud-application-programming-model, programming-tool>node-js, software-product>sap-business-technology-platform, software-product>sap-fiori]
primary_tag: software-product-function>sap-cloud-application-programming-model
author_name: Svetoslav Pandeliev
author_profile: https://github.com/slavipande
---

# Integrate SAP BTP ABAP Environment and SAP S/4HANA Cloud, public edition using the OAuth 2.0 SAML Bearer Assertion Flow

<!-- description --> Integrate SAP BTP, ABAP environment and SAP S/4HANA Cloud, public edition using the OAuth 2.0 SAML Bearer Assertion Flow. This tutorial takes an existing integration based on Basic Authentication and configures the more complex authentication mechanism to enable the usage of principal propagation.

## Prerequisites

- This is the third tutorial of this tutorial group. Please complete the tutorial [Implement an Outbound Service Call in SAP BTP ABAP environment](abap-environment-business-partner-outbound-call) and the tutorial [Integrating an SAP BTP, ABAP Environment and an SAP S/4HANA Cloud, public edition System using Basic Authentication](abap-environment-business-partner-basic-auth) before proceeding. The prerequisites of the previous tutorials apply.
- The custom communication scenario that is used must be enabled for outbound connectivity using **OAuth 2.0**, with **SAML 2.0 Bearer Assertion** as the grant type.

## You will learn

- How to configure **OAuth 2.0** for outbound connectivity.
- How to configure an **OAuth 2.0 Identity Provider** for inbound connectivity.
  
## Intro

>In this tutorial, all activities on S/4HANA Cloud side will be performed in the **customizing tenant** (100) of your **development system**.

### Understand the OAuth 2.0 SAML Bearer Assertion Flow

The **OAuth 2.0 SAML Bearer Assertion** flow works as follows:

At design time, the OAuth 2.0 client, in our case a system in the SAP BTP, ABAP environment, is signed up to the authorization server, in our case the SAP S/4HANA Cloud, public edition system. The authorization server provides the clients credentials.

At run time, the OAuth 2.0 client sends the following information to the token endpoint of the authorization server (1):

- SAML Bearer Assertion (authorization grant)
- Client ID
- Client secret or client certificate

The authorization server authenticates the OAuth 2.0 client with its credentials (client id and client secret or client certificate), validates the SAML Bearer assertion, and, if the SAML Bearer assertion is validated successfully, the authorization server issues an access token (2).  

The OAuth 2.0 client sends the access token to the resource server – in our case the SAP S/4HANA Cloud, public edition system – to access the protected resource (3). The protected resource validates the access token and, if valid, grants access.

![OAuth explanation](Oauth_explanation.png)

The SAML Bearer Assertion is an XML document. It contains the information about the principal to be propagated. This information is stored in the `<saml.Subject><saml:NameID>` element of the XML document. It provides the actual ID and its format.

To validate the SAML Bearer Assertion, the authorization server needs to trust the issuer of the SAML Bearer assertion – in our case, the system in the SAP BTP, ABAP Environment.

### Get OAuth 2.0 Endpoint Information

As an administrator in SAP S/4HANA Cloud, public edition system, you will derive the information of the endpoint and audience needed to set up the OAuth 2.0 communication.

1. Open the SAP Fiori Launchpad of your SAP S/4HANA Cloud, public edition system

2. Open the **Communication Systems** app and access the **Own SAP Cloud System**. You can filter for the **Own SAP Cloud System** via the **Adapt Filters** button.

    ![Own SAP Cloud System](own_sap_cloud_system.png)

3. In the General section copy the **OAuth 2.0 SAML2 Audience** and **OAuth 2.0 Confidential Client Token Service URL**. Store it for later use.

    ![Copy Credentials](copy_credentials.png)

### Add OAuth 2.0 Client to Communication System in SAP BTP ABAP environment

As an administrator in SAP BTP ABAP environment, adjust your communication system to support the **OAuth 2.0** authentication method for outbound connectivity.

1. Open SAP Fiori Launchpad of your SAP BTP ABAP environment system.  

2. Open the **Communication Systems** app and access Communication System `ZBPA2X_COM_SYS_S4H`

3. Choose **Edit**

4. In section **OAuth 2.0 Settings** set
<ol type="a">
	<li>Token Endpoint: **OAuth 2.0 Confidential Client Token Service URL** (derived in STEP 1)</li>
	<li>Audience: **OAuth 2.0 SAML2 Audience** (derived in STEP 1)

    ![Set Credentials](set_credentials.png)</li>
</ol>
	
5. In section **Users for Outbound Communication**
<ol type="a">
	<li>Choose `+`</li>
	<li>Choose Authentication Method **OAuth 2.0**</li>
	<li>Provide OAuth 2.0 Client ID: Username of communication user created in [Step 'Create a Communication User' of the previous tutorial](abap-environment-business-partner-basic-auth) (`ZBPA2X_COM_USER`)</li>
	<li>Provide Client Secret: Password of communication user created in [Step 'Create a Communication User' of the previous tutorial](abap-environment-business-partner-basic-auth). Here you reuse the communication user in order to sign up to the OAuth 2.0 client.

    ![Provide User Credentials](provide_user_credentials.png)</li>
	<li>Choose **Create**</li>
</ol>

6. Choose **Save** to save the communication system