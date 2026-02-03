# Command Prompt
This section contains some of my favorite windows command prompt tools along with snippets of my favorite commands. 

## Pktmon
Pktmon or Packet Monitor "is an in-box, cross-component network diagnostics tool for Windows. It can be used for packet capture..." Most people do not seem to know that since Windows 10/Windows 2016 there is a built-in utility to perform packet captures on a Windows Host. Previously you would need wireshark or another application. I still reccommend analyzing the traffic in wireshark.

For reference:
[https://learn.microsoft.com/en-us/windows-server/networking/technologies/pktmon/pktmon](https://learn.microsoft.com/en-us/windows-server/networking/technologies/pktmon/pktmon)

### Cheat Sheet

| **Command** | **Description** |
| --------------|-------------------|
| `pktmon filter add help` | Open the help menu for adding filters | 
| `pktmon filter add <blah>` | Create a pktmon filter |
| `pktmon filter list` | List a pktmon filter |
| `pktmon filter list` | Remove all filters |
| `pktmon start -c` | Start capturing packets |
| `pktmon start --etw -m real-time` | Capture packets in real-time on the screen |
| `pktmon stop` | Stop capturing packets |
| `pktmon etl2txt <etl file path>` | Convert and export an etl file to text |
| `pktmon etl2pcap .\PktMon.etl --out Pktmon.pcapng` | Convert and export to a pcapng file |
| `pktmon counters` | List packet counters as a packet capture is occurring |
| `pktmon list` | List all components/interfaces which can capture packets |
| `pktmon status`| List current packet capture status

### Example 1
Capture all traffic. Convert the default .etl (Event Trace Log) format to a pcapng file.
```batch
pktmon start -c
pktmon stop
pktmon etl2pcap .\PktMon.etl --out Pktmon.pcapng
```

### Example 2
Create a filter to capture web and DNS traffic.
```batch
pktmon filter add HTTP -p 80
pktmon filter add HTTPS -p 80
pktmon filter add DNS -t UDP -p 53
pktmon start -c
pktmon stop
pktmon etl2pcap .\PktMon.etl --out Pktmon.pcapng
```

### Example 3
Run a test for one minute by pinging 8.8.8.8 
```powershell
$pktmonTime = (Get-Date).AddMinutes(1)
while ((Get-Date) -le $pktmonTime) {
    ping 8.8.8.8
    Start-Sleep -Seconds 10
}
```
Create a filter to capture ICMP traffic to 8.8.8.8.
```batch
pktmon filter add -i 8.8.8.8 -t ICMP
pktmon start --etw -m real-time
```

## net 

The net command has several uses. Here are a few! 

### Cheat Sheet

| **Command** | **Description** |
| --------------|-------------------|
| `net localgroup administrators` | List of every group with an admin account | 
| `net start` | See every service running on a system |
| `net user <username>` | View a given user information |
| `net use` | Lists all mapped network shares |
| `net view \\<computer or ip address>` | Lists all shares on a remote host |
| `net view /domain:<blah>` | Lists all systems registered with a given domain |
| `net user /domain` | Lists all users for a given domain |

### net view
The net `net view` command for enumerating shares on a remote system. 
This can be used by both a threat actor for [File and Directory Discovery](https://attack.mitre.org/techniques/T1083/).
From a defensive perspective it can be useful to monitor for these commands or use them to discover file shares to understand
where a threat actor may have moved laterally. 

Enumerate shares on a remote system.
```batch
net view \\<computer or ip address>
```

Collect shared folder names in an array and print to console.
```batch
$sharedFolders = (NET.EXE VIEW \\<computer or ip address>) 
$sharedFolders[7].split('  ')[0]
```

Print folder names to the console.
```batch
(net view \\<computer or ip address>) | % { if($_.IndexOf(' Disk ') -gt 0){ $_.Split('  ')[0] } }
```