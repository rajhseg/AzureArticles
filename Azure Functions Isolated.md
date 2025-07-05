# Azure Articles

**Azure Functions Isolated**
   <br/>
    Reference : https://learn.microsoft.com/en-us/azure/azure-functions/dotnet-isolated-process-guide?tabs=hostbuilder%2Cwindows

    1. To debug the Azure Function remotely

    **Prepare your Azure Function App:**
    1. Enable Remote Debugging: In the Azure portal, navigate to your function app's settings. 
    2. Under "General settings", enable "Remote debugging" and set the "Remote Visual Studio version" to the appropriate version (e.g., 2022). 
    3. Deployment: Ensure your function is deployed to Azure in debug configuration. 
    4. Check for Matching Symbols: Make sure the local symbol files in your project's bin folder match the deployed code. This is crucial for the debugger to map the compiled code to your source code. 

    **Configure Visual Studio:**
    1. Open your solution: Open the Azure Functions project in Visual Studio.
    2. Disable "Just My Code": Go to Debug > Options and uncheck "Enable Just My Code" to allow debugging optimized code.
    3. Attach to Process: Go to Debug > Attach to Process.
    4. Select Connection Type: Choose "Microsoft Azure App Service" as the connection type.
    5. Find the App Service: Click "Find" to browse your Azure subscriptions and select the correct App Service instance.
    6. Select **"Dotnet.exe"** for **isolated**, or **"w3wp.exe"** for **others**
    6. Attach: Click **"Attach"** to connect Visual Studio to the remote process. 
