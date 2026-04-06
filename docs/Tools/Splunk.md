# Splunk
This section contains some snippets of Splunk commands. 

## Basic Datamodel SPL
| **Command** | **Description** |
| --------------|-------------------|
|`datamodelsimple`| List all data models |
| `\| datamodel Change`| Lists the fields in the Change datamodel |
|`\| datamodel Network_Traffic summariesonly=true search`| Search the Network Traffic datamodel |
|`\| tstats prestats=true summariesonly=true count from datamodel=Network_Traffic.All_Traffic by index sourcetype \| stats count by index sourcetype`| Lists the indexes and sourcetypes in the Network_Traffic datamodel|
|`\| datamodel Network_Traffic summariesonly=true search sourcetype=aws:cloudwatchlogs:vpcflow`| Search specific datamodel sourcetypes|
|`\| datamodel Vulnerabilities summariesonly=true search \| timechart span=1h count`| Create a timechart from a datamodel|
|`tstats count where sourcetype="<foo>" by sourcetype index _time \|  timechart count by index `| Create a timechart for counting events by index.|

## Datamodel SPL Baselining
List all the fields in a datamodel
```
| datamodel
| spath output=modelName modelName
| search modelName="Network_Traffic" 
| spath output=objectNameList objects{}.displayName 
| spath output=field_names objects{}.fields{}.displayName
| stats values(field_names) as field_names values(objectNameList) as Datasets by modelName
```
List all the fields in a datamodel using Splunk rest command.
```
| rest "/servicesNS/-/-/data/models"
| table title eai:appName eai:data
| spath input=eai:data objects{}.displayName
| spath input=eai:data objects{}.fields{}.fieldName
| table title eai:appName objects{}.displayName objects{}.fields{}.fieldName
```

## Datamodel Specific Commands

### Change Datamodel
Count stats from the change datamodel
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Change by _time, All_Changes.action, All_Changes.change_type, All_Changes.dest, All_Changes.dest_category, All_Changes.object, All_Changes.object_category, All_Changes.object_path, All_Changes.result, All_Changes.src, All_Changes.user, index, sourcetype
| `drop_dm_object_name(All_Changes)`
| table _time action change_type dest dest_category object object_category object_path result src index sourcetype
| sort - _time
```

### Event_Signatures
Count stats from the event signatures datamodel
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Event_Signatures where Signatures.signature_id IN ("4688","1") by _time, Signatures.dest, Signatures.dest_category, Signatures.dest_priority, Signatures.signature_id, Signatures.tag, Signatures.vendor_product, index, sourcetype, source 
| `drop_dm_object_name(All_Changes)` 
| sort - _time
```

### Log Ingestion
Check the log ingestion of a given sourcetype/index with a timechart
```
| `tstats` count where sourcetype="<blah>" by sourcetype index _time |  timechart count by index
```

### Authentication 
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Authentication where sourcetype="azure:aad:signin" by Authentication.action, Authentication.app, Authentication.user, Authentication.src, Authentication.signature
| sort -count
```

```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Intrusion_Detection.IDS_Attacks where NOT IDS_Attacks.src IN ("10.3.42.143","10.86.24.34","10.185.77.18","10.244.23.135")
```

```
| tstats summariesonly=t fillnull_value="MISSING" count by IDS_Attacks.src from datamodel=Intrusion_Detection.IDS_Attacks where NOT IDS_Attacks.src IN ("10.3.42.143","10.86.24.34","10.185.77.18","10.244.23.135")
```

### Web Datamodel
Search the Web Datamodel for Outbound Traffic
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Web where Web.url_domain IN ($field3$) Web.action IN ($field5$) by Web.url_domain, Web.uri_path, Web.action, Web.user, Web.app, Web.category, Web.src, Web.bytes_out, Web.dest, Web.dest_port, Web.http_method, Web.status, Web.is_Proxy, Web.http_referrer, index, sourcetype
| `drop_dm_object_name(Web)`
| sort -count
```
Filter the web datamodel by category; searching for specific ipv4 addresses. 
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Web where Web.category IN ("Compromised Websites", "Malicious Web Sites", "Potentially Unwanted Software", "Illegal or Questionable", "Hacking", "Peer-to-Peer File Sharing", "Phishing and Other Frauds", "Surveillance", "Suspicious Embedded Link", "Violence")
by _time, Web.action, Web.user, Web.category, Web.bytes_in, Web.bytes_out, Web.dest, Web.dest_port, Web.http_method, Web.url,Web.user_bunit, index, sourcetype
```

### Nework Traffic Datamodel
Search for a specific source ipv4 address.
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Network_Traffic.All_Traffic where All_Traffic.src IN ("202.63.218.19","69.42.81.14","35.81.84.184","54.215.84.169","115.211.28.95","79.106.137.115","177.155.134.68") by All_Traffic.src, All_Traffic.dest, All_Traffic.action, All_Traffic.dest_port, All_Traffic.bytes, sourcetype
| sort -count
```

### Network Sessions Datamodel
Search for network sessions filtered by given source/destination ipv4 addresses.
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Network_Sessions.All_Sessions where All_Sessions.src_ip IN (foo>,<bar>) by All_Sessions.src_ip, All_Sessions.dest_ip, All_Sessions.tag, All_Sessions.user, sourcetype
| sort -count
```

### File hashes Datamodel 
Search the file hashes datamodel for a given range of hashes
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=File_Hashes where File_Hashes.file_hash IN (foo>,<bar>) by File_Hashes.app, File_Hashes.file_hash, File_Hashes.file_name, File_Hashes.tag, File_Hashes.user, File_Hashes.user_bunit, sourcetype
| sort -count
```
Search the malware datamodel for the same.
```
| tstats summariesonly=t fillnull_value="MISSING" count from datamodel=Malware where Malware_Attacks.file_hash IN () by _time, Malware_Attacks.action, Malware_Attacks.dest, Malware_Attacks.file_name, Malware_Attacks.file_hash, Malware_Attacks.file_path, Malware_Attacks.signature, Malware_Attacks.user, Malware_Attacks.user_bunit, count, sourcetype
| sort -_time
```