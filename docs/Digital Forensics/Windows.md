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

## Startup
<TODO>

## Registry
<TODO>

## Services
<TODO>

## Scheduled Tasks
<TODO>
