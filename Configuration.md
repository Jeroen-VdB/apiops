# Configuration providers
We use the default [.NET configuration](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration#configure-console-apps), plus any specified YAML configuration file. Here's the priority order taken from the .NET documentation:

1. YAML file specified in configuration key ``CONFIGURATION_YAML_PATH``.
2. Command-line arguments using the [Command-line configuration provider](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#command-line-configuration-provider).
3. Environment variables using the [Environment Variables configuration provider](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).
4. [App secrets](https://learn.microsoft.com/en-us/aspnet/core/security/app-secrets) when the app runs in the Development environment.
5. ``appsettings.Environment.json`` using the [JSON configuration provider](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#file-configuration-provider). For example, ``appsettings.Production.json`` and ``appsettings.Development.json``.
6. ``appsettings.json`` using the [JSON configuration provider](https://learn.microsoft.com/en-us/dotnet/core/extensions/configuration-providers#file-configuration-provider).
7. [ChainedConfigurationProvider](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.configuration.chainedconfigurationsource) : Adds an existing IConfiguration as a source.

You can use multiple providers simultaneously: for instance, ``apimServiceName`` as an environment variable, ``API_SPECIFICATION_FORMAT`` as a command line argument, and overriding a named value in your configuration YAML. When keys conflict between providers, the highest priority provider wins out.

Here's what a sample configuration would look like across providers:
#### Configuration YAML
```yaml
apimServiceName: myapim
namedValues:
  - name: environment
    properties:
      displayName: environment
      value: "https://www.search2.com"
```

#### Environment variables:
```
apimServiceName: myapimservice
namedValues__0__name: environment
namedValues__0__properties__displayName: environment
namedValues__0__properties__value: https://www.search2.com
```

#### Appsettings.json
```json
{
  "apimServiceName": "myapimservice",
  "namedValues": [
    {
      "name": "environment",
      "properties": {
        "displayName": "environment",
        "value": "https://www.search2.com"
      }
    }
  ]
}
```

# Configuration values

| Value | Purpose | Extractor, publisher, or both | Sample values |
| - | - | - | - |
| ``CONFIGURATION_YAML_PATH`` | Absolute path for the configuration YAML file. | Both | ``C:\Temp\configuration.yaml``<br><br> ``/tmp/configuration/yml`` |
| ``AZURE_CLOUD_ENVIRONMENT`` | Azure cloud where APIM is located. See [here](https://github.com/Azure/apiops/wiki/Connecting-to-different-Azure-clouds) for more details. | Both | ``AzureGlobalCloud``<br><br>``AzureUSGovernment`` |
| ``AZURE_BEARER_TOKEN`` | JWT token for authenticating to Azure. | Both | ``eyJhbGciOiJIUzI1Ni...`` |
| ``API_MANAGEMENT_SERVICE_OUTPUT_FOLDER_PATH`` | Absolute path of directory to store artifacts.<br>Extractor will output its artifacts here.<br>Publisher will push the artifacts here to APIM. | Both | ``/tmp/artifacts`` |
| ``API_SPECIFICATION_FORMAT`` or ``apiSpecificationFormat`` | Default format for API specification files. See [here](https://github.com/Azure/apiops/wiki/APIM-resources#api-specification) for more details. | Extractor | ``Wadl``<br><br>``OpenApiV3Yaml`` |
| ``API_MANAGEMENT_SERVICE_NAME`` OR ``apimServiceName`` | Name of API management instance | Both | ``myapiminstance`` |
| ``AZURE_SUBSCRIPTION_ID`` | Subscription ID where APIM instance is located | Both | ``a85e4e0d-a203-40fd-9a3d-f7cfb3ed2b91`` |
| ``AZURE_RESOURCE_GROUP_NAME`` | Resource group where APIM instance is located | Both | ``apim-rg`` |
| ``COMMIT_ID`` | If specified, publisher will only publish changes that occurred in this commit. | Publisher | ``ca82a6dff817ec66f44342007202690a93763949`` |

# Can ApiOps override resource names using the configuration.[env].yaml file?

There's a distinction between resource names and resource properties. Resources are things like APIs, backends, named values, loggers, etc. Their names should be consistent across environments. Their properties however can be overridden across environments through configuration. e.g. a named value is a resource. Its name should be consistent across environments (fun01-key, for instance). Its value (properties.value) is a property that can be overridden. Note that APIM sometimes relies on the display name property (properties.displayName) of a resource rather than just its name. Named values used in policies fall in that category, for instance. In those cases, we would recommend keeping both name and properties.displayName consistent across environments.