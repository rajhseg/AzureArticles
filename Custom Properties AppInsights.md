This read me will have details of various ways to add the custom properties in application insights.

1. Direct Logger
2. Using Scopes
3. Using Telemetry Client
4. Using Custom Telemetry Initializer
5. Using Custom Telemetry Processor

# Direct Logger
In Direct Logger method using curly brackets to identify the properties name inside log method. <br/>
Here **AuthorTimeStamp** and **Id** are consider as Properties and there values from right side.
<br />

```nginx

logger.LogDebug("Debug: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);

logger.LogError("EXception: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);

```

<br />

# Using Scopes
In this method it will apply the properties for all logger inside the scope. <br />
Here **AuthorScope** is the Parameter name applied for both logs.
<br />

```nginx

using (logger.BeginScope(new Dictionary<string, object>
{
    ["AuthorScope"] = "12345"
}))
{
    logger.LogDebug("Debug: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);

    logger.LogError("EXception: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);
}

```

<br />

# Using Telemetry Client
This is direct method using telemetry client, we will create a Exception Telemetry for sample.
<br />

```nginx

var excepTele = new ExceptionTelemetry(new Exception("Sample exception"));
excepTele.Properties["AuthorExcep"] = "1234";

var tracTele = new TraceTelemetry("AuthorTrace", SeverityLevel.Information);
tracTele.Properties["AuthorTrace"] = "1234";

TelemetryClient cli = new TelemetryClient();
cli.TrackException(excepTele);
cli.TrackTrace(tracTele);


```

<br />

# Using Custom Telemetry Initializer
This will run in Pipeline for all telemetry's before send the logs to Azure monitor.
<br />
For example we will add property to all Trace Telemetry 
<br />

```nginx

    internal class CustomTraceTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (telemetry is TraceTelemetry traceTelemetry) {
                traceTelemetry.Properties["AuthorTraceGlobal"] = "234";
            }
        }
    }

builder.Services.AddSingleton<ITelemetryInitializer, CustomTraceTelemetryInitializer>();

```

<br />

# Using Custom Telemetry Processor
This will run in Pipeline for all telemetry's before send the logs to Azure monitor
<br />



<br />
