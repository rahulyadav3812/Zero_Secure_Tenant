# Zero Secure Tenant - Sanitized Project Report Notes

This document is a sanitized markdown adaptation of the final project report. It removes credential-like values and other environment-specific sensitive details while preserving the technical architecture, methodology, results, and lessons learned.

## Title

Zero Secure Tenant on Microsoft Entra ID

## Abstract

Zero Secure Tenant is an IAM-based fog computing security project implemented on Microsoft Entra ID P1 and Azure. The system applies Zero Trust principles so that every fog node user and service principal must authenticate before accessing cloud resources. The architecture demonstrates the principle of "never trust, always verify" by using Azure IoT Hub, Azure IoT Edge, custom RBAC roles, and Azure Monitor / Log Analytics. The project was developed in an academic Azure environment and also documents the governance and licensing limitations encountered there.

## Problem Statement

Traditional fog computing deployments often rely on network-based trust. Devices inside a trusted segment can inherit broad access, which creates exposure to lateral movement, compromised edge devices, and insider threats. The project addresses this by moving to an identity-first architecture where each fog node carries a verifiable cloud identity and is limited to only the permissions it actually needs.

## Objectives

- Build a Zero Trust IAM architecture using Microsoft Entra ID
- Authenticate fog nodes as cloud identities
- Use Azure IoT Hub and IoT Edge as the fog infrastructure layer
- Enforce least privilege with custom RBAC roles
- Monitor actions and access attempts through Azure logging and KQL
- Record institutional constraints as realistic implementation findings

## Methodology

### Zero Trust Principles

- Never Trust, Always Verify
- Least Privilege Access
- Assume Breach
- Verify Explicitly
- Just-In-Time access as a recommended enhancement for future work

### System Layers

1. Identity: Microsoft Entra ID and app registration
2. Infrastructure: Azure IoT Hub
3. Fog Node: Azure VM with Ubuntu and IoT Edge
4. Access Control: custom RBAC roles
5. Audit and Monitoring: Log Analytics and KQL queries

## Implementation Summary

### 1. Account Verification and Environment Setup
- Confirmed Azure subscription availability
- Confirmed Entra ID P1 access
- Created app registration for fog node identity

### 2. Zero Trust Foundation
- Reviewed tenant controls available to non-admin academic users
- Documented restrictions on security defaults and conditional access
- Noted that PIM and Identity Protection would require higher licensing

### 3. Fog Computing Layer
- Deployed Azure IoT Hub
- Registered IoT Edge devices to represent fog nodes
- Deployed Ubuntu VM to simulate a physical fog node
- Installed Docker CE and Azure IoT Edge runtime
- Verified runtime health and network readiness
- Authenticated the fog node identity through Entra ID

### 4. Architecture Hardening and Monitoring
- Created custom RBAC roles
- Set up a Log Analytics workspace
- Forwarded diagnostic and activity logs
- Ran KQL queries for IAM and IoT evidence collection

## Custom RBAC Roles

### Administrator
Permissions for higher-trust IoT Hub administrative operations.

### Operator
Read and statistics access for operational visibility without destructive permissions.

### Auditor
Read-only access to logs, metrics, definitions, and security-related evidence.

## Testing and Validation

### Test Suite
- T-01: IoT Edge system status
- T-02: IoT Edge connectivity check
- T-03: Service principal authentication
- T-04: RBAC read permission test
- T-05: RBAC write denial test
- T-06: Role assignment audit verification
- T-07: IoT Hub action / connectivity log verification

### Key Results
- IoT Edge services were running and ready
- Connectivity checks passed successfully
- Identity-based authentication worked
- Authorized read actions succeeded
- Unauthorized delete actions were denied
- Role assignments and IoT actions appeared in audit logs

## Example Audit Queries

### Azure Activity
```kusto
AzureActivity
| project TimeGenerated, OperationNameValue, ActivityStatusValue, CallerIpAddress, Caller
| order by TimeGenerated desc
| take 20
```

### IoT Hub Diagnostics
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

## Institutional Constraints Observed

The project documented multiple governance and licensing boundaries, including:
- Read-only tenant-level settings for student users
- Conditional Access creation blocked for non-admin accounts
- Need for Entra ID P2 to use PIM / Just-In-Time administrative workflows
- Need for higher licensing for some advanced identity protections
- Region and policy restrictions affecting deployment decisions

These findings helped show that Zero Trust adoption is often incremental and shaped by governance, not just architecture.

## Conclusion

The project successfully demonstrated a Zero Trust fog computing architecture in Azure by treating fog nodes as explicitly authenticated identities. It validated least privilege with RBAC, demonstrated continuous verification through runtime and connectivity checks, and used centralized logging to support auditability. It also highlighted a realistic operational lesson: implementing Zero Trust in institutional or enterprise environments requires adapting to licensing, policy, and permission boundaries.
