# AzureArticles

# 1. WebPubSub Service articles.
  To add a upstream eventhandler we have to add two endpoints one is Validate Options method endpoint, another one is event based endpoint, for example if the system event connect you want to hookup then it should be connect post method endpoint. Here we are using the Azure functons to create a http based two endpoints for upstream hookup eventhandler, if you want you can use the webapi also for this two endpoints. for example if this is the upstream hookup eventhandler url http://{func-host or hostname}/api/{event} then the validate options method url will be http://{func-host or hostname}/api/validate.

<br/>

When the PubSub service is connecting to the App, it first does an OPTIONS request to the "/validate" endpoint. This request expects a response with HTTP header "WebHook-Allowed-Origin". 

<br/>

When configuring an Azure Web PubSub event handler, the service validates the upstream webhook URL using the CloudEvents Abuse Protection mechanism. This validation involves checking for the WebHook-Allowed-Origin header in the response to an OPTIONS request. The WebHook-Request-Origin header, set to the service domain (e.g., xxx.webpubsub.azure.com), is included in the OPTIONS request, and the upstream endpoint must respond with WebHook-Allowed-Origin header, which can be either * or the service domain. 

Allowed origins:
The value of WebHook-Allowed-Origin can be either * (allowing any origin) or the specific Azure Web PubSub service domain (e.g., xxx.webpubsub.azure.com).
  1. Validate options method in azure function for upstream handler
     
   **Reference: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/microsoft.azure.functions.worker.extensions.webpubsub-readme?view=azure-dotnet**
   
      // validate method when upstream set as http://<func-host>/api/{event}
      [Function("validate")]
      public static HttpResponseData Validate(
          [HttpTrigger(AuthorizationLevel.Anonymous, "options")] HttpRequestData req,
          [WebPubSubContextInput] WebPubSubContext wpsReq)
      {
          return BuildHttpResponseData(req, wpsReq.Response);
      }
      
      // Respond AbuseProtection to put header correctly.
      private static HttpResponseData BuildHttpResponseData(HttpRequestData request, SimpleResponse wpsResponse)
      {
          var response = request.CreateResponse();
          response.StatusCode = (HttpStatusCode)wpsResponse.Status;
          response.Body = response.Body;
          foreach (var header in wpsResponse.Headers)
          {
              response.Headers.Add(header.Key, header.Value);
          }
          return response;
      }

  2. Azure Function Isolated with connect upstream handler
     
   **Reference: https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-web-pubsub-trigger?tabs=isolated-process%2Cnodejs-v4&pivots=programming-language-csharp**
   
   Below one specific to one Hub 
   <br/>
   ( EventHandlerUrl= <Function_App_Url>/runtime/webhooks/webpubsub?code=<webpubsub_extension key from AppKeys of Function> )

   <br/>
    UpStream Sample EventHandler Url : https://webpubsubtrdesd.azurewebsites.net/runtime/webhooks/webpubsub?code=F_sYDrpz7BdvmK57dha_po2a6Y2tttyTSG3QYJde9DAzFuIiOP4A==**
   <br/>
     <br/>
   copy the value of webpubsub_extension key from azure function app keys section.
   
   ![image](https://github.com/user-attachments/assets/e140cbd5-4127-465b-a97c-ce0453707657)

   <br/>
    
    
    
    [Function("Broadcast")]
    public static WebPubSubEventResponse Run(
    [WebPubSubTrigger("HubName", WebPubSubEventType.System, "Connect")] ConnectEventRequest request)
    {
            // You can add custom logic here to handle the connection request
            // For example, you can check the origin and return a specific response
            return new ConnectEventResponse() { UserId = "user123" };
          
        //return new EventErrorResponse() { Code = WebPubSubErrorCode.Unauthorized, ErrorMessage="UnAuthorized" };
    }

   Below one is Generic connect method works for all Hub  
   ( EventHandlerUrl= <Function_App_Url>/api/{event} )
  
   **UpStream EventHandler Sample url**: **https://webpubsubfwwwww.azurewebsites.net/api/{event}**

    // validate method when upstream set as **http://<func-host>/api/{event}**

    [Function("connect")]
    public async Task<IActionResult> connect([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req)
    {
        StreamReader reader = new StreamReader(req.Body);
        var result = await reader.ReadToEndAsync(); // Read the request body if needed

        //Do validation based on {result} string contains headers, query, connection info etc.
   
        var resp = new ConnectEventResponse() { UserId = "user123" };
        return new OkObjectResult(resp); // for valid connection return success
    
        // return new UnauthorizedObjectResult("Invalid Origin"); // for invalid connection return unauthorized.
    }


  3. ServiceClient send messages to client using various formats
     
  **Reference from: https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/WebPubSubSamples.HelloWorld.cs**

   Sample code:
   
    WebPubSubServiceClient client = new WebPubSubServiceClient(conn, "HubName");
    Console.WriteLine("Enter a message to send:");
    string message = Console.ReadLine();
    
    Stream stream = BinaryData.FromString(message).ToStream();
    client.SendToUser(userid ,RequestContent.Create(stream), ContentType.ApplicationOctetStream);
   
    (or)

    client.SendToUser(userid, message, ContentType.TextPlain);



 All Reference Urls:
   1. https://learn.microsoft.com/en-us/dotnet/api/overview/azure/microsoft.azure.functions.worker.extensions.webpubsub-readme?view=azure-dotnet
   2. https://learn.microsoft.com/en-us/azure/azure-functions/functions-bindings-web-pubsub-trigger?tabs=isolated-process%2Cnodejs-v4&pivots=programming-language-csharp
   3. https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Azure.Messaging.WebPubSub/tests/Samples/WebPubSubSamples.HelloWorld.cs
   4. https://github.com/JialinXin/azure-sdk-for-net/blob/awps/isolated-func/sdk/webpubsub/Microsoft.Azure.Functions.Worker.Extensions.WebPubSub/samples/Sample4_HttpTriggerInputBinding.md
   5. https://github.com/Azure/azure-sdk-for-net/tree/Microsoft.Azure.Functions.Worker.Extensions.WebPubSub_1.7.0-beta.1/sdk/webpubsub/Microsoft.Azure.Functions.Worker.Extensions.WebPubSub/samples
   6. https://github.com/JialinXin/azure-sdk-for-net/blob/awps/isolated-func/sdk/webpubsub/Microsoft.Azure.Functions.Worker.Extensions.WebPubSub/samples/Sample3_EventNotification.md
      


    

  
   
   
