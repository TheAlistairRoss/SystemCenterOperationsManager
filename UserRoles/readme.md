# Export / Import SCOM User Roles

## Synopsis

This guide details how to using the custom Import and Export functions for SCOM User Roles

- [Export-SCOMUserRole](./Export-SCOMUserRole)
- [Import-SCOMUserRole](./Import-SCOMUserRole)

## Description

When using the `Get-SCOMUserRole` Cmdlet found in the OperationsManager PowerShell Module, the object returned provides various layers of information required to build a User Role within Operations Manager, however some of these parameters can appear to be quite cryptic because the return GUID's from the management group

```PowerShell
Get-SCOMUserRole -DisplayName "Contoso Operator"

Name        : Contoso Operator
DisplayName : Contoso Operator
Description :
Users       : {Contoso\aliross}

```

Though even this expanded still doesn't provide a complete insight to the configuration

```PowerShell
Get-SCOMUserRole -DisplayName "Contoso Operator" |format-list *

Id                 : c1881289-3ce0-4d8b-b9db-7903a62b5c02
Name               : Contoso Operator
DisplayName        : Contoso Operator
Description        :
IsSystem           : False
IsScopeFixed       : False
LastModified       : 11/03/2020 16:16:30
LastModifiedBy     : Contoso\aliross
ScopeId            : 13
ImpliedInstanceId  :
Scope              : Microsoft.EnterpriseManagement.Security.UserRoleScope
Profile            : Operator
ProfileDisplayName : Operator
Users              : {Contoso\aliross}
ManagementGroup    : MG1
ManagementGroupId  : 6063d62d-91e2-604c-d06c-e4d35801a8aa

```
Drilling down through the nested scope provides you with details of the various scoped types (though not all of these are applicable to Operations Manager)

```PowerShell
Get-SCOMUserRole -DisplayName "Contoso Operator" | Select-Object -ExpandProperty Scope

IsScopeFixed        : False
Objects             : {2e99e65e-ba92-05b6-88a2-858831aad96d, b07b5b54-315f-7a9c-ac96-a1304cafe068}
Classes             : {}
Views               : {Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean], Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean], Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean],
                      Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean]...}
NonCredentialTasks  : {Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean], Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean], Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean],
                      Microsoft.EnterpriseManagement.Common.Pair`2[System.Guid,System.Boolean]}
CredentialTasks     : {}
ConsoleTasks        : {563c11f8-3ec9-4a92-a89f-df7b7a533d69}
Templates           : {}
DashboardReferences : {29c561d3-a167-da46-43ec-260fd6b4bc67}
ManagementGroup     : MG1
ManagementGroupId   : 6063d62d-91e2-604c-d06c-e4d35801a8aa

```

## Process

Each of these objects are a list of GUID's which are unique to the management group. This means that these cannot be easily exported to another management group (For Example, performing a parallel upgrade or creating roles on test and production). To achieve this the `Export-SCOMUserRole` performs the following

1. Gets the configuration of all the user roles provided 

2. For every GUID, converts them to a readable and transferable format (Name, DisplayNames, ManagementPack and Version)

3. Writes the configuration to a custom XML

Then in turn the `Import-SCOMUserRole` function does

1. Imports the XML

2. Validates the UserRole

    1. Confirms the User role isn't a built in role
    2. Confirms it doesn't conflict with an exisitng role
        1. If there is an exisiting role, then it updates it
        2. If there isn't an exisitng role, create it.

3. For each scoped type, get the relevant object and add the guid, otherwise generate a warning (i.e. if it doesn't exisit in the new management group)     
    - If Overwrite is `$true` then existing scope types will be cleared and updated with the new configuration
    - If Overwrite is `$false` then existing scope types will retain their current configuration and any additional configuration will appended to this scope

## XML Output

The XML Output example

```XML
    <?xml version="1.0" encoding="UTF-8"?>
    <UserRoles>
        <UserRole Name="Contoso Operator" Id="4abb669b-8cd6-4e38-b74d-7dbb33db8787" IsSystem="False" Profile="Operator" ImpliedInstanceId="">
            <DisplayName>Contoso Operator</DisplayName>
            <Description>
            </Description>
            <ManagementGroup Id="6063d62d-91e2-604c-d06c-e4d35801a8aa" Name="Contoso-MG1" />
            <Scope ScopeId="9" IsScopeFixed="False">
            <Objects>
                <Object Name="Microsoft.Windows.Cluster.NodeRole.Group" ManagementPackName="Microsoft.Windows.Cluster.Management.Library" ManagementPackVersion="10.1.0.0">
                <DisplayName>Cluster Roles</DisplayName>
                <ManagementPackDisplayName>Windows Cluster Management Library</ManagementPackDisplayName>
                <Id>ff95b717-0883-3777-f3dd-160627395c69</Id>
                </Object>
            </Objects>
            <Classes />
            <Views>
                <View Name="Microsoft.Linux.NetworkAdapter.Health.DashboardView" ManagementPackName="Microsoft.Linux.Library" ManagementPackVersion="7.5.1070.0" Second="False">
                <DisplayName>Network Adapter Health</DisplayName>
                <ManagementPackDisplayName>Linux Operating System Library</ManagementPackDisplayName>
                <Id>03178a99-fa14-94db-5f7d-0026a99bfa11</Id>
                </View>
            </Views>
            <NonCredentialTasks>
                <NonCredentialTask Name="Microsoft.Windows.Server.AD.DomainController.DCDIAG" ManagementPackName="Microsoft.Windows.Server.AD.2016.Monitoring" ManagementPackVersion="10.0.0.0" Second="False">
                <DisplayName>DCDIAG</DisplayName>
                <ManagementPackDisplayName>Active Directory Domain Services for Microsoft Windows Server 2016 (Monitoring)</ManagementPackDisplayName>
                <Id>b7d29cac-a806-0b58-be4c-0e36ed8d8242</Id>
                </NonCredentialTask>
            </NonCredentialTasks>
            <CredentialTasks>
            </CredentialTasks>
            <ConsoleTasks>
                <ConsoleTask Name="" ManagementPackName="" ManagementPackVersion="">
                <DisplayName>All Console Tasks Automatically Approved</DisplayName>
                <ManagementPackDisplayName>
                </ManagementPackDisplayName>
                <Id>563c11f8-3ec9-4a92-a89f-df7b7a533d69</Id>
                </ConsoleTask>
            </ConsoleTasks>
            <Templates>
            </Templates>
            <DashboardReferences>
                <DashboardReference Name="Microsoft.SystemCenter.OperationsManager.ViewFolder.Root.SCOMTrendDashboard" ManagementPackName="Microsoft.SystemCenter.OperationsManager.SummaryDashboard" ManagementPackVersion="7.2.12213.0">
                <DisplayName>Management Group Health Trend</DisplayName>
                <ManagementPackDisplayName>Microsoft SystemCenter OperationsManager Summary Dashboard</ManagementPackDisplayName>
                <Id>da19a29a-a198-bc89-6c5b-02d50ee1ba1d</Id>
                </DashboardReference>
            </DashboardReferences>
            </Scope>
            <Users>
            <User>Contoso\aliross</User>
            <User>Contoso\TMiller</User>
            </Users>
        </UserRole>
    </UserRoles>
```
