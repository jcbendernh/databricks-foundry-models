# Calling AI Foundry Models from Azure Databricks

## Overview
There has been much discussion of being able to call AI Foundry models from Azure Databricks.  The reason for this is that there may be a model deployed in AI Foundry that is not listed as a registered model within Unity Catalog.  

In this repo, I have created 2 Notebooks that show how you call a deployed model in AI foundry in 2 different methods.

### Call Foundry Notebook
Calls an Azure AI Foundry model deployment directly from a Databricks notebook, bypassing Databricks Model Serving entirely. It acquires an Azure AD token via service principal credentials (client_credentials flow against https://cognitiveservices.azure.com/.default) stored in an Azure Key Vault-backed secret scope, then uses the AzureOpenAI client to send a chat completion request to the Foundry resource endpoint. The service principal requires the Cognitive Services OpenAI User RBAC role on the Foundry resource.

Use case: Workaround for environments where Databricks Model Serving cannot resolve KV-backed secrets for Entra token acquisition when calling Azure AI Foundry endpoints. 

Prerequisite: The service principal utilized must have the following RBAC role in the AI Foundry Resource: **Cognitive Services OpenAI User**

### Register Foundry Model - API
Registers an Azure AI Foundry model deployment as a Databricks external model serving endpoint using the MLflow deployments client (mlflow.deployments). Authentication uses standard Azure API key auth with the key stored in a Databricks-backed secret scope - required because Model Serving cannot resolve Azure Key Vault-backed secrets via a Service Principal. The notebook walks through three steps: (1) create the endpoint, (2) verify it reaches READY state, and (3) validate it with a sample ai_query() SQL call. A utility cell is included to delete and recreate the endpoint if the configuration needs to change.

Prerequisites: A Databricks-backed secret scope containing the Foundry API key, and a deployed model in Azure AI Foundry with a known deployment name and API version.  AI Foundry has api key authentication enabled.


## To get started, please perform the following:
1. Using Databricks Git Integration with Git folders, you can import these notebooks into your Databricks workspace. To do so, clone this repository to your GitHub environment and add your cloned repository via Git folders. For more on this procedure, see [Azure Databricks Git folders](https://learn.microsoft.com/en-us/azure/databricks/repos/).

2. For the example notebooks, I deployed the gpt-5.6-luna model and it's deployment name was gpt-5.6-luna-deployment-name.  You will need to deploy a model within your AI Foundry project and capture its details.