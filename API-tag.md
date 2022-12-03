### API tag
API tags are extracted in ``artifacts/apis/apiName/tags.json``. You can override this in configuration like this:
```yaml
apis:
    - name: apiName
      tags:
        - name: tagA
        - name: tagB
```

Tags defined in configuration fully override tags defined in ``tags.json``. In other words:

| Given configuration tags | Given ``tags.json`` tags | Given current tags in APIM | After publisher runs |
|:--|:--|:--|:--|
| tagA, tagB | tagC | tagD | tagA, tagB |
| Array set to empty | tagA | tagA | No tags
| Not specified | tagA | tagB | tagA