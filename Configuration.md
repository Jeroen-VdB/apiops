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