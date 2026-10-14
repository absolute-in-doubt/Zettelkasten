
03-10-2026 10:44

Status:

Tags: [[Python]]

---
# docstring - Python

Здесь `"""Возвращает dict с a, rho, stable, p_wait, Lq, Wq. Wq считается только при rho < 1."""` - docstring, а не просто комментарий.

```python
  
def mmc(lam, mu, c):  
    """Возвращает dict с a, rho, stable, p_wait, Lq, Wq. Wq считается только при rho < 1."""  
    a = lam / mu  
    rho = a / c  
    res = {"c": c, "lam": lam, "a": a, "rho": rho, "stable": rho < 1,  
           "p_wait": None, "Lq": None, "Wq": None}  
    if rho >= 1:  
        return res                      # неустойчиво: формулы не применяем  
    s = sum(a**n / math.factorial(n) for n in range(c))  
    top = a**c / (math.factorial(c) * (1 - rho))  
    p_wait = top / (s + top)  
    wq = p_wait / (c * mu - lam)  
    res.update(p_wait=p_wait, Wq=wq, Lq=lam * wq)   # закон Литтла: Lq = λ·Wq  
    return res
```


- Это **первый оператор** внутри функции/класса/модуля в виде строки (`"""..."""` или `'''...'''`).docs.python+1
    
- Python сохраняет его в атрибуте `__doc__` и инструменты вроде `help()`, `pydoc`, IDE используют его как документацию.


### Пример использования

```python
print(mmc.__doc__)
# или
help(mmc)
```


----
#### [[socstring - Python - Flashcards|Link to flashcards]]



---
### References:

