This read me will have details of various ways to add the custom properties in application insights.

1. Direct Logger
2. Using Scopes
3. Using Telemetry Client
4. Using Custom Telemetry Initializer
5. Using Custom Telemetry Processor

# Direct Logger
In Direct Logger method using curly brackets to identify the properties name inside log method. <br/>
Here AuthorTimeStamp and Id are consider as Properties and there values from right side.
<br />

```nginx

logger.LogDebug("Debug: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);

logger.LogError("EXception: GetAuthor called for id: {Id} {AuthorTimeStamp}", Id, DateTime.UtcNow);

```

<br />

# Using Scopes

<br />

# Using Telemetry Client

<br />

# Using Custom Telemetry Initializer

<br />

# Using Custom Telemetry Processor

<br />
