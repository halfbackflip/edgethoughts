# Chainsaw

Chainsaw is one of my favorite tools. It can search through various Windows forensics artifacs quickly. One of the most basic use cases is searching Windows Event logs for keywords. Chainsaw also supports Sigma and can match on an entire folder of Sigma rules when hunting through logs. 

| **Command** | **Description** |
| --------------|-------------------|
| `chainsaw search --skip-errors "powershell.exe"` | Search all logs in the current directory for powershell.exe | 
| ``` chainsaw hunt ~/dfir/logs/--sigma ~/dfir/rules/sigma/rules/ --mapping ~/dfir/mappings/sigma-event-logs-all.yml``` | Search the dfir/logs by matching against an entire sigma repository |
| `./chainsaw search -t 'Event.System.EventID: =4104' /dfir/logs/` | Search all dfir logs for powershell script block events |
|```./chainsaw search -e "^.*\.iso$``` | Search dfir logs for iso file types using regex. |
|```./chainsaw analyse shimcache ./SYSTEM --regexfile ./analysis/shimcache_patterns.txt``` | Search the shimcache based on a regex file |
|```./chainsaw dump ./SOFTWARE.hve --json --output ./output.json``` | Dump the software hve to json |