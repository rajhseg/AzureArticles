# AzureArticles

# 1. WebPubSub Service articles.

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
   
   **UpStream Sample EventHandler Url** **https://webpubsubtrdesd.azurewebsites.net/runtime/webhooks/webpubsub?code=F_sYDrpz7BdvmK57dha_po2a6Y2tttyTSG3QYJde9DAzFuIiOP4A==**
   
   Below one specific to one Hub 
   <br/>
   ( EventHandlerUrl= <Function_App_Url>/runtime/webhooks/webpubsub?code=<webpubsub_extension key from AppKeys of Function> )
   
   copy the value of webpubsub_extension key from azure function app keys section.
   
    [Function("Broadcast")]
    public static WebPubSubEventResponse Run(
    [WebPubSubTrigger("Hubabcd", WebPubSubEventType.System, "Connect")] ConnectEventRequest request)
    {
        if (request.Headers["Origin"][0].ToLower().Trim()== "https://aaaaa.com")
        {
            // You can add custom logic here to handle the connection request
            // For example, you can check the origin and return a specific response
            return new ConnectEventResponse() { UserId = "user123" };
        }   
    
        return new EventErrorResponse() { Code = WebPubSubErrorCode.Unauthorized, ErrorMessage="UnAuthorized" };
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
   
    WebPubSubServiceClient client = new WebPubSubServiceClient(conn, "Hubabcd");
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
      


    

  
   
   
