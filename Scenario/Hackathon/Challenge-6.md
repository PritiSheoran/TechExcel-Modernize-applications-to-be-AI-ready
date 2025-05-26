# Challenge 07: Integrate the Chatbot with the Contoso Hotel Application
### Estimated Time: 30 Minutes
## Introduction

At this point, you have a chatbot that can query the hotel brochures. In this challenge, you will integrate the chatbot into the updated Contoso Hotel application.

## Challenge Objectives:

> **Important**: When deploying services in this challenge, please make sure to use the resource group named **Appmod** and use the same region as the resource group.

1. **Set up Visual Studio Code and Run the Flow Locally:**

   - Navigate to the `C:\Users\demouser\AssetsRepo\Assets` folder in the File Explorer and extract the **lab-6-promptflow.zip** file. 
   - Launch Visual Studio Code as an **administrator** from the desktop and open the folder **lab-6-promptflow**. 
   - Rename the `.env.sample` file to `.env`. Create an environment file for the chatbot.
   - Create a connection to **Azure OpenAI**.
      * Use the following command to help set up the environment variables from the .env file and create the required connections:
         ```
         get-content .env | foreach {
         $name, $value = $_.split('=')
         set-content env:\$name $value
         }
         ```
         ```
         pf connection create --file azure_openai.yaml --name azure_openai --set "api_base=$env:AZURE_OPENAI_ENDPOINT" --set "api_key=$env:AZURE_OPENAI_API_KEY"
         pf connection create --file azure_ai_search.yaml --name azure_ai_search --set "api_base=$env:AZURE_AI_SEARCH_ENDPOINT" --set "api_key=$env:AZURE_AI_SEARCH_API_KEY"
         pf connection create --file postgresql.yaml --name postgresql --set "configs.hostname=$env:PGHOST" --set "configs.port=$env:PGPORT" --set "configs.user=$env:PGUSER" --set "configs.database=$env:PGDATABASE" --set "secrets.passwd=$env:PGPASSWORD"
         ```
   - Install all dependencies listed in the **requirements.txt** file by running the `pip install -r requirements.txt` command.
   - Run the chatbot locally using the command `pf flow test --flow. --interactive` by giving the prompts **Where can I ski?** and **How many free rooms do hotels in Switzerland have grouped by hotel on 2024-10-10?** to test the chatbot.

1. **Deploy the Flow to Azure Container Apps and Test the App:** 

   - Log in to the Azure portal using `az login` command.
   - Log in to ACR, create the flow, build the container, containerize the flow, and push the flow to ACR. 
   
      Copy the following .yaml files into the .\docker-dist\connections folder  before building the container:

         1. azure_openai.yaml
         2. azure_ai_search.yaml
         3. postgresql.yaml

   - Create a container app named **chatbot**, then deploy the container to ACR and configure environmental variables.

      Use the following command to create the container app:
      ```
      az containerapp create --name "chatbot" --resource-group "$RG_NAME" --environment "$CONTOSO_HOTEL_ENV" `
      --image "$ACR_NAME.azurecr.io/chatbot:v1.0.0" --target-port 8080 --transport http `
      --registry-server "$ACR_NAME.azurecr.io" --registry-username "$ACR_NAME" --registry-password "$CONTOSO_ACR_CREDENTIAL" `
      --secrets "searchkey=$AZURE_AI_SEARCH_API_KEY" "openaikey=$AZURE_OPENAI_API_KEY" "pgpassword=$PGPASSWORD" `
      --env-vars "AZURE_AI_SEARCH_ENDPOINT=$AZURE_AI_SEARCH_ENDPOINT" "AZURE_AI_SEARCH_API_KEY=secretref:searchkey" `
      "AZURE_OPENAI_ENDPOINT=$AZURE_OPENAI_ENDPOINT" "AZURE_OPENAI_API_KEY=secretref:openaikey" `
      "PGHOST=$PGHOST" "PGPORT=$PGPORT" "PGUSER=$PGUSER" "PGDATABASE=$PGDATABASE" "PGPASSWORD=secretref:pgpassword" --ingress external 
      ```
   - Update the **frontend** and **backend** applications using the following commands to set the **CHATBOT_BASEURL** environment variable:

      ```
      az containerapp update --name "frontend" --resource-group "$RG_NAME" --set-env-vars "CHATBOT_BASEURL=$CONTOSO_BACKEND_URL"
      az containerapp update --name "backend" --resource-group "$RG_NAME" --set-env-vars "CHATBOT_BASEURL=$CONTOSO_CHATBOT_URL"
      ```

   - Navigate to the **chatbot** application URL, enter the queries, `Where can I ski?` and `How many free rooms do hotels in Switzerland have grouped by hotel on 2024-10-10`? View the results. The Contoso Hotel app will display the chatbot page.
   - Open a new web browser tab and navigate to the **Application URL** for the **frontend** container. This will allow you to display the **chatbot** from within the updated Contoso Hotel app.

     <validation step="487703c2-6b5c-4059-ae13-a8898f10f02e" />      

## Success Criteria:

- You’ve set up your development environment.
- You’ve tested the chatbot locally.
- You’ve updated the app to include the chatbot feature.


## Additional Resources:

-  Refer to the  [Prompt Flow documentation](https://microsoft.github.io/promptflow/reference/pf-command-reference.html#pf-flow) to learn about it.
