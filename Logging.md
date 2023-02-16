The extractor and publisher use the [.NET logging API](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging). The easiest way to enable enhanced logging in your pipelines is to set environment variable ``Logging__LogLevel__Microsoft`` to either ``Debug`` or ``Trace``. You can do this in ADO by updating your ``apim-automation`` variable group:

![image](https://user-images.githubusercontent.com/31325620/214876645-edfbb325-01a3-4f6b-a795-fc37fef749d4.png)


### Debug
- Shows the URLs used in each API call. For instance, `Beginning request to put REST resource URI https://management.azure.com/subscriptions/***/resourceGroups/***/providers/Microsoft.ApiManagement/service/$(APIM_NAME)/namedValues/mynamedvalue?api-version=2021-12-01-preview...`

### Trace
- Shows the URLs used in each API call
- For PUT requests, shows the contents of the JSON. _**WE MAKE NO ATTEMPTS TO SCRUB THE JSON CONTENTS, SO SENSITIVE INFORMATION MAY APPEAR IN THE OUTPUT.**_