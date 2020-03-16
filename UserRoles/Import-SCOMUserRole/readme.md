# Import-SCOMUserRole

## SYNOPSIS
Imports SCOM User Roles using configuration based on the output of Export-SCOMUserRole

## SYNTAX

### Default (Default)

```
Import-SCOMUserRole [-FilePath] <string> [[-Overwrite] <bool>]  [<CommonParameters>]
```

## DESCRIPTION

This cmdlet takes the input from the Export-SCOMUserRole cmdlet, in the form of .XML.

It then checks to see if the user role already exists, doesn't conflict with the existing profile and creates the role.
It will append or overwrite existing user role scope depending on the $Overwrite Parameter (default is $false)

Built in System Roles will not be ammended.
Only the following Profiles wil be ammended:  "Author" , "AdvancedOperator" , "Operator" , "ReadOnlyOperator"

If the Object, View that is being scoped within this role doesn't exist, then a warning message will notify you as it cannot be found

See NOTES for the XML format

## EXAMPLES

### Example 1: Import the UserRole configuration Xml and overwrite the exising configuration

This example imports the userrole.xml, generated by Export-SCOMUserRole and overwrites any existing configuration of the scope


```powershell
Import-SCOMUserRoles -FilePath "C:\temp\userroles.xml" -Overwrite $true
```


## PARAMETERS

### -FilePath

Specifies the file location that contains the xml configuration from `Export-SCOMUserRole`

```yaml
Type: String[]
Parameter Sets: (All)
Aliases: 

Required: True
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -Overwrite

Boolean value which specifies if the exisitng configuration should be overwritten or appended.

```yaml
Type: bool[]
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: True
```

### CommonParameters

This cmdlet supports the common parameters: -Debug, -ErrorAction, -ErrorVariable,
-InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose,
-WarningAction, and -WarningVariable. For more information, see
[about_CommonParameters](https://go.microsoft.com/fwlink/?LinkID=113216).

## INPUTS

### None

You cannot pipe input to this cmdlet

## OUTPUTS

### PSCustomObject, File

This cmdlet returns objects that represent the configured User Roles on the computer and an xml file to the path specified.

## NOTES

### Input .xml format example

The following attributes are not used for the Import-SCOMUserRole:
    
    <UserRole Id ="">   
    <UserRole ImpliedInstance="">   
    <ManagementGroup Id="">   
    <ManagementGroup Name="">   
    <Scope ScopeId="">   
    <Scope IsScopedFixed="">   

and the following elements for each scope type:

    <DisplayName>
    <ManagementPackDisplayName>
    <Id>

These are all used for configuration capture and to make the User roles readable. They are not required by the Import-SCOMUserRole, but are generated by the Export-SCOMUserrole

If Cloning roles, ammed the following attributes and parameters

    <UserRole Name="">           This must be unique within a Management Group
    <UserRole IsSystem="">       This must be "False"
    <UserRole Profile="">        This can be changed to one of the following: "Author" , "AdvancedOperator" , "Operator" , "ReadOnlyOperator". Other Profiles have not been tested using this script
    <UserRole DisplayName="">    This is how the profile will look in the Console

#### XML Example

    <?xml version="1.0" encoding="UTF-8"?>
    <UserRoles>
        <UserRole Name="Final Operator" Id="4abb669b-8cd6-4e38-b74d-7dbb33db8787" IsSystem="False" Profile="Operator" ImpliedInstanceId="">
            <DisplayName>Final Operator</DisplayName>
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
            <User>Contoso\JWilliams</User>
            <User>Contoso\TMiller</User>
            </Users>
        </UserRole>
    </UserRoles>


## RELATED LINKS

[Export-SCOMUserRole](Export-SCOMUserRole.md)

[Get-SCOMUserRole](https://docs.microsoft.com/en-us/powershell/module/operationsmanager/get-scomuserrole?view=systemcenter-ps-2019)