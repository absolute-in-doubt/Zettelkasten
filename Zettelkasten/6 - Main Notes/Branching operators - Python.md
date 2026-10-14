
27-08-2026 16:53

Status:

Tags: [[Python]]

---
# Branching operators - Python


```python
x = input('Name: ')

if x == 'Tim':
	print(f'Hi, {x}')
elif x == 'Joe':
	print("You look like a good Joe")
else:
	print(f'Bye, {x}')
```


### match

```python
def handle_command(cmd: str):
    match cmd:
        case "start":
            print("Starting...")
        case "stop":
            print("Stopping...")
        case "restart":
            print("Restarting...")
        case _:
            print(f"Unknown command: {cmd}")
```

----
#### [[Branching operators - Python - Flashcards|Link to flashcards]]



---
### References:

