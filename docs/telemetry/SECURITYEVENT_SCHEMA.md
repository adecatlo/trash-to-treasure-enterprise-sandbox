# 8. Current SecurityEvent Schema

The current `SecurityEvent` table contains a broad Windows security event schema.

The previously encountered assumption that fields such as `Process` and `ProcessName` were unavailable is **no longer valid** for the current workspace state.

The current schema includes, among many others:

### Event Identification

* `EventID`
* `EventRecordId`
* `EventLevelName`
* `Level`
* `Channel`
* `Opcode`
* `Task`
* `Version`

### Host / System

* `Computer`
* `Workstation`
* `WorkstationName`
* `SourceComputerId`
* `SystemProcessId`
* `SystemThreadId`
* `SourceSystem`
* `TimeGenerated`

### Authentication / Logon

* `Account`
* `AccountName`
* `AccountDomain`
* `TargetAccount`
* `TargetUser`
* `TargetUserName`
* `SubjectUserName`
* `SubjectDomainName`
* `SubjectUserSid`
* `LogonId`
* `LogonGuid`
* `LogonType`
* `LogonTypeName`
* `AuthenticationPackageName`
* `AuthenticationProvider`
* `AuthenticationServer`
* `FailureReason`
* `Status`
* `SubStatus`

### Process Information

* `Process`
* `ProcessId`
* `ProcessName`
* `CommandLine`
* `ParentProcessName`
* `NewProcessId`
* `NewProcessName`
* `CallerProcessId`
* `CallerProcessName`
* `SystemProcessId`

### Network Information

* `IpAddress`
* `IpPort`
* `ClientIPAddress`
* `ClientAddress`
* `RemoteIpAddress`
* `RemotePort`
* `CallingStationID`
* `CalledStationID`

### Account / Identity

* `SamAccountName`
* `UserPrincipalName`
* `MemberName`
* `MemberSid`
* `TargetSid`
* `TargetUserSid`
* `AccountType`
* `UserAccountControl`
* `PrimaryGroupId`
* `GroupMembership`

### Security / Object Information

* `ObjectName`
* `ObjectType`
* `ObjectServer`
* `HandleId`
* `AccessMask`
* `PrivilegeList`
* `MandatoryLabel`
* `SecurityDescriptor`

### File / Certificate / Service Information

* `FileHash`
* `FilePath`
* `FilePathNoUser`
* `ServiceName`
* `ServiceFileName`
* `ServiceStartType`
* `ServiceType`
* `CertificateDatabaseHash`
* `CACertificateHash`
* `CAPublicKeyHash`

### Event Payload

* `EventData`
* `RenderedDescription`
* `ParameterXml`
* `Properties`
* `AdditionalInfo`
* `AdditionalInfo2`
* `Message`

### Azure / Billing Metadata

* `AzureDeploymentID`
* `ManagementGroupName`
* `TenantId`
* `_BilledSize`
* `_IsBillable`
* `_ResourceId`
* `_SubscriptionId`

## KQL Guidance

The actual schema available in the workspace takes precedence over older training material.

When a training query fails:

1. Inspect the current table schema.
2. Determine whether the referenced field exists.
3. Determine whether the field contains the expected data type.
4. Adapt the query to the current schema.
5. Verify the result against actual telemetry.

Do not assume that a query from an older Microsoft training course represents the exact schema currently exposed by the lab.

The current schema supports detailed investigation of authentication, process creation, account activity, network information, and other Windows security events.
