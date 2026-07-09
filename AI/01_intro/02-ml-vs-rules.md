
### Rule based systems follows rules that humans explicitly write

```python
age = 20

if age >= 18:
    print("Adult")
else:
    print("Child")
    
    
# ######################

if username == "admin" and password == "123":
    login()
else:
    deny()
```

### Machine Learning Systems
Machine learning systems don't rely on manually written decision rules
instead they learn patterns from data

suppose you have
```bash
Email                    Spam?
-------------------------------
Win $1000                Yes
Meeting tomorrow         No
Lottery winner           Yes
Project report           No
```

the algorithm studies thousands of emails
