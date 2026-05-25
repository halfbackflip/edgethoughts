# Windows
This section contains general guides on how to profile a windows system during an invesigation using only the available tools, i.e. powerhshell and the command prompt.  

Many of these commands will use powershell to query Common Information Model (CIM) instances. CIM is a cross-platform standard for managing system data. The standard is maintained by [https://www.dmtf.org/standards/cim](https://www.dmtf.org/standards/cim). The equivalent powershell or cmd commands can also be used. 

## System Profiling
The first step is to profile the system. This includes gathering basic system information such as hostname, network adapters, operating system and time zone. 

List the data and timezone.
```powershell
Get-Date ; Get-TimeZone
```

Retrieve the configuration for enabled network adapters
```powershell
Get-CimInstance win32_networkadapterconfiguration -Filter IPEnabled=TRUE | ft DNSHostname, IPAddress, MACAddress
```

Retrieve the operating system details including the last boot time.
```powershell
Get-CimInstance -ClassName Win32_OperatingSystem | fl CSName, Version, BuildNumber, InstallDate, LastBootUpTime, OSArchitecture
```

List all the drives connected to the system
```powershell
Get-CimInstance -ClassName Win32_Volume | Select-Object -Property DriveLetter, Label, FileSystem, Capacity, FreeSpace
```

## Users / Groups
Threat actors can create new accounts or manipulate existing accounts. The following are some basic commands to profile the users on a windows system.

Retrieve all logged on users.
```powershell
Get-CimInstance -ClassName Win32_LoggedOnUser | Select-Object -Property Dependent, Antecedent
```

Retrieve all user accounts.
```powershell
Get-CimInstance -ClassName Win32_UserAccount | Select-Object -Property Name, Caption, AccountType, SID, SIDType, InstallDate, Domain, LocalAccount, Disabled, Description, PasswordRequired
```

Retrieve all user accounts and their group membership.
```powershell
Get-CimInstance -ClassName Win32_GroupUser | Select-Object -Property @{Name='Group'; Expr={$_.GroupComponent.Name}}, @{Name='Users'; Expr={$_.PartComponent.Name}}
``` 

## Network Activity
These commands look at the network activity and configuration of a system. 

List all the nework connections and correlate them to their processes. 
```powershell
Get-NetTCPConnection | Select-Object -Property Local*, Remote*, State, OwningProcess,` @{n="ProcName";e={(Get-Process -Id $_.OwningProcess).ProcessName}},` @{n="ProcPath";e={(Get-Process -Id $_.OwningProcess).Path}} `
```

List all mounted Network Shares. See also: Net view.
```powershell
Get-CimInstance -Class Win32_Share | Select-Object -Property Name, Path, Description 
```

List all the configured DNS Servers on a host.
```powershell
Get-CimInstance Win32_NetworkAdapterConfiguration -Filter "IPEnabled = True" | Select-Object Description, DNSServerSearchOrder
```

Dump the existing DNS Cache.
```powershell
Get-DnsClientCache | Select-Object -Property Entry, Type, Data, TimeToLive
```

Read the DNS hosts file
```powershell
Get-Content "C:\Windows\System32\drivers\etc\hosts"
```

## Get-WinEvent
Get-WinEvent is a powershell module for querying windows event logs available on Windows Server 2008 and later. 
Note: Get-EventLog is a legacy powershell module with limited capability.

List available window event logs.
```powershell
Get-WinEvent -ListLog * | Select-Object LogName, RecordCount, IsClassicLog, IsEnabled, LogType | Format-Table -AutoSize
```

List the associated log providers for each log. 
```powershell
Get-WinEvent -ListProvider * | Format-Table -AutoSize
```

Retrieve the first 100 events from the Security Log.
```powershell
Get-WinEvent -LogName 'Security' -MaxEvents 100 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Retrieve the first 100 events from a local windows event log.
```powershell
Get-WinEvent -Path '<PATH>' -MaxEvents 100 | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Filtering the security log to search for failed and successful logon events
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4624,4625} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Filtering the security log to search for failed and sucessful logon events from May 01 to June 01 2025. 
```powershell
$startDate = (Get-Date -Year 2025 -Month 5 -Day 01).Date
$endDate   = (Get-Date -Year 2025 -Month 6 -Day 01).Date
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4624,4625; StartTime=$startDate; EndTime=$endDate} | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Filtering the security log to search for failed and sucessful logon type 10 (RDP) events using an xml filter. 
```powershell
$Query = @"
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">
      *[System[(EventID=4624 or EventID=4625)]]
      and
      *[EventData[Data[@Name='LogonType']='10']]
    </Select>
  </Query>
</QueryList>
"@
Get-WinEvent -FilterXML $Query | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Filtering the security log to search for failed and sucessful logon type 10 (RDP) events using an xpath filter. 
```powershell
Get-WinEvent -LogName Security -FilterXPath "*[System[(EventID=4624 or EventID=4625)] and EventData[Data[@Name='LogonType']='10']]" | Select-Object TimeCreated, ID, ProviderName, LevelDisplayName, Message | Format-Table -AutoSize
```

Filtering the security log to search for 4625 events where the SubjectUserSid is S-1-5-18. 
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} | Where-Object {$_.Properties[0].Value -like "S-1-5-18"} | Format-List
```

## Startup
<TODO>

## Registry
<TODO>

## Services
<TODO>

## Scheduled Tasks
<TODO>
