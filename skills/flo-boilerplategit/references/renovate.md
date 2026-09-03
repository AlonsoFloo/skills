# Renovate Reference Template (`renovate.json`)

```json
{
    "$schema": "https://docs.renovatebot.com/renovate-schema.json",
    "extends": [
        "config:best-practices",
        ":maintainLockFilesWeekly",
        "abandonments:recommended",
        "security:minimumReleaseAgeNpm",
        "group:allNonMajor",
        ":assignAndReview(AlonsoFloo)"
    ],
    "automerge": false,
    "pinDigests": true,
    "recreateWhen": "auto",
    "minimumReleaseAge": "10 days",
    "internalChecksFilter": "strict",
    "semanticCommits": "enabled",
    "rebaseWhen": "behind-base-branch",
    "timezone": "UTC",
    "prHourlyLimit": 6,
    "prConcurrentLimit": 6,
    "labels": [
        "renovate"
    ],
    "pre-commit": {
        "enabled": true
    },
    "git-submodules": {
        "enabled": true
    },
    "apm": {
        "enabled": true
    },
    "vulnerabilityAlerts": {
      "enabled": true
    },
    "packageRules": [
        {
            "matchManagers": ["pre-commit"],
            "pinDigests": false
        }
    ]
}
```
