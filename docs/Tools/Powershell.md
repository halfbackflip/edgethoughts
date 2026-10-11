# Select-String 

Below are some quick commands to search for urls, domains, emails, ipv4 addresses and specific file extensions using Powershell Select-String.

| Data Type | PowerShell Command Example | Description |
| :--- | :--- | :--- |
| **HTTP/HTTPS URLs** | `Select-String -Path file.txt -Pattern 'https?://\S+' -AllMatches | % {$_.Matches} | % {$_.Value}` | Extracts URLs case-insensitively (PowerShell regex is case-insensitive by default). |
| **Domains** | `Select-String -Path file.txt -Pattern '([a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}' -AllMatches | % {$_.Matches} | % {$_.Value}` | Extracts domain names. |
| **Email Addresses** | `Select-String -Path file.txt -Pattern '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' -AllMatches | % {$_.Matches} | % {$_.Value}` | Extracts email addresses. |
| **IPv4 Addresses** | `Select-String -Path file.txt -Pattern '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' -AllMatches | % {$_.Matches} | % {$_.Value}` | Extracts standard IPv4 addresses. |
| **Given File Extension** | `Select-String -Path file.txt -Pattern '\S+\.(exe|ps1)' -AllMatches | % {$_.Matches} | % {$_.Value}` | Extracts filenames ending in specific extensions. |

*Tip: In PowerShell, `Select-String` regex matching is case-insensitive by default, and `-AllMatches` combined with the pipeline expansion extracts just the matched substrings similar to `grep -o`.*