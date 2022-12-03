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