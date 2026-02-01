Python code example
```py title="add_num.py"
# Function to add two numbers
def add_two_numbers(num1, num2):
    
return num1 + num2
```
Powershell sample
```powershell title="powershell_test.ps1" linenums="1"
Write-Host "Hello from PowerShell"
$var = Get-Service
Write-Host "Hello, MkDocs!"
Get-Service -Name "Spooler"
```

bash sample
```bash title="smash.sh"
#!/bin/bash
echo "Hello from Bash"
export VAR="value"
```

cmd.exe sample
```batch title="command.bat"
@echo off
echo Hello from CMD
set VAR=value
```

Splunk example
```sql
index=main sourcetype=access_combined
| stats count by categoryId
| eval description="Total Events"
| table description, count
```