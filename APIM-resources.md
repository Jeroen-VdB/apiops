# API
APIs are extracted in ``artifacts/apis/apiName/apiInformation.json``.

You can override the JSON in your configuration YAML by updating this section:
```yaml
apis:
    - name: apiName
      properties:
      ...
```

# API diagnostic
API diagnostics are extracted in ``artifacts/apis/apiName/diagnostics/diagnosticName/diagnosticInformation.json``. You can override the JSON in configuration like this:
```yaml
apis:
    - name: apiName
      diagnostics:
        - name: diagnosticName
          properties:
          ...

```
# API operation policy
API operation policies are extracted in ``artifacts/apis/apiName/operations/operationName/policyName.xml``. As of this writing, all APIM policies are named ``policy``, so the path above is effectively ``artifacts/apis/apiName/operations/operationName/policy.xml``.

# API policy
API policies are extracted in ``artifacts/apis/apiName/policyName.xml``. As of this writing, all APIM policies are named ``policy``, so the path above is effectively ``artifacts/apis/apiName/policy.xml``.

# API specification
API specifications are extracted in ``artifacts/apis/apiName/specification.yaml|wadl|wsdl|json|graphql``.

During extraction, you can specify your preferred specification type by passing a parameter ``API_SPECIFICATION_FORMAT`` with one of the following values:
| Parameter value | Specification | File name |
|:--|:--|:--|
| WADL | WADL | ``specification.wadl`` |
| JSON | Open API v3 JSON | ``specification.json`` |
| YAML | Open API v3 YAML (default) | ``specification.yaml`` |
| OpenApiV2JSON | Swagger JSON | ``specification.json`` |
| OpenAPIV2YAML | Swagger YAML | ``specification.yaml`` |

This parameter does not affect GraphQL or SOAP APIs. They will always be extracted in their original format as ``specification.graphql`` and ``specification.wsdl``.

If you really wanted to, you could override your specification within your configuration YAML like this:
```yaml
apis:
  - name: myapi
    properties:
      format: One of these formats (https://learn.microsoft.com/en-us/rest/api/apimanagement/current-ga/apis/create-or-update?tabs=HTTP#contentformat)
      value: Specification file contents
```

> **NOTE:** It's possible to create a ``specification.xxx`` file that conflicts with the API's ``apiInformation.json``. We don't attempt to resolve any conflicts; rather, we pass both and let APIM handle it.

# API tag
API tags are extracted in ``artifacts/apis/apiName/tags.json``. You can override this in configuration like this:
```yaml
apis:
    - name: apiName
      tags:
        - name: tagA
        - name: tagB
```

Tags defined in configuration fully override tags defined in ``tags.json``. In other words:

| Given configuration tags | Given ``tags.json`` tags | Given current tags in APIM | After publisher runs |
|:--|:--|:--|:--|
| tagA, tagB | tagC | tagD | tagA, tagB |
| Array set to empty | tagA | tagA | No tags
| Not specified | tagA | tagB | tagA

# Backend
Backends are extracted in ``artifacts/backends/backendName/backendInformation.json``. You can override the JSON in configuration like this:
```yaml
backends:
    - name: backendName
      properties:
      ...
```

# Diagnostic
Diagnostics are extracted in ``artifacts/diagnostics/diagnosticName/diagnosticInformation.json``. You can override the JSON in configuration like this:
```yaml
diagnostics:
    - name: diagnosticName
      properties:
      ...
```

Each diagnostic is associated with a logger resource. When promoting across environments, make sure to override the ``loggerId`` property with the corresponding logger.
```yaml
diagnostics
    - name: diagnosticName
      properties:
        loggerId: Resource ID of the associated logger. Format is "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.ApiManagement/service/serviceName/loggers/loggerName"  
      ...
```

# Gateway
Gateways are extracted in ``artifacts/gateways/gatewayName/gatewayInformation.json``. You can override the JSON in configuration like this:
```yaml
gateways:
    - name: gatewayName
      properties:
      ...
```

# Gateway API
Gateway APIs are extracted in ``artifacts/gateways/gatewayName/apis.json``. You can override this in configuration like this:
```yaml
gateways:
    - name: gatewayName
      apis:
        - name: apiA
        - name: apiB
```

Gateway APIs defined in configuration fully override APIs in ``apis.json``. In other words:

| Given configuration APIs | Given ``apis.json`` APIs | Given current APIs in APIM | After publisher runs |
|:--|:--|:--|:--|
| API A, API B | API C | API D | API A, API B |
| Array set to empty | API A | API A | No APIs
| Not specified | API A | API B | API A

# Loggers
Loggers are extracted in ``artifacts/loggers/loggerName/loggerInformation.json``.

You can override the JSON in your configuration YAML by updating this section:
```yaml
loggers:
  - name: loggerName
    properties:
      ...
```

## Application Insights loggers
When you link an Application Insights resource to your APIM instance in the Azure portal, APIM generates a logger with your Application Insights resource name. You don't have an option to override the logger name. This can be problematic when promoting across environments.

Suppose your DEV Application Insights resource is named ``appinsights-dev``. When you link it to your DEV APIM instance, a logger named ``appinsights-dev`` gets created. Running the extractor tool will generate the folder structure ``artifacts/loggers/appinsights-dev/loggerInformation.json``.

When it's time to promote the logger to PROD, the logger will still be called ``appinsights-dev``. You can override the target Application Insights resource, but you will see a logger named ``appinsights-dev`` in your PROD APIM instance pointing to your PROD Application Insights resource. While everything will work, it's not desirable to have a resource with a ``-dev`` prefix in your PROD instance.

Here is our recommended approach for handling Application Insights loggers:
1. In the Azure portal, link your Application Insights resource to your APIM instance. A logger with your Application Insights resource name will get created.
2. Run the extractor. You will get the folder structure ``artifacts/loggers/your-appinsights-resource-name/``.
3. Rename the logger folder name from ``your-appinsights-resource-name`` to a name that can be consistent across environments. For instance, set the name to ``appinsights`` instead of ``appinsights-dev``.
4. **Optional:** APIM autogenerates a named value for your application insights instrumentation key. It's usually called ``Logger-Credentials-...``. Rename the extracted named value folder to something human-friendly; for instance, from ``artifacts/named values/Logger-Credentials-.../`` to ``artifacts/named values/application-insights-instrumentation-key/``.
5. The extracted JSON in ``artifacts/loggers/logger-name/loggerInformation.json`` specifies a resource ID that points to the Application Insights resource. Override it in your configuration YAML as needed:
```yaml
loggers:
  - name: appinsights (or whichever folder name you chose in Step #3)
    properties:
      resourceId: Resource ID of your environment's Application Insights resource (/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/microsoft.insights/components/appInsightsResourceName"
```
6. **Optional:** If you renamed the named value in Step #4, update the instrumentation key reference in your ``artifacts/loggers/logger-name/loggerInformation.json``.
```json
{
  "properties": {
    "credentials": {
      "instrumentationKey": "your-new-named-value-name"
    },
...
  }
}
```
Or override your configuration YAML like this:
```yaml
loggers:
  - name: appinsights (or whichever folder name you chose in Step #3)
    properties:
      credentials:
        instrumentationKey: "your-new-named-value-name"
...
```
7. Your Application Insights instrumentation key value will likely be different across environments. Override your configuration named value with the proper instrumentation key.
```yaml
namedValues:
  - name: Instrumentation key's named value name (Logger-Credentials-... or the name you chose in Step #4)
    properties:
      value: your instrumentation key
```

8. In the Azure portal, delete your APIM instance logger from Step 1 and your ``Logger-Credentials-...`` named value if you overrode it in Step 4. They will get recreated with proper names when the publisher runs.

## Event Hub loggers
We recommend a code-first approach to create Event Hub loggers:
1. Create a folder under ``loggers`` with a name for your logger (``artifacts/loggers/eventhub``, for instance). This logger name should be consistent across environments.
2. Under your logger folder, create a ``loggerInformation.json`` file (``artifacts/loggers/eventhub/loggerInformation.json``). The file contents should look like this:
```json
{
  "properties": {
    "loggerType": "azureEventHub",
    "description": "A description for your logger",
    "credentials": {
      "connectionString": "{{Named value for your event hub connection string}}"
    }
  }
```
3. Create a named value with your Event Hub connection string. The named value name should match your ``properties/credentials/connectionString`` value in your logger's ``loggerInformation.json``.
4. Override the named value's value in each environment's configuration as needed.

# Version set
Version sets are extracted in ``artifacts/version sets/versionSetName/apiVersionSetInformation.json``. You can override the JSON in configuration like this:
```yaml
gateways:
    - name: gatewayName
      properties:
      ...
```

To link a version set and an API, set the API's ``apiVersionSetId`` property to the version set's resource ID. 

__artifacts/apis/apiName/apiInformation.json__
```json
{
  "properties": {
    "apiVersionSetId": "/subscriptions/subscriptionName/resourceGroups/resourceGroupName/providers/Microsoft.ApiManagement/service/apimServiceName/apiVersionSets/versionSetName"
...
}
```

You can also set it in configuration like this:
```yaml
apis:
    - name: apiName
      properties:
        apiVersionSetId: /subscriptions/subscriptionName/resourceGroups/resourceGroupName/providers/Microsoft.ApiManagement/service/apimServiceName/apiVersionSets/versionSetName
```