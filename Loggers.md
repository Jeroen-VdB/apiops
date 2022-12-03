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