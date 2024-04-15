# Status Bot - Specifications

**Company/Team Name:** Company, Inc.\
**Author:** Emmanuel Alozie\
**Date:** 15th April 2024

## Overview

The Status Bot is an internal tool designed to streamline the process of notifying our engineers of the status of our smoke tests within `<Project Name>`. This tool is intended for:

### Customers

- `<Project Name>` Developers / Engineers (On-Call Employees included)
- `<Project Name>` Management

## Implementation

We aim to design the tool to be versatile, allowing the addition of custom rules to our unique workflows. Considerations include our tenants and cloud platforms:

- GCP
  - Dev
  - Staging
- Azure
  - Staging
  - Prod

Each smoke test scenario will be tagged as critical in severity moving forward. However, we will allow for a degree of flakiness/nondeterminism for individual API calls.

## Architecture

Three primary variables need to be determined:

1. **Sample Size** - Number of test results in history to reference
2. **Pass Rate** - What constitutes a pass (percentage of scenarios passed)

**For PROD environments:**
- Every scenario must pass for the workflow run to pass.

**For STAGING environments:**
- Every document upload / requests to the claims service must pass for the workflow run to pass

Test results will be published to Slack in the `E2E-Status` channel.

## Technologies Used

The technologies and languages we will use for this tool are as follows:

- **Python Programming Language** - To write the script that performs the logic and sends the message to Slack using the Requests library.
- **GitHub Actions** - The script will interface with a GitHub Actions runner, set to execute every 5 hours.
- **GitHub Pages** - To checkout the `gh-pages` branch and add the runner to our GitHub Actions workspace, as this is where our media/run history is stored.
- **Docker** - To create an image containing all necessary packages and other dependencies needed to successfully run the tool. This will be self-hosted.

## Scenarios

### Scenario 1: At least 75% of tests pass

```markdown
GIVEN the last 4 test run results:
- Latest Run: PASSED
- Latest Run -1: FAILED
- Latest Run -2: PASSED
- Latest Run -3: PASSED

WHEN we execute our tool

THEN a message to Slack is sent with "[TENANT]: [PASSED_STATUS_SYMBOL]"
```

### Scenario 2: 25% or fewer tests pass

```markdown
GIVEN the last 4 test run results:
- Latest Run: PASSED
- Latest Run -1: FAILED
- Latest Run -2: FAILED
- Latest Run -3: FAILED

WHEN we execute our tool

THEN a message to Slack is sent with "[TENANT]: [FAILED_STATUS_SYMBOL]; <x>% or less of last 4 test runs PASSED"
```

### Scenario 3: Between 25% and 75% of tests pass

```markdown
GIVEN the last 4 test run results:
- Latest Run: PASSED
- Latest Run -1: FAILED
- Latest Run -2: FAILED
- Latest Run -3: PASSED

WHEN we execute our tool

THEN a message to Slack is sent with "[TENANT]: [WARNING_STATUS_SYMBOL]; <x>% or less of last 4 test runs PASSED"
```

---
