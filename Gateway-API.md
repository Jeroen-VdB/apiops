### Gateway API
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