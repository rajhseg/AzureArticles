Application Insights is used to log Messages, Here in this read me we will see about how to implemented the Application Insights in Asp.Net Core.

### Basic Implementation:

# Step 1:
  Install Package Microsoft.ApplicationInsights.AspNetCore in Asp.Net Core Package.

# Step 2:
Configure Program.cs
<br />
<br />

```nginx

  builder.Services.AddApplicationInsightsTelemetry();

```

After this step Messages will get logged, but it will get logged up to Warning, that means Critical, Error and Warning messages will get logged
Because Default LogLevel for ApplicationInsightsLoggerProvider is Warning. If you try to log Debug it will not Log.

<br/>
<br/>

To Make LogDebug log we have to override the Default Provider LogLevel.
<br />
<br />

When you give Empty value in AddFilter for first param then it will take default provider with log level what we mention in second parameter.

```csharp

builder.Logging.AddFilter<ApplicationInsightsLoggerProvider>(string.Empty, LogLevel.Debug);


builder.Logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);


builder.Services.Configure<LoggerFilterOptions>(options =>
{
    var defaultRule = options.Rules.FirstOrDefault(
        rule => rule.ProviderName == 
                    "Microsoft.Extensions.Logging.ApplicationInsights.
                            ApplicationInsightsLoggerProvider");

    if (defaultRule != null)
    {
        options.Rules.Remove(defaultRule);
    }

});

```
<br />

# Bottom to Top Approach
Above code will make default log level to Debug, so now up to Log Debug will work. Now you can log the messages in your code, Make sure you are getting the log level from configuration File.

<br />
<br />

```nginx

Logs works Bootom to Top approach

   public enum LogLevel
    {
      Trace = 0,
      Debug = 1,
      Information = 2,
      Warning = 3,
      Error = 4,
      Critical = 5,
      None = 6,
    }

```
<br />
<br />

### Add Custom Properties To Telemetry:

```nginx

internal class CustomTelemetryProcessor : ITelemetryProcessor
{
    private readonly ITelemetryProcessor next;  

    public CustomTelemetryProcessor(ITelemetryProcessor next)
    {
        this.next = next;    
    }

    public void Process(ITelemetry item)
    {

        if (item is OperationTelemetry operationTelemetry)
        {
            if (operationTelemetry.Name.ToLower().Contains("getauthor"))
            {
                operationTelemetry.Properties["Props123"] = "CustomValue123";
            }
        }

        if (item is RequestTelemetry requestTelemetry && 
                requestTelemetry.ResponseCode == "200")
        {
            requestTelemetry.Properties["SuccessfulRequest"] 
                = "Sample Response";
        }

        next.Process(item);
    }
}

```

```nginx

builder.Services.AddApplicationInsightsTelemetryProcessor<CustomTelemetryProcessor>();


```

<br />
<br />

<img width="627" height="399" alt="image" src="https://github.com/user-attachments/assets/fc4ac3ef-120e-4b04-ab43-9d31c276a98a" />

<br/>
<br />

### Controller Level Configuration :

```nginx

builder.Logging.AddFilter<ApplicationInsightsLoggerProvider>(
    "WebAssemblyApp.Server.Controllers.AuthorController", LogLevel.Error);

builder.Logging.AddFilter<ApplicationInsightsLoggerProvider>(
    "WebAssemblyApp.Server.Controllers.BookController", LogLevel.Debug);

```

### Filter or Remove Modules :
 In Module level we can filter the messages like in Dependencies are logged in application insights, if we don't want SQL Text from EF core wont need to log then configure EnableSqlCommandTextInstrumentation item as "false".

<br />

```nginx

builder.Services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
{
    module.EnableSqlCommandTextInstrumentation = false;
    module.DisableDiagnosticSourceInstrumentation = true;
});

```
<br />

if you don't want the Dependencies are not allow to log then we have to remove the module like below in Program.cs.

<br />

```nginx

var dependencyModule = builder.Services.FirstOrDefault(x=>x.ImplementationType == typeof(DependencyTrackingTelemetryModule));

if (dependencyModule != null)
{
    builder.Services.Remove(dependencyModule);
}

```

# Before Remove Dependency count will be there

<img width="658" height="400" alt="image" src="https://github.com/user-attachments/assets/aa2ce111-72e3-413c-82c9-44d480b65533" />


# After Remove Dependency count will be 0

<img width="657" height="398" alt="image" src="https://github.com/user-attachments/assets/d180ba11-4916-4d9a-8a18-dd536e4558fd" />





