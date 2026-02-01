# Command Prompt
This section contains some of my favorite windows command prompt tools along with snippets of my favorite commands. 

## Pktmon
Pktmon is a "Packet Monitor (Pktmon) is an in-box, cross-component network diagnostics tool for Windows. It can be used for packet capture..." Most people do not seem to know that since Windows 10/Windows 2016 there is a built-in utility to perform packet captures on a Windows Host. Previously you would need wireshark or another application. I still reccommend analyzing the traffic in wireshark.

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