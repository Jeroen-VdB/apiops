### API diagnostic
API diagnostics are extracted in ``artifacts/apis/apiName/diagnostics/diagnosticName/diagnosticInformation.json``. You can override the JSON in configuration like this:
```yaml
apis:
    - name: apiName
      diagnostics:
        - name: diagnosticName
          properties:
          ...
```