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