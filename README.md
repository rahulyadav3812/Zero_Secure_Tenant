# Zero Secure Tenant Using Microsoft Entra ID P1

[![Microsoft Entra ID](https://img.shields.io/badge/Identity-Microsoft%20Entra%20ID-0078D4?logo=microsoftazure&logoColor=white)](docs/project-report.md)
[![Azure](https://img.shields.io/badge/Cloud-Microsoft%20Azure-0089D6?logo=microsoftazure&logoColor=white)](docs/project-report.md)
[![Zero Trust](https://img.shields.io/badge/security-Zero%20Trust-6f42c1)](docs/project-report.md)
[![IoT Edge](https://img.shields.io/badge/fog-Azure%20IoT%20Edge-0A66C2)](docs/project-report.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Zero Trust identity and access management project for fog and edge computing on Microsoft Entra ID and Azure.

## Overview

Zero Secure Tenant is an academic cloud-security project that demonstrates how fog nodes can be treated as verifiable cloud identities instead of implicitly trusted network devices. The implementation combines Microsoft Entra ID, Azure IoT Hub, Azure IoT Edge, custom RBAC, and Azure Monitor / Log Analytics to enforce least privilege and maintain an audit trail.

Important note: the original report and presentation artifacts in the working folder contained sensitive environment details and credential-like values. This repository keeps sanitized project documentation instead of publishing unsafe raw materials as the primary narrative.

For a cleaner technical write-up, see `docs/project-report.md`.

## Why this project matters

- Shows how Zero Trust concepts can be applied to fog and edge computing
- Connects identity security with cloud infrastructure and IoT operations
- Demonstrates least privilege and auditability in a realistic Azure environment
- Documents governance and licensing constraints that affect real deployments
- Highlights practical security engineering instead of only theoretical architecture

## Skills demonstrated

This project highlights skills that are useful to recruiters evaluating cloud, IAM, and cybersecurity work:

- Microsoft Entra ID and Azure identity architecture
- Zero Trust security design and access-control modeling
- Azure IoT Hub and Azure IoT Edge deployment concepts
- Service principal and application registration workflows
- Custom RBAC role design and least-privilege enforcement
- Cloud monitoring, diagnostics, and Log Analytics integration
- Kusto Query Language (KQL) for audit and security analysis
- Technical risk documentation and security-focused reporting
- Working within governance, licensing, and tenant restrictions
- Sanitizing architecture evidence for safe public documentation

## Project overview

Traditional fog computing environments often trust devices based on network location. That model increases the risk of lateral movement, compromised edge devices, and insider abuse. Zero Secure Tenant applies a Zero Trust approach where every fog node, service principal, and user must be explicitly authenticated and authorized before accessing cloud resources.

Core idea:
- Never trust, always verify
- Enforce least privilege
- Assume breach and log everything
- Validate identities at the infrastructure and application layers

## Objectives

- Implement a Zero Trust IAM architecture using Microsoft Entra ID
- Authenticate fog nodes through Azure identities and service principals
- Deploy Azure IoT Hub and IoT Edge as the fog computing layer
- Enforce least-privilege access with custom RBAC roles
- Collect audit evidence with Azure Monitor and Log Analytics
- Document real-world institutional governance constraints encountered during implementation

## Architecture summary

The system was structured in five layers:

1. Identity layer
   - Microsoft Entra ID
   - App Registration / Service Principal
2. Fog infrastructure layer
   - Azure IoT Hub
3. Fog node layer
   - Azure VM running Ubuntu 24.04
   - Azure IoT Edge runtime
4. Access control layer
   - Custom RBAC roles for administrator, operator, and auditor responsibilities
5. Monitoring and audit layer
   - Log Analytics Workspace
   - Diagnostic Settings
   - KQL audit queries

## Zero Trust principles applied

| Principle | Implementation |
|---|---|
| Never Trust, Always Verify | Each fog node authenticates before access to Azure resources |
| Least Privilege | Custom RBAC roles restrict actions to only what is required |
| Assume Breach | Activity and IoT events are logged centrally |
| Verify Explicitly | IoT Hub identity and TLS validation are checked continuously |
| Just-In-Time Access | Identified as a future enhancement requiring Entra ID P2 / PIM |

## Main components

- Microsoft Entra ID P1
- Azure App Registration / Service Principal
- Azure IoT Hub
- Azure VM for fog node simulation
- Azure IoT Edge runtime
- Custom Azure RBAC roles
- Azure Monitor + Log Analytics Workspace
- KQL-based audit and verification queries

## Implementation notes

### Phase 1: Environment and identity setup
- Verified availability of an Azure for Students subscription and Entra ID P1 licensing
- Created an app registration to serve as the fog node identity anchor
- Treated the service principal as the cloud identity for the fog node

### Phase 2: Zero Trust foundation
- Assessed tenant-level controls available in the academic environment
- Documented governance restrictions around security defaults, conditional access, and privileged identity features
- Pivoted to controls available at the subscription and resource layers

### Phase 3: Fog computing layer
- Deployed Azure IoT Hub
- Registered IoT Edge devices representing fog nodes
- Deployed an Ubuntu VM to simulate a fog node
- Installed Docker CE and Azure IoT Edge runtime
- Verified runtime health and upstream connectivity
- Authenticated the fog node identity against Entra ID

### Phase 4: Access control and monitoring
- Created custom RBAC roles to separate duties
- Configured Log Analytics for centralized audit logging
- Enabled diagnostics for IoT Hub and Azure activity logs
- Wrote KQL queries to capture IAM and IoT security evidence

## RBAC model

The project used role separation to support Zero Trust access control:

- ZST-FogNode-Administrator
  - Broad IoT Hub administrative actions for managed operations
- ZST-FogNode-Operator
  - Read and operational visibility only
- ZST-FogNode-Auditor
  - Read-only access to diagnostics, log definitions, metrics, and security settings

This separation demonstrates least privilege and separation of duties.

## Validation and results

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

## Sample KQL queries

### Azure Activity / IAM audit trail
```kusto
AzureActivity
| project TimeGenerated, OperationNameValue, ActivityStatusValue, CallerIpAddress, Caller
| order by TimeGenerated desc
| take 20
```

### IoT Hub connection events
```kusto
AzureDiagnostics
| where ResourceType == "IOTHUBS"
| project TimeGenerated, OperationName, ResultType, ResultDescription
| order by TimeGenerated desc
| take 20
```

### Failed operations
```kusto
AzureActivity
| where ActivityStatusValue == "Failure"
| project TimeGenerated, OperationNameValue, Caller, CallerIpAddress
| order by TimeGenerated desc
```

### Role assignment / infrastructure changes
```kusto
AzureActivity
| where OperationNameValue contains "ROLEASSIGNMENT"
   or OperationNameValue contains "IOTHUBS"
   or OperationNameValue contains "VIRTUALMACHINES"
| project TimeGenerated, OperationNameValue, ActivityStatusValue, Caller
| order by TimeGenerated desc
| take 30
```

## Repository contents

- `README.md` - sanitized project overview derived from the report
- `LICENSE` - repository license
- `Azure Template.json` - exported Azure template used in the project
- `Architecture Diagram.png` - high-level system diagram
- `Key Vault.png` - supporting architecture artifact
- `docs/project-report.md` - expanded sanitized notes extracted from the final report
- `Output Folder/` - project evidence and supporting screenshots

## Reproducibility notes

This repository is intended as a documentation and architecture reference. If you want to rebuild the project on a personal Azure account, use a fresh tenant and generate new identities, secrets, device registrations, and connection strings. Do not reuse any values from old reports, screenshots, or exports.

## Future work

Potential next steps for extending Zero Secure Tenant:

- add Conditional Access and stronger adaptive identity controls in a tenant with the required privileges
- integrate Privileged Identity Management for just-in-time administrative access
- connect alerts to Microsoft Sentinel or another SIEM workflow
- automate more deployment steps with Bicep, Terraform, or deployment pipelines
- extend the design to multiple fog nodes and larger IoT device fleets
- add stronger secret management patterns around Key Vault and rotation workflows
- evaluate device attestation and stronger workload-identity patterns for edge nodes

## Conclusion

Zero Secure Tenant demonstrates that fog nodes can be managed as explicit cloud identities within a Zero Trust architecture. By combining Entra ID, IoT Hub, IoT Edge, RBAC, and centralized logging, the project shows how authentication, authorization, and auditability can be enforced even in a constrained academic cloud environment.

## Contributor

- Rahul Yadav

## Academic context

This work was developed as a B.Tech CSE project at BML Munjal University under faculty mentorship.

## License

This project is released under the MIT License. See `LICENSE` for details.
