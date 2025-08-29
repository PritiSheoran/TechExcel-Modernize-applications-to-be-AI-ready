# Challenge 06: Configure an AI Hub and Prompt Flow
### Estimated Time: 90 Minutes
## Introduction

There are a lot of ways to create a chatbot. For this challenge, you will use Prompt Flow within Azure AI Studio. Azure OpenAI Prompt Flow is a development tool designed to streamline the entire lifecycle of AI applications powered by Large Language Models (LLMs). Prompt Flow simplifies the process of prototyping, experimenting, iterating, and deploying AI applications.

Here is a simple overview of each service used:

- An **Azure AI Hub** is a central resource within Azure AI Studio that helps teams manage, collaborate, and organize their AI projects.
- A **flow** encapsulates the logic that tells the chatbot what it can do and how to do things. Creating a flow can be complicated. For this challenge, you will use a pre-built flow. The flow uses the OpenAI API to query the Azure Search Index directly.

## Challenge Objectives:

> **Important**: When deploying services in this challenge, please make sure to use the resource group named **ODL-app-hack-xxxxxxx-Appmod** and use the same region as the resource group.

1. **Create a PostgreSQL user and set up an AI Hub and Prompt Flow:**

   - Navigate to the **Cloud Shell** terminal in the Azure portal and select **Bash**.
   - Enter the given commands at the Cloud Shell prompt to connect to the **database**.
      ```
      export PGHOST="POSTGRESQL_ENDPOINT"
      export PGUSER="contosoadmin"
      export PGPORT="5432"
      export PGDATABASE="pycontosohotel"
      export PGPASSWORD="1234ABcd!"
      psql
      ```   
   - Create a user for the **PostgreSQL database** that allows the chatbot read-only access to the Hotels, Visitors, and Bookings tables.

     ```
     CREATE USER promptflow WITH PASSWORD '1234ABCD!';
     ```

   - Grant the user access to the database tables with the following commands:
      ```
      GRANT SELECT ON TABLE hotels TO promptflow;
      GRANT SELECT ON TABLE bookings TO promptflow;
      GRANT SELECT ON TABLE visitors TO promptflow;
      GRANT EXECUTE ON FUNCTION getroomsusagewithintimespan TO promptflow;
      ```
   - From the Azure portal, Within the Foundry, create a new AI Hub, ensuring you assign a unique name to the AI Hub, AI Service and the associated Storage Account during the setup process.
   - Assign the **Storage Blob Data Owner** role to the current azure user.
   - Assign the **Storage Blob Data Reader** role to the **AI Hub**.

1. **Import and Configure a Flow:** 

   - Create a new project named **contosopf** on the AI Hub.
   - Navigate to Prompt Flow and create a new Chat flow by uploading the **chatflow-oai-datasources.zip** file, which is          located inside the **AssetsRepo/Assets** folder.
   - Import a pre-built flow into the project. 
   - Add a new connection to the external assets using **Custom keys** and **Azure AI Search**.
   - For the OpenAI service that was created during the AI Hub setup, deploy the **GPT-4o** model
   - Start the **Compute session**, then configure the flow by specifying the required connection details, including the  AI search name, OpenAI name, and setting the search_index name to brochures-vector.
   - Test the flow by entering the prompts `Where can I ski?` and `How many free rooms do hotels in Switzerland have grouped by hotel on 2024-10-10?` in the chat. View the results returned by the flow.

1. **Deployment of Configured Prompt Flow**

   - Deploy the Prompt Flow as a managed online endpoint for real-time inference with the Virtual Machine SKU of **Standard_D2a_v4**.

     > **Note**: This may take up to 10-15 minutes to deploy.   

   - Copy the endpoint **target URL** and **primary key** into a notebook.

1. **Configure Network Security Group Rules for External Access**

   - In the **Network Security Groups (NSG)** of the GPU Virtual Machine, configure **Inbound Security Rules** to allow traffic for the hackathon setup:  

      - Open the port `9000` with the `TCP` protocol and set the priority to `100`.  
      - Open the port `50551` with the `TCP` protocol and set the priority to `101`.  

   - Copy the **Public IP** of the GPU Virtual Machine into a notebook. 
     
   - Open a new tab in the browser and navigate to the following URL to verify if the service is ready to handle inference requests.

      ```
      http://<nvidia-gpu-public-ip>:9000/v1/health/ready
      ```

      ![](../../media/web-trigger.png)

1. **Setting Up and Running the AI-Powered Speech-to-Text and Chat Application** 

   - **Clone the repository** in Visual Studio Code: `https://github.com/CloudLabsAI-Azure/NVIDIA-Speech-to-text.git`.  
      > **Hint**: You can use the provided repository https://github.com/CloudLabsAI-Azure/NVIDIA-Speech-to-text to explore and perform the below-mentioned scenarios.  

   - **Install the prerequisites** from `requirements.txt`.  
      > **Hint**: Use `pip` to install the dependencies from `requirements.txt`.  

   - **Update the `.env` file** with the following details:  
      - **Public IP** of the GPU Virtual Machine  
      - **Target URI** of the endpoint  
      - **Primary Key**  

   - **Run the application** by executing `app.py` using Python.  

   - This application **supports both Riva Speech-to-Text and Chat with Azure AI services** running in **Prompt Flow**, which has been deployed.  

   - You can use **pre-created sample audio** files from the `audio` folder, such as:  
      - `Sample 1.wav` with the question **"Where can I ski?"**  
      - `Sample 2.wav` with the question **"How many free rooms do hotels in Switzerland have, grouped by hotel, on 2024-10-10?"**  

   - Alternatively, you can **record your own audio** and send it.

1. **Containerizing and Deploying the AI-Powered Speech-to-Text and Chat Application**  (Optional task)

- Package the application into a container and push it to **Azure Container Registry (ACR)**.  
- Create **Container Apps** for the AI-Powered Speech-to-Text and Chat Application.

## Success Criteria:

- You have successfully created a user in the PostgreSQL database.
- You have created a new Azure AI Hub and created a project.
- You have imported and configured a pre-built flow.
- You have tested the flow and confirmed that it returns appropriate results.
- Configure the necessary network security group rules to allow external access.
- Deploy the configured Prompt Flow to a virtual machine.
- Configure the necessary network security group rules to allow external access.
- Verify that the NVIDIA Riva ASR service is running and can handle inference requests.

## Additional Resources:

-  Refer to the [Secure Azure Database for PostgreSQL](https://learn.microsoft.com/en-us/training/modules/secure-azure-database-for-postgresql/) to learn about the security features of Azure Database for PostgreSQL.
-  Refer to [Get Started with Prompt Flow](https://learn.microsoft.com/en-us/training/modules/get-started-prompt-flow-ai-studio/) to learn about how to use Prompt Flow to develop applications that leverage language models in the Azure AI Foundry.
-  Refer to the [Deploy a Flow in Prompt Flow](https://learn.microsoft.com/en-us/azure/machine-learning/prompt-flow/how-to-deploy-for-real-time-inference?view=azureml-api-2) to learn about how to deploy a flow as a managed online endpoint for real-time inference.
- [az network nsg rule](https://learn.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)

