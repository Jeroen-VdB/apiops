### API
APIs are extracted in ``artifacts/apis/apiName/apiInformation.json``.

You can override the JSON in your configuration YAML by updating this section:
```yaml
apis:
    - name: apiName
      properties:
      ...
```

### API specification
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

> **NOTE:** It's possible to create a ``specification.xxx`` file that conflicts with the API's ``apiInformation.json``. We don't attempt to resolve any conflicts; rather, we pass both and let APIM handle it.

If you really wanted to, you could override your specification within your configuration YAML like this:
```yaml
apis:
  - name: myapi
    properties:
      format: One of these formats (https://learn.microsoft.com/en-us/rest/api/apimanagement/current-ga/apis/create-or-update?tabs=HTTP#contentformat)
      value: Specification file contents
```
### API policy
API policies are extracted in ``artifacts/apis/apiName/policyName.xml``. As of this writing, all APIM policies are named ``policy``, so the path above is effectively ``artifacts/apis/apiName/policy.xml``.

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

### API operation policy
API operation policies are extracted in ``artifacts/apis/apiName/operations/operationName/policyName.xml``. As of this writing, all APIM policies are named ``policy``, so the path above is effectively ``artifacts/apis/apiName/operations/operationName/policy.xml``.

### API tag
API tags are extracted in ``artifacts/apis/apiName/tags.json``. You can override this in configuration like this:
```yaml
apis:
    - name: apiName
      tags:
        - name: tagA
        - name: tagB
```

Tags in configuration fully override tags in ``tags.json``. In other words:

| Configuration tags | ``tags.json`` tags | Current tags in APIM | After publisher runs |
|:--|:--|:--|:--|
| tagA, tagB | tagC | tagD | tagA, tagB |
| Array set to empty | tagA | tagA | No tags
| Not specified | tagA | tagB | tagA

### Backend
Backends are extracted in ``artifacts/backends/backendName/backendInformation.json``. You can override the JSON in configuration like this:
```yaml
backends:
    - name: backendName
      properties:
      ...
```

### Diagnostic
Diagnostics are extracted in ``artifacts/diagnostics/diagnosticName/diagnosticInformation.json``. You can override the JSON in configuration like this:
```yaml
diagnostics:
    - name: diagnosticName
      properties:
      ...
```

### Gateway
Gateways are extracted in ``artifacts/gateways/gatewayName/gatewayInformation.json``. You can override the JSON in configuration like this:
```yaml
gateways:
    - name: gatewayName
      properties:
      ...
```

### Gateway API
Gateway APIs are extracted in ``artifacts/gateways/gatewayName/apis.json``. You can override this in configuration like this:
```yaml
gateways:
    - name: gatewayName
      apis:
        - name: apiA
        - name: apiB
```

APIs in configuration fully override APIs in ``apis.json``. In other words:

| Configuration APIs | ``apis.json`` APIs | Current APIs in APIM | After publisher runs |
|:--|:--|:--|:--|
| API A, API B | API C | API D | API A, API B |
| Array set to empty | API A | API A | No APIs
| Not specified | API A | API B | API A

