# Calling Azure AI Foundry Models from Azure Databricks

## Overview
There has been much discussion of being able to call AI Foundry models from Azure Databricks.  The reason for this is that there may be a model deployed in AI Foundry that is not listed as a registered model within Unity Catalog. 

This repository demonstrates two ways to call deployed Azure AI Foundry models from Azure Databricks.

## Notebooks

### 1) Call Foundry Deployment Directly
This notebook calls an Azure AI Foundry deployment directly from a Databricks notebook, without using Databricks Model Serving.

It acquires a Microsoft Entra ID token using service principal credentials (client credentials flow for `https://cognitiveservices.azure.com/.default`) stored in an [Azure Key Vault-backed secret scope](https://learn.microsoft.com/en-us/azure/databricks/security/secrets/#akv-ss), then uses the `AzureOpenAI` client to submit a chat completion request to the Foundry endpoint.

Use case:
Workaround for environments where Databricks Model Serving cannot resolve Key Vault-backed secrets for Entra token acquisition when calling Azure AI Foundry endpoints.

Prerequisite:
An Azure Service Principal created that must have the **Cognitive Services OpenAI User** RBAC role on the Azure AI Foundry resource.

### 2) Register Foundry Model - API/Resource Key
This notebook registers an Azure AI Foundry deployment as a Databricks external model serving endpoint using the MLflow deployments client (`mlflow.deployments`).

Authentication uses an Azure Foundry Resource key stored in a Databricks-backed secret scope. This is required because Model Serving cannot authenticate an Azure Service Principal in this scenario.

The notebook covers three steps:
1. Creates the endpoint.<BR>When completed, you will see the model name in the Serving Endpoints listing within Azure Databricks.<BR>
[Create custom model serving endpoints](https://learn.microsoft.com/en-us/azure/databricks/machine-learning/model-serving/create-manage-serving-endpoints)
2. Verifies the endpoint.
3. Validates the endpoint with a sample `ai_query()` SQL call.

A utility cell is also included to delete and recreate the endpoint when configuration updates are needed.

Prerequisites:
- A Databricks-backed secret scope that contains the Foundry API key.<BR>
[Create a Databricks-backed secret scope](https://learn.microsoft.com/en-us/azure/databricks/security/secrets/#create-a-databricks-backed-secret-scope)
- A deployed model in Azure AI Foundry with a known deployment name and API version.<BR>
[Deploy Microsoft Foundry Models in the Foundry portal](https://learn.microsoft.com/en-us/azure/foundry/foundry-models/how-to/deploy-foundry-models)
- API key authentication enabled in Azure AI Foundry.<BR>
[Authenticate with a Foundry resource key](https://learn.microsoft.com/en-us/azure/ai-services/authentication#authenticate-with-a-foundry-resource-key)

## Getting Started
1. Import these notebooks into your Databricks workspace using Databricks Git folders.  Clone this repository to your GitHub account and add it in Databricks via Git folders. For details, see [Azure Databricks Git folders](https://learn.microsoft.com/en-us/azure/databricks/repos/).
2. Update the notebook settings with your own deployed model details (deployment name, endpoint, API version, and authentication values).

## Example Model in This Repo
The examples were originally authored using a `gpt-5.6-luna` deployed model within AI Foundry.

You should deploy a model in your own Azure AI Foundry project and replace the sample values with your environment-specific details.
