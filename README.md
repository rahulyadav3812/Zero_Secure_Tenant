# Zero Secure Tenant

A Zero Trust Identity and Access Management project for fog/edge computing built on Microsoft Entra ID P1 and Azure.

This repository documents an academic project that demonstrates how fog nodes can be treated as verifiable cloud identities instead of implicitly trusted network devices. The implementation combines Microsoft Entra ID, Azure IoT Hub, Azure IoT Edge, custom RBAC, and Azure Monitor / Log Analytics to enforce least privilege and maintain an audit trail.

Important note: the original report and presentation artifacts in the working folder contained sensitive environment details and credential-like values. This repository keeps a sanitized, GitHub-safe project summary instead of publishing those raw documents.

## Project Overview

Traditional fog computing environments often trust devices based on network location. That model increases the risk of lateral movement, compromised edge devices, and insider abuse. Zero Secure Tenant applies a Zero Trust approach where every fog node, service principal, and user must be explicitly authenticated and authorized before accessing cloud resources.

Core idea:
- Never trust, always verify
- Enforce least privilege
- Assume breach and log everything
- Validate identities at the infrastructure and application layers

## Objectives

- Implement a Zero Trust IAM architecture using Microsoft Entra ID
- Authenticate fog nodes through Azure identities / service principals
- Deploy Azure IoT Hub and IoT Edge as the fog computing layer
- Enforce least-privilege access with custom RBAC roles
- Collect audit evidence with Azure Monitor and Log Analytics
- Document real-world institutional governance constraints encountered during implementation

## Architecture Summary

The system was structured in five layers:

1. Identity Layer
   - Microsoft Entra ID
   - App Registration / Service Principal

2. Fog Infrastructure Layer
   - Azure IoT Hub

3. Fog Node Layer
   - Azure VM running Ubuntu 24.04
   - Azure IoT Edge runtime

4. Access Control Layer
   - Custom RBAC roles for administrator, operator, and auditor responsibilities

5. Monitoring and Audit Layer
   - Log Analytics Workspace
   - Diagnostic Settings
   - KQL audit queries

## Zero Trust Principles Applied

| Principle | Implementation |
|---|---|
| Never Trust, Always Verify | Each fog node authenticates before access to Azure resources |
| Least Privilege | Custom RBAC roles restrict actions to only what is required |
| Assume Breach | Activity and IoT events are logged centrally |
| Verify Explicitly | IoT Hub identity and TLS validation are checked continuously |
| Just-In-Time Access | Identified as a future enhancement requiring Entra ID P2 / PIM |

## Main Components

- Microsoft Entra ID P1
- Azure App Registration / Service Principal
- Azure IoT Hub (F1 tier used in the academic deployment)
- Azure VM for fog node simulation
- Azure IoT Edge runtime
- Custom Azure RBAC roles
- Azure Monitor + Log Analytics Workspace
- KQL-based audit and verification queries

## Implementation Notes

### Phase 1: Environment and Identity Setup
- Verified availability of an Azure for Students subscription and Entra ID P1 licensing
- Created an app registration to serve as the fog node identity anchor
- Treated the service principal as the cloud identity for the fog node

### Phase 2: Zero Trust Foundation
- Assessed tenant-level controls available in the academic environment
- Documented governance restrictions around security defaults, conditional access, and privileged identity features
- Pivoted to controls available at the subscription and resource layers

### Phase 3: Fog Computing Layer
- Deployed Azure IoT Hub
- Registered IoT Edge devices representing fog nodes
- Deployed an Ubuntu VM to simulate a fog node
- Installed Docker CE and Azure IoT Edge runtime
- Verified runtime health and upstream connectivity
- Authenticated the fog node identity against Entra ID

### Phase 4: Access Control and Monitoring
- Created custom RBAC roles to separate duties
- Configured Log Analytics for centralized audit logging
- Enabled diagnostics for IoT Hub and Azure activity logs
- Wrote KQL queries to capture IAM and IoT security evidence

## RBAC Model

The project used role separation to support Zero Trust access control:

- ZST-FogNode-Administrator
  - Broad IoT Hub administrative actions for managed operations
- ZST-FogNode-Operator
  - Read and operational visibility only
- ZST-FogNode-Auditor
  - Read-only access to diagnostics, log definitions, metrics, and security settings

This separation demonstrates least privilege and separation of duties.

## Validation and Results

The original report documented the following successful tests:

- T-01: IoT Edge system status verification
- T-02: IoT Edge connectivity verification
- T-03: Service principal authentication validation
- T-04: RBAC read permission test
- T-05: RBAC write denial test
- T-06: Role assignment audit log verification
- T-07: IoT Hub connectivity log verification

Summary of findings:
- IoT Edge runtime services were healthy
- Connectivity checks succeeded across required channels
- Fog node identity authentication worked as intended
- Authorized read actions succeeded
- Unauthorized destructive actions were denied
- KQL queries captured IAM and IoT audit evidence

## Sample KQL Queries

### Azure Activity / IAM Audit Trail
```kusto
AzureActivity
| project TimeGenerated, OperationNameValue, ActivityStatusValue, CallerIpAddress, Caller
| order by TimeGenerated desc
| take 20
```

### IoT Hub Connection Events
```kusto
AzureDiagnostics
| where ResourceType == "IOTHUBS"
| project TimeGenerated, OperationName, ResultType, ResultDescription
| order by TimeGenerated desc
| take 20
```

### Failed Operations
```kusto
AzureActivity
| where ActivityStatusValue == "Failure"
| project TimeGenerated, OperationNameValue, Caller, CallerIpAddress
| order by TimeGenerated desc
```

### Role Assignment / Infrastructure Changes
```kusto
AzureActivity
| where OperationNameValue contains "ROLEASSIGNMENT"
   or OperationNameValue contains "IOTHUBS"
   or OperationNameValue contains "VIRTUALMACHINES"
| project TimeGenerated, OperationNameValue, ActivityStatusValue, Caller
| order by TimeGenerated desc
| take 30
```

## Real-World Constraints Documented

A major part of the project was documenting what happens when Zero Trust is implemented inside a restricted institutional tenant. Observed limitations included:

- Read-only access to certain tenant-wide security settings
- Conditional Access policy creation blocked by insufficient privileges
- Privileged Identity Management unavailable without Entra ID P2
- Identity Protection unavailable without higher licensing
- Region and governance restrictions affecting deployment choices
- Student/non-admin access boundaries that mirror real enterprise governance

These constraints are part of the project outcome, not just limitations. They show how Zero Trust often has to be implemented progressively within organizational boundaries.

## Repository Contents

- `README.md` - sanitized project overview derived from the report
- `Azure Template.json` - exported Azure template used in the project
- `Architecture Diagram.png` - high-level system diagram
- `Key Vault.png` - supporting architecture artifact
- `docs/project-report.md` - expanded sanitized notes extracted from the final report
- `Output Folder/` - project evidence and supporting screenshots, preserved in folder structure:
  - `Connectivity Checks.png`
  - `Custom RBAC roles.png`
  - `Health checks.png`
  - `IOT HUBDelete.png`
  - `KQL Queries/`
  - `Restrictions/`
  - `Test Cases/`

## Reproducibility Notes

This repository is intended as a documentation and architecture reference. If you want to rebuild the project on a personal Azure account, use a fresh tenant and generate new identities, secrets, device registrations, and connection strings. Do not reuse any values from old reports, screenshots, or exports.

## Conclusion

Zero Secure Tenant demonstrates that fog nodes can be managed as explicit cloud identities within a Zero Trust architecture. By combining Entra ID, IoT Hub, IoT Edge, RBAC, and centralized logging, the project shows how authentication, authorization, and auditability can be enforced even in a constrained academic cloud environment.

## Contributor

- Rahul Yadav

## Academic Context

This work was developed as a B.Tech CSE project at BML Munjal University under faculty mentorship.
