# Grep Cheat Sheet

Some quick commands to search for urls, domains, emails, ipv4 addresses and specific file extensions.

| Data Type | Command Example | Description | 
| ----- | ----- | ----- | 
| **HTTP/HTTPS URLs** | `grep -oEi 'https?://[^[:space:]]+' file.txt` | Matches URLs regardless of capitalization (e.g., HTTP://, https://). | 
| **Domains** | `grep -oEi '([a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}' file.txt` | Matches domain names safely across any case variations. | 
| **Email Addresses** | `grep -oEi '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file.txt` | Matches email addresses regardless of username or domain case. | 
| **IPv4 Addresses** | `grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' file.txt` | Extracts standard IPv4 addresses (e.g., 192.168.1.1). |
| **Given File Extension** | `grep -oEi '[^[:space:]]+\.(exe|ps1)' file.txt` | Matches filenames ending in specific extensions (like .exe or .ps1). | 

*Tip: The `-i` flag makes the search case-insensitive, while `-o` prints only the matching text and `-E` enables extended regular expressions.*