# Challenge 01: Deploy Azure OpenAI Service and LLM Models
### Estimated Time: 30 Minutes
## Introduction

Welcome to the Deploy Azure OpenAI Service Challenge! This challenge tests your skills in deploying the Azure OpenAI Service and its Large Language Models (LLMs). The goal is to set up the OpenAI Service and deploy LLM models.

**Azure OpenAI Service** provides REST API access to OpenAI's powerful language models, including the GPT-4, GPT-4 Turbo with Vision, `GPT-4o`, and Embeddings model series. In addition, the new `GPT-4` and ` GPT-4o` model series have now reached general availability.

A **Large Language Model (LLM)** is a deep learning algorithm that can perform various Natural Language Processing (NLP) tasks. Large language models use transformer models and are trained using massive datasets—hence, large. This enables them to recognize, translate, predict, or generate text or other content.

**Contoso Ltd.**, a leading technological firm, seeks to enhance its product support operations. They receive many queries daily, resulting in longer waiting times and decreased customer satisfaction. To address this, Contoso plans to implement an AI-powered solution that can handle customer inquiries effectively and efficiently.

They have chosen to deploy Azure OpenAI Service and its Large Language Models (LLM), like ` GPT-4o` and `text-embedding-ada-002.` These models are known for their capability of processing and generating human-like text, making them ideal for this application.

As a part of this challenge, your task is to create an Azure OpenAI service and deploy Large Language Models (LLM). The Large Language Models include **GPT-4o** and **text-embedding-ada-002**.

### Accessing the Azure Portal

1. To access the Azure portal, open a private/incognito window in your browser and navigate to the Azure Portal.

1. On the **Sign in to Microsoft Azure tab**, you will see a login screen. Enter the following email/username, then click on **Next**.

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>

1. Now, enter the following password and click on **Sign in**.

   - **Password:** <inject key="AzureAdUserPassword"></inject>

1. If you see the pop-up **Stay Signed in?**, click No.

1. If you see the pop-up, **"You have free Azure Advisor recommendations!"** close the window to continue with the challenge.

1. If a **Welcome to Microsoft Azure** pop-up window appears, click **Maybe Later** to skip the tour.

## Prerequisites

Make sure you have the following from the CloudLabs-provided integrated environment:

> **Note**: Prerequisites are already set up in the CloudLabs-provided environment. If you are using your personal computer or laptop, please install all necessary prerequisites to complete this hackathon.

  - [Azure Subscription](https://azure.microsoft.com/en-us/free/)
  - [Azure OpenAI](https://aka.ms/oai/access) access is available with the following models:
    - GPT-4o
    - text-embedding-ada-002

## Challenge Objectives:

>**Note:** When deploying services in this challenge, please make sure to use the resource group named **Appmod.** 

1. **Azure OpenAI Service Deployment:**
   
   - Set up an Azure OpenAI Service instance with SKU size Standard `S0`.
   - Deploy it in the existing resource group named - **Appmod.**
   - Deploy the resource in the **East US** region.
   - Obtain the necessary Azure OpenAI Key and Endpoint.
   - Please ensure the Azure OpenAI Service name follows this format: **OpenAI-xxxxxx**, where xxxxxx should be replaced with your specific **Deployment ID**.    

      <validation step="	e958ee57-1b51-469f-86a0-44889bee9648"/>

1. **Deploy Large Language Models (LLM):**
   
   - Azure OpenAI provides a web-based portal named **Azure OpenAI Studio** that you can use to deploy, manage, and explore models. You'll start your exploration of Azure OpenAI by using Azure OpenAI Studio to deploy a model.
   - Launch Azure OpenAI Studio from the overview pane and deploy two OpenAI models, i.e., `GPT-4o ', and `text-embedding-ada-002` with a TPM capacity of 20k.   

## Success Criteria:

- Verify that the Azure OpenAI Service is successfully deployed in the existing resource group named **Appmod**.
- Verify that the Large Language Models (LLM), `GPT-4o ', and `text-embedding-ada-002` are successfully deployed with the Azure OpenAI Service.

## Additional Resources:

- Refer to the [Azure OpenAI Service documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/) for guidance on deploying the service.   
