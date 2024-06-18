# SOAP API operation policies ([issue](https://github.com/Azure/apiops/issues/399))
SOAP APIs don't store operation IDs in their specification files. As a result, APIM generates new operation IDs every time a WSDL file is imported. Because these IDs don't match what was extracted, the publisher fails when applying operation-level policies.

Here are reproduction steps:
- Create a SOAP API with an operation. APIM will generate a random ID for that operation.
- Assign a policy to the operation.
- Run the extractor. The artifacts below get generated:
  - `artifacts/apis/apiA/operations/operation1/policy.xml` (`operation1` is the randomly-generated ID).
  - `artifacts/apis/apiA/specification.wsdl` (this specification file does not include the generated ID `operation1`)
- Run the publisher. It fails when applying the operation policy.
  - First, it creates `apiA` and imports the ``specification.wsdl`` file. Because the WSDL file doesn't store operation IDs, APIM will create the operation with a new ID (`operation2`).
  - It then tries to apply the contents of ``policy.xml`` to ``operation1``. This fails, as there's no ``operation1`` under ``apiA``. That original operation has been recreated with a new ID (``operation2``).


Unfortunately there is currently no workaround for this, short of converting SOAP APIs to REST while importing WSDL files (see screenshot below). REST APIs do include operation IDs in their specification files.

![image](https://github.com/Azure/apiops/assets/31325620/a36eed47-b519-452a-aef3-2973bc6587f3)

If you have ideas on how to address this, please feel free to comment on the linked issue in the title.

# Overriding resource names
We often get asked how to create `apiA` in one environment and promote its contents to `apiB` in another environment. This is not supported. While ApiOps can override resource **properties**, it cannot override resource **names**.

Resources are things like named values, backends, APIs, operations, etc. Each resource has a name (aka ID). Many resources also have human-friendly display names. ApiOps uses the format `artifacts/.../{resource type}/{resource name}/...`.

Take this API:

![image](https://github.com/Azure/apiops/assets/31325620/dbcf04e7-84eb-4fc5-9b09-16bc1f7e2106)

It will be stored in `artifacts/apis/my-name-must-remain-consistent/apiInformation.json`. `apiInformation.json` will include `properties.displayName = i am a display name that can be overridden across environments`.

If you want this API to have a new display name in another environment, you can simply override its ``displayName`` property.

If you want it to have a new **name**, you'll have to rename the folder to `artifacts/apis/my-new-name/apiInformation.json` prior to publishing. You'll also have to make sure all references to the old name were updated in other resources (such as subscriptions, product apis, etc).