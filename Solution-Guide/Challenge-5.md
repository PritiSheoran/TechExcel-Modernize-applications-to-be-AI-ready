# Challenge 06: Configure an AI Hub and Prompt Flow
### Estimated Time: 60 Minutes
## Introduction

There are a lot of ways to create a chatbot. For this challenge, you will use prompt flow within Azure AI Studio. Azure OpenAI prompt flow is a development tool designed to streamline the entire lifecycle of AI applications powered by Large Language Models (LLMs). Prompt flow simplifies the process of prototyping, experimenting, iterating, and deploying AI applications.

Here's a simple overview of each service used:

- An **Azure AI Studio Hub** is a central resource within Azure AI Studio that helps teams manage, collaborate, and organize their AI projects.
- A **flow** encapsulates the logic that tells the chatbot what it can do and how to do things. Creating a flow can be complicated. For this challenge, you will use a pre-built flow. The flow uses the OpenAI API to directly query the Azure Search index.

## Solution Guide

### Task 1: Create a PostgreSQL user and set up an AI Hub and Prompt Flow

In this task, you will create a hub and then create a project within the hub. You will also create a PostgreSQL user so that the flow can access the database records.

1. On the Azure home page, select the **PostgreSQL database** that you created in a previous challenge.

    ![](../media/h150.png)

1. Copy and paste the **Enpoint** that appears in the **Overview** section of your database server into Notepad.

    ![](../media/c6.task1.1.png)

1. On the pane bar for the database server, select the **Cloud Shell** icon.

    ![](../media/h152.png)

1. Then select **Bash** in the Cloud Shell pane.     

    ![](../media/h153.png)

1. Select your **Subscription** from the drop-down list **(1)** and then select **Apply (2)**.

    ![](../media/h154.png)

1. Replace the value for **POSTGRESQL_ENDPOINT** with the Endpoint name that appears in the Overview section for your database server that you copied in the previous step. Then, enter the following commands at the Cloud Shell prompt and press **Enter**. These commands connect to the database.   

   ```
   export PGHOST="POSTGRESQL_ENDPOINT"
   export PGUSER="contosoadmin"
   export PGPORT="5432"
   export PGDATABASE="pycontosohotel"
   export PGPASSWORD="1234ABcd!"
   psql
   ```

    ![](../media/h155.png)

1. Next to `pycontosohotel`, enter the following SQL statement at the Cloud Shell prompt and press **Enter**. This statement creates a read-only user for the prompt flow chatbot:

   ```
   CREATE USER promptflow WITH PASSWORD '1234ABCD!';
   ```

    ![](../media/h156.png)

1. Next to `pycontosohotel`, enter the following SQL statement at the Cloud Shell prompt and press **Enter**. These statements grant the user access to the database tables.

   ```
   GRANT SELECT ON TABLE hotels TO promptflow;
   GRANT SELECT ON TABLE bookings TO promptflow;
   GRANT SELECT ON TABLE visitors TO promptflow;
   GRANT EXECUTE ON FUNCTION getroomsusagewithintimespan TO promptflow;
   ```

    ![](../media/h157.png)

1. Navigate back to the **Azure portal**.

1. On the Azure Portal page, in the Search resources, services, and docs (G+/) box at the top of the portal, enter **Azure AI Foundry (1)**, and then select **Azure AI Foundry (2)** under **Services**.

    ![](../media/challenge6.task.1.png) 

1. In the left navigation pane for the AI Foundry, select **AI Hubs (1)**. On the AI Hubs page, click on **Create (2)** and select **Hub (3)** from the drop-down.

    ![](../media/challenge6.task.3.png) 

1. On the **Create an AI hub resource** pane enter the following details:

    - Subscription : **Leave default subscription** 
    - Resource Group : Select **Appmod (1)** 
    - Region : Use the same location as the resource group **(2)**
    - Name : Use the format **aihub-xxxxxx (3)** (replace **xxxxxx** with the **Deployment ID**) 

        ![](../media/challenge6.task.4.png) 

    - Connect AI Services incl. OpenAI : Click on **Create New (1)**
    - Connect AI Services incl. OpenAI : Provide a name to the AI Service ,Use the format **aiservice-xxxxxx (2)** (replace **xxxxxx** with the **Deployment ID**)  
    - Click on **Save (3)**, followed by **Next:Storage (4)**
    
        ![](../media/challenge6.task.5.png) 

        >**Note**: Here, xxxxxx refers to the **deployment ID** which you can get from the Environment tab.

1. For **Storage account**, click on **Create new (1)**, provide a **Name** to the storage account, Use the format **storageaccountxxxxxx (2)** (replace **xxxxxx** with the **Deployment ID**)  and then click on **Save (3)**

     ![](../media/challenge6.task.6.png)

1. Then Select **Review + create > Create.**

    ![](../media/challenge6.task.7.png)
    ![](../media/challenge6.task.8.png)

1. Wait for the AI Hub deployment to complete.

1. On the Azure home page, select **Resource groups** and then select **Appmod**.

1. You should see two storage accounts. The first is the storage account that you created earlier in the lab. *The other was created by the AI Hub. Select the storage account that was created by AI Hub*.

    ![](../media/challenge6.task10.png)

     >**Note:** The name for the newly created storage account will start with **storageaccount**.

1. In the left navigation pane for the storage account, select A**ccess Control (IAM) (1)**. On the Access Control (IAM) page, on the **Grant access to this resource** tile, select **Add role assignment (2)**.

    ![](../media/challenge6.task11.png)

1. In the search field, enter **Storage Blob Data Owner (1)** and then select **Storage Blob Data Owner (2)** from the search results list. Select **Next (3)**.

    ![](../media/h165.png)

1. On the **Add role assignment** page, select **+Select members (1)**. In the Select members pane, search for your username provided in the Environment tab **(2)** and select your username **(3)**. Click on **Select (4)** to close the **Select members** pane.    

    ![](../media/challenge6.task12.png)

1. Then, select **Review + assign** twice.  

      ![](../media/challenge6.task13.png)

1. On the **Access Control (IAM)** page, on the **Grant access to this resource** tile, select **Add role assignment** to add a second role assignment.    

    ![](../media/challenge6.task11.png)

1. In the Search field, enter **Storage Blob Data Reader (1)** and then select **Storage Blob Data Reader (2)** from the search results list. Select **Next (3)**.

    ![](../media/h169.png)

1. On the **Add role assignment** page, select **+Select members (1)**. In the Select members pane, **search (2)** for and **select (3)** the name for the *AI Hub that you created in Step 12 of this task* and then choose **Select (4)**.

    ![](../media/challenge6.task14.png)

1. Then, select **Review + assign** twice.

    ![](../media/challenge6.task15.png)


### Task 2: Import and Configure a Flow 

A flow encapsulates the logic that tells the chatbot what it can do and how to do things. The flow uses the OpenAI API to directly query the Azure Search index.

In this task, you will import a pre-built flow, configure flow settings, and then test the flow.  

1. In the Azure portal, Navigate and select the AI Hub that is created in the previous task.

    ![](../media/aihub.png) 

1. On the Overview pane, click on **Launch Azure AI Foundry**. This will navigate you to the Azure AI Foundry portal.

    ![](../media/challenge6.task18.png)

1. Scroll down and click on **+ New project** on the Hub Overview. 

    ![](../media/project.png)

1. Provide the project name as **contosopf (1),** then click on **Create (2)**.

    ![](../media/h175.png)

1. In the left navigation pane for the project page, in the **Build and customize** section, select **Prompt flow**.

    ![](../media/challenge6.task21.png)

1. On the **Create, iterate, and debug your orchestration flows** page, select **+Create**.

    ![](../media/challenge6.task22.png)

1. On the **Create a new flow** page, in the **Upload from local** section, select **Upload**.

    ![](../media/h178.png)

1. On the **Upload from local** page, select **Zip file (1)** and then select **Browse (2)**.      

    ![](../media/h179.png)

1. Go to the `C:/Users/demouser/AssetsRepo/Assets` folder and press **Enter.**

    ![](../media/h180.png)

1. Select **chatflow-oai-datasources..zip (1)** and then select **Open (2)**.    

    ![](../media/h181.png)

1. Under **Select flow** type, select **Chat flow (1)**. Then, choose **Upload (2)** to import the zip file into the project. 

    ![](../media/h182.png)

     >**Note:** It may take several minutes to upload the flow. Separately, if the Upload button becomes available again, keep clicking on the Upload button.  

1. This will load the prompt flow once uploaded. In the middle pane for the flow, you will see one flow for each of the four logical steps in the flow. Review the information in each tile. This will help you understand how the flow functions.

    ![](../media/h183.png)

1. Navigate back to the **Azure portal.**

1. On the Azure home page, select **Resource groups** and then select **Appmod**.

1. Select the PostgreSQL database that you created in a previous challenge.

    ![](../media/h150.png)

1. In the **Overview section** for the database, copy and paste the value of the **Endpoint** into Notepad. You will pass the value into a field in Step 22 of this task. 

    ![](../media/c6.task1.1.png)

1. Return to the Azure AI Studio browser window.

1. In the left navigation pane for the flow, select **Management Center**.

    ![](../media/h184.png)

1. Select **Connected resources (1)** in the **Project (contosopf)** section and then click on **+ New connection (2)**.

    ![](../media/h185.png)

1. On the **Add a connection to external assets** page, search for **Custom keys (1)** and select **Custom keys (2)**.    

    ![](../media/h186.png)

1. Select **+ Add key value pairs**.

    ![](../media/h187.png)

1. Enter the following information and select **+ Add key value pairs (3)**.    

    | Field | Value |
    | -- | -- |
    | Custom keys | **hostname (1)**|
    | Value | **Use the Endpoint name you copied in Step 16 of this lab (2)**|    

     ![](../media/h188.png)    

1. Enter the following information.

    | Field | Value |
    | -- | -- |
    | Custom keys | **user**|
    | Value | **promptflow**|        

1. Select **+ Add key value pairs**.

1. Enter the following information.

    | Field | Value |
    | -- | -- |
    | Custom keys | **port**|
    | Value | **5432**|          

1. Select **+ Add key value pairs**.

1. Enter the following information.

    | Field | Value |
    | -- | -- |
    | Custom keys | **database**|
    | Value | **pycontosohotel**|        

1. Select **+ Add key value pairs**.

1. Enter the following information.  

    | Field | Value |
    | -- | -- |
    | Custom keys | **passwd**|
    | Value | **1234ABCD!**| 
    | Is Secret | **Selected**|

     ![](../media/challenge6.task23.png)

1. In the *Connection name* field, enter **PostgreSQL (1)** and select **Add connection (2)**.

    ![](../media/h191.png)

1. Select **+ New Connection** again.    

    ![](../media/h192.png)

1. Search **Azure AI Search (1)** and select **Azure AI Search (2)**.

    ![](../media/h193.png)

1. Select **Add connection** to the right of your Azure AI Search Service.    

    ![](../media/connection.png)

1. Click on **Close**.

    ![](../media/connection1.png)

1. In the left navigation pane, click on **Go to project.**

    ![](../media/h199.png)

1. In the left navigation pane for the flow, in the **My assets** section, select **Model + endpoints (1)** , and then select the **+ Deploy Model (2)** drop-down. Next, choose **Deploy Base Model (3)**. 

     ![](../media/challenge6.task29.png)

1. Search for **GPT-4o (1),** then select **GPT-4o (2)** and click on **Confirm (3)**.

    ![](../media/challenge6.task30.png)

1. Within the **"Deploy model"** pop-up interface, click on **Customize**.

    ![](../media/model.png)
    
1. On the **Deploy gpt-4o,** enter the following details:

    - Deployment name: **gpt-4o (1)**
    - Deployment type: **Standard (2)**
    - Model version upgrade policy: **Upgrade once new default version becomes available (3)**
    - Model version: **select the default (4)**
    - Tokens per Minute Rate Limit (thousands): **20K (5)**
    - Enable dynamic quota: **Enabled (6)**
    - Click on **Deploy (7)**

      ![](../media/challenge6.task31.png)

1. In the left navigation pane for the flow, in the **Build and customize** section, select **Prompt flow.** 

     ![](../media/challenge6.task24.png)

1. Select **Start compute session**. This allows you to run and test the chatbot.    

    ![](../media/startsession.png)

1. Locate the **check_question_intent** tile. Click on the  **Connection (1)** field drop-down, and select the connection that displays **(2)**.

    ![](../media/flow.png)

1. Scroll down to the **chat_with_data** tile and under the **Inputs** section.   

    - Select the value of **search_connection** and then select your Azure AI Search Service from the drop-down list **(1)**.
    - Select the value of **ai_connection** and then select your Azure OpenAI resource from the drop-down list **(2)**.
    - Change the value of **search_index** to **brochures-vector (3)**.

      ![](../media/challenge6.task27.png)

1. Scroll down to the **generate_sql** tile. In the **Connection** field, select the connection that displays.      

    ![](../media/challenge6.task28.png)

1. Scroll down to the bottom of the **conclude_answer** tile. We’ll input a value into the field that will populate after testing the next steps. 

1. If the compute session has started, select **Chat** to test the flow.

    ![](../media/h205.png)

1. Enter `Where can I ski?` in the chat prompt and select **Enter (1)**. This will give you a warning and *populate the PostgreSQL **(2)** property at the bottom of the* **conclude_answer** tile.    

    ![](../media/h206.png)

1. Select **Value** of **PostgreSQL** and then choose **PostgreSQL** from the dropdown list.    

    ![](../media/h207.png)

1. Select the **X** on the warning in the chat.    

    ![](../media/h208.png)

1. Then send `Where can I ski?` again. Your results should resemble the following:

    ![](../media/h209.png)

    - In the Graph,

      ![](../media/h210.png)

1. Start a new conversation and enter, "`How many free rooms do hotels in Switzerland have grouped by hotel on 2024-10-10?`" Your results should resemble the following:     

    ![](../media/h211.png)

    - In the Graph,

      ![](../media/h212.png)

### Task 3: Deployment of Configured Prompt Flow

1. In the prompt flow tab, click on **Deploy**.

    ![](../media/challenge6.task32.png)

1. In the **Basic settings** tab of the Deploy prompt flow, select the Virtual machine size as **Standard_D2a_v4** **(1)** and click on the **Review + Create** **(2)** button.

    ![](../media/challenge6.task33.png)

1. In the **Review** tab of the Deploy prompt flow, click on the **Create** **(1)** button.

    ![](../media/challenge6.task34.png)

    > **Note**: The deployment of the endpoint may take 10-15 minutes, so please wait.

1. Once the deployment has succeeded, from the left side pane, select **Models + endpoints** **(1)** under the **My assets** session, and open the newly deployed endpoint **Contosopf-suffix** **(2)**.

    ![](../media/newimag4.png)

1. In the **contosopf-suffix** endpoint, copy the **Target URI** **(1)** and **Primary key** **(2)** under the Endpoint. Paste the values in a notepad.

    ![](../media/newimag5.png)

### Task 4: Configure Network Security Group Rules for External Access

1. Navigate back to the Azure portal, in the search bar, **Network security groups** **(1)**, and select **Network security groups** **(2)**.

   ![](../media/select-nsg.png)

1. In the **Network security groups**, copy the name on the **Resource group name**  **(1)** and the **NCG name** **(2)** of nvidia-gpu.

   ![](../media/nsg-name.png)

1. Configure Azure NSG rules for **nvidia-gpu** Virtual machines

   ```
   az network nsg rule create --resource-group youRGName --nsg-name myNSG --name allow-http --protocol tcp --priority 100 --destination-port-range 9000
   ```

   ```
   az network nsg rule create --resource-group youRGName --nsg-name myNSG --name allow-grpc --protocol tcp --priority 110 --destination-port-range 50051
   ```

   ![](../media/nsg-9000.png)

   ![](../media/nsg-50051.png)

   > **Note**: replace youRGName with Resource group name and myNSG with Network security groups name of nvidia-gpu 

1. In the Azure portal, use the search bar to search for **Virtual machines** **(1)** and select **Virtual machines** **(2)**.

   ![](../media/search-vm.png)

1. In the **Virtual machines**, copy the public IP of **nvidia-gpu** Virtual machines.

   ![](../media/search-vmip.png)

1. Add a new tab in the browser and navigate to the URL below to check if the service is ready to handle inference requests.

   ```
   http://<nvidia-gpu-public-ip>:9000/v1/health/ready
   ```

   ![](../media/web-trigger.png)

### Task 5: Setting Up and Running the AI-Powered Speech-to-Text and Chat Application

1. Open **Visual Studio Code** from the Lab VM desktop by double-clicking on it.

1. In **Visual Studio Code**, from the top left pane, select the **(...) (1)** ellipses > **Terminal (2)**, then choose **New Terminal (3)**.

   ![](../media/Active-image42.png)

1. Execute the following command in the terminal to clone the repository to a local folder: (it doesn't matter which folder).

   ```
   git clone https://github.com/CloudLabsAI-Azure/NVIDIA-Speech-to-text.git
   ```
    
    ![](../media/Active-image43.png)

1. When the repository has been cloned, open the folder in Visual Studio Code by following these steps:

    - From the top left corner pane, select **File (1)** >  **Open Folder (2)**.

       ![](../media/Active-image44.png)
      
    - Within the file explorer in **Quick access,** select **NVIDIA-Speech-to-text (1),** then click on **Select folder (2)**.

       ![](../media/Active-image45.png)
      
    - If **Do you trust the authors of the files in this folder?** prompted, click on **Yes, I trust the authors**.

         ![](../media/Active-image46.png)

       > **Note**: If you are prompted to add required assets to build and debug, select **Not Now**.

1. In **Visual Studio Code**, from the top left pane, select the **(...) (1)** ellipses > **Terminal (2)**, then choose **New Terminal (3)**.

   ![](../media/Active-image42.png)

1. Run the following command to install the Python package.

    ```
    pip install -r requirements.txt
    ```

    ![](../media/Active-imagenew1.png)

1. In the `.env` file, update the values `publicip` with the **public IP** of nvidia-gpu Virtual machines, `Azure AI Foundry Model Endpoint URL` with **Target URI** **(1)** and `Azure AI Foundry Model Key` with **Primary key** **(2)**. 

    ![](../media/newimag6.png)

1. In **Visual Studio Code**, from the top left pane, select the file **Files** **(1)** and click on **Save** **(2)**.

    ![](../media/newimag7.png)

1. In **Visual Studio Code**, from the top left pane, select the **(...) (1)** ellipses > **Terminal (2)**, then choose **New Terminal (3)**.

   ![](../media/Active-image42.png)

1. Run the following command: "Run the application" locally.

    ```
    python app.py
    ```
   
   ![](../media/newimag8.png)

1. Open a web browser, copy and paste the following URL: `http://127.0.0.1:5000/`

1. This application **supports both Riva Speech-to-Text and Chat with Azure AI services** running in **Prompt Flow**, which has been deployed.

1. The **AI Speech Assistant** page displays.

   ![](../media/newimag9.png)

1. On the **AI Speech Assistant** page, click on **upload audio file** **(1)**. In the pop-up window, navigate to the **NVIDIA-Speech-to-text\audio** **(2)** folder, select any **one sample** **(3)** audio file, and click on the **Open** **(3)** button.

    - `Sample 1.wav` with the question **"Where can I ski?"**  
    - `Sample 2.wav` with the question **"How many free rooms do hotels in Switzerland have, grouped by hotel, on 2024-10-10?"**

        ![](../media/newimag15.png)

1. Select the `Sample 1.wav` **(1)** audio file, click on the **send** **(2)** button, and you will get a response from the RIVA model in **response** **(3)** session and **text box** **(4)**.

   ![](../media/newimag18.png)

1. The response text from the **RIVA model** appears in the **text box** **(1)** as `## Where can I ski?` Click on the **Send** **(2)** button to trigger the **Prompt Flow**.

    ![](../media/newimag19.png)

1. You can view the response from the **Prompt Flow** chat application.

    ![](../media/newimag16.png)

1. Similarly, you can try with `Sample 2.wav`.

   ![](../media/newimag17.png)

### Task 6: Containerizing and Deploying the AI-Powered Speech-to-Text and Chat Application (Optional Task)

1. Enter the following command at the Terminal window prompt and then press **Enter**. This command builds the container for the chat app. Wait while the container builds.

   ```
   docker build -t "chatapp:v1.0.0" .
   ```
   ![](../media/newimag10.png)

    >**Note:** It may take 2-3 minutes to build the container.

1. Enter the following command at the Terminal window prompt and then press **Enter**. This command connects the Terminal window with your Azure subscription so that you can deploy Azure resources to the correct subscription.

   ```
   az login
   ```

    ![](../media/h46.png)  

1. Minimize Visual Studio Code.

    - On the Let’s get you signed in page, select **Work or School account (1)** and then select **Continue (2)**. 

      ![](../media/h47.png)      

    - Sign in using your Azure credentials.

    - On the **Sign in** page, enter the **Username (1)** and click on **Next (2)**.

      ![](../media/h48.png)     

       >**Note:** You can find the **Username** on the Lab VM's **Environment** page.

    - Enter the **Password (1)** and then click on **Sign in (2)**.  

      ![](../media/h49.png)     

       >**Note:** You can find the **Password**, on the Lab VM's **Environment** page.   

    - On the **Automatically sign to all desktop app and websites in this device** page, select **No, this app only.**

      ![](../media/c3.task1.1.png)        

1. Navigate back to Visual Studio Code. Press **Enter** to **Select a subscription and tenant**.      

   ![](../media/h51.png)

1. Run the command below to get the name of the **Container Registry instance** that you have created in *Challenge 2 Task 3*.

   ```
   az acr list --query "[].{Name:name}" --output table
   ```

   ![](../media/newimag12.png)

1. From the Terminal copy the name of the Container registry, which starts with **contosoacr{suffix}**.

   ![](../media/newimag13.png)

1. Run the following command on the Container registry name. 

    ```
    $ACR_NAME= "continer_name"
    ```

    > Note: Replace **continer_name** with **contosoacr{suffix},** which you copied in previous step.

1. Enter the following command at the Terminal window prompt and then press **Enter**. This command signs you into the ACR instance. 

   ```
   az acr login --name "$ACR_NAME"
   ```

    ![](../media/newimag11.png)

     >**Note:** You may see an error message stating Azure could not connect to the registry login server. This error usually indicates that even though the container registry instance is provisioned, some configuration is still happening. Wait a few minutes and run the command again.

1. Enter the following command at the Terminal window prompt and then press **Enter**. This command creates a Docker tag for the app.  

   ```
   docker tag "chatapp:v1.0.0" "$ACR_NAME.azurecr.io/chatapp:v1.0.0"
   ```

1. Enter the following command at the Terminal window prompt and then press **Enter**. This command pushes the app container to ACR.    

   ```
   docker push "$ACR_NAME.azurecr.io/chatapp:v1.0.0"
   ```

    ![](../media/newimag14.png)

     >**Note:** It may take 1-2 minutes to push the app container to ACR.

1. Update the value of the **AZURE_REGION_FROM_CHALLENGE1_TASK01** variable to use the region that you selected in Challenge 01 Task 01. Then, enter the command at the Terminal window prompt and then press **Enter**.   

   ```
   $AZURE_REGION="AZURE_REGION_FROM_CHALLENGE01_TASK01"
   ```

    ![](../media/h112.png) 

1. Enter the command at the Visual Studio Code Terminal window prompt and then select **Enter** after the last command. These commands create the container app for the chat app components.

   ```
   $chatapp = "chatapp$(Get-Random -Minimum 100000 -Maximum 999999)"

   az containerapp create --name "$chatapp" --resource-group "Appmod" --environment "$CONTOSO_HOTEL_ENV" --image "$ACR_NAME.azurecr.io/chatapp:v1.0.0" --target-port 5000 --ingress external --transport http --registry-server "$ACR_NAME.azurecr.io" --registry-username "$ACR_NAME" --registry-password "$CONTOSO_ACR_CREDENTIAL"
   $CONTOSO_CHAT_URL = "https://$(az containerapp show --name "$chatapp" --resource-group "Appmod" --query 'properties.configuration.ingress.fqdn' -o tsv)"
   Write-Host -ForegroundColor Green  "Chatapp URL is: $CONTOSO_CHAT_URL"
   ```

    >**Note:** If you encounter any error when creating the container app, first retrieve the Container Apps environment            name using the following command:
    ```
    az containerapp env list --resource-group Appmod --query "[].name" -o tsv
    ```
    Assign it to the variable:
   ```
    $CONTOSO_HOTEL_ENV = "<your-environment-name>"
   ```
    Then retrieve the ACR credential using:
   ```
    $CONTOSO_ACR_CREDENTIAL = az acr credential show --name $ACR_NAME --query "passwords[0].value" -o tsv
   ```
    Re-run the container app creation command after these steps.

1. Copy the Chatapp URL, open a new web browser window, and go to the URL for the Chatapp container.

## Success Criteria:

- You have successfully created a user in the PostgreSQL database.
- You have created a new AI Studio Hub and created a project.
- You have imported and configured a pre-built flow.
- You have tested the flow and confirmed that the flow returns appropriate results.
- Configured the necessary network security group rules to allow external access.
- Deployed the configured prompt flow to a virtual machine.
- Configured the necessary network security group rules to allow external access.
- Verified that the NVIDIA Riva ASR service is running and can handle inference requests.

## Additional Resources:

-  Refer to the  [Secure Azure Database for PostgreSQL](https://learn.microsoft.com/en-us/training/modules/secure-azure-database-for-postgresql/) to learn about the security features of Azure Database for PostgreSQL.
-  Refer to the [Get started with prompt flow](https://learn.microsoft.com/en-us/training/modules/get-started-prompt-flow-ai-studio/) guide to learn how to use it and develop applications that leverage language models in the Azure AI Foundry.


## Proceed with the next challenge by clicking on **Next**>>.
