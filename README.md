# renovate-versioning

## desciption
I try to update the Version defined in the [openldap.spec](./openldap.spec). As datasource I'm using `github-tags` of the upstream repository https://github.com/openldap/openldap. The problem is, that the upstream repository uses a different versioning scheme than the spec file. The tags in the upstream repository use `_` to separate the major/minor/patch versions while in the spec file the version is represented in semver.

## current configuration
The current configuration uses `regexManagers`. There is also a regex `versioning` defined in the `packageRules` which extracts the major/minor/patch versions from the upstream tags. Running renovate in debug mode with this configuration, results in the following error:

```
DEBUG: manager.getUpdatedPackageFiles() reuseExistingBranch=false (repository=package-builds/openldap, branch=renovate/openldap-2.x)
DEBUG: Starting search at index 391 (repository=package-builds/openldap, packageFile=openldap.spec, branch=renovate/openldap-2.x)
       "depName": "openldap"
DEBUG: Found match at index 391 (repository=package-builds/openldap, packageFile=openldap.spec, branch=renovate/openldap-2.x)
       "depName": "openldap"
DEBUG: Value is not updated (repository=package-builds/openldap, packageFile=openldap.spec, branch=renovate/openldap-2.x)
       "manager": "regex",
       "expectedValue": "2_6_4",
       "foundValue": "2"
 WARN: Error updating branch: update failure (repository=package-builds/openldap, branch=renovate/openldap-2.x)
DEBUG: getPrCache() (repository=package-builds/openldap)
```

Initially I thought this is because the regex used in `matchStrings` only matches the semver version and would thus only recognize the first digit as version. But I don't see why renovatebot would compare the new version against the regex defined in `matchStrings`. Nevertheless I updated the `matchStrings` to also match the version using `_`: `"Version:\\s+(?<currentValue>[\\d\\._]+)"`. With this configuration renovatebot creates a PR but replaces the semver string with the string present in the upstream tags (from `2.6.2` to `2_6_4`).
