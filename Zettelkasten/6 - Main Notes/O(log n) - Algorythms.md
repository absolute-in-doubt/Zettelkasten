
18-01-2026 14:21

Status:

Tags:

---
# O(log n) - Algorythms


![[Pasted image 20260118142252.png]]

```java
private static int find(List<String> list, String str){  
    int left = 0, right = list.size() - 1;  
    while(right-1 != left){  
        int middle = (right + left) / 2;  
        String foundVal = list.get(middle);  
        if(foundVal.compareTo(str) > 0){  
            right = middle;  
        } else if(foundVal.compareTo(str) == 0){  
            return middle;  
        } else {  
            left = middle;  
        }  
    }  
    return -1;  
}
```

### Как получаем O(log n) в худшем случае

Если размер коллекции 8:
1. делим на 2 -> size = 4
2. делим на 2 -> size = 2
3. делим на 2 -> size = 1  => return -1/result

Т.е. количество шагов = кол-во раз, которое мы поделили исходный размер ($n$) на 2:

## $\frac{n}{2^\text{amt of steps}} = 1 \quad \sim \quad \frac{8}{2^3} = 1$

таким образом:

### $n = 2^\text{amt of steps} \quad \to \quad log_2(n) = \text{amt of steps}$

Приводим логарифмы, выделяя константу (по свойствам логарифмов):

$log_e2 \cdot log_2n = log_en \cdot log_22 = log_en$

### $\frac{log_en}{log_e2} = log_2n \quad \text{, где } log_e2 - const$ 

Отбрасываем константу и получаем, что алгоритм занял $O(log_e n)$

Ну или $O(log_{10} n)$ не суть.

> [!tldr]
> Короче, если в процессе делим еолекцию на две или больше частей (кол-во не важно, т.к. это const обычно) - то $O(log_en)$

---
### References:

