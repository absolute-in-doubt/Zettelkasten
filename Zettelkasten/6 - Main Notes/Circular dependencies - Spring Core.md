
04-06-2026 11:09

Status:

Tags: [[Spring Core]] [[Spring]]

---
# Circular dependencies - Spring Core

Spring resolves circular dependencies mainly using **early object references + 3-level singleton cache + (sometimes) proxies**.

But there’s an important limitation:

> Spring can only resolve circular dependencies for **singleton beans with setter/field injection**, not constructor injection.

---

# 🔥 1. The classic circular dependency

```java
@Service
class A {
    @Autowired B b;
}

@Service
class B {
    @Autowired A a;
}
```

Dependency graph:

```text
A → B
B → A
```

This looks impossible — but Spring resolves it.

---

# 🧠 2. Key idea: “partially constructed beans”

Spring breaks bean creation into stages:

```text
1. Instantiate (new A)
2. Expose early reference
3. Inject dependencies
4. Initialize
5. Put into singleton cache
```

---

# 🧩 3. The 3-level singleton cache

Spring uses:

```java
singletonObjects         // fully initialized beans
earlySingletonObjects    // partially created beans
singletonFactories       // factories for early references
```

---

# ⚙️ 4. Step-by-step resolution

Let’s trace `A → B → A`.

---

## Step 1: Create A

```text
createBean(A)
→ instantiate A (constructor)
→ A is not yet initialized
```

Spring puts a factory:

```text
singletonFactories.put("A", () -> getEarlyReference(A))
```

---

## Step 2: Inject B into A

Spring sees:

```text
A needs B → create B
```

---

## Step 3: Create B

```text
instantiate B
```

Now B needs A.

---

## Step 4: A is not ready → check caches

Spring tries:

```text
singletonObjects.get("A") → null
earlySingletonObjects.get("A") → null
singletonFactories.get("A") → FOUND
```

So Spring calls factory:

```text
A early reference created
```

---

## Step 5: Inject A into B

Now B gets **early A reference**.

So:

```text
B.a = earlyA
```

---

## Step 6: Finish B

```text
B fully initialized → put into singletonObjects
```

---

## Step 7: Finish A

Now B is ready, so:

```text
A.b = B
A fully initialized → singletonObjects
```

---

# 🧠 Final result

```text
A → B → A (cycle resolved via early reference)
```

---

# ⚠️ 5. Why constructor injection fails

This does NOT work:

```java
@Service
class A {
    A(B b) {}
}
```

Why?

Because:

```text
constructor must be completed BEFORE bean exists
```

So Spring cannot:

- create “half A”
    
- expose early reference
    

Result:

```text
BeanCurrentlyInCreationException
```

---

# 6. Role of proxies (very important)

If A or B requires AOP (e.g. `@Transactional`), Spring must return a **proxy even in early reference stage**.

So instead of raw object:

```text
A (real object)
```

Spring may return:

```text
A$$Proxy
```

via:

```java
SmartInstantiationAwareBeanPostProcessor#getEarlyBeanReference()
```

---

# 🔥 7. The hidden mechanism

Key method:

```java
DefaultSingletonBeanRegistry#getSingleton()
```

It orchestrates:

- singletonObjects
    
- earlySingletonObjects
    
- singletonFactories
    

---

# 🧩 8. When circular dependency works

✔ Works:

- field injection
    
- setter injection
    
- singleton scope
    

---

# ❌ Does NOT work:

- constructor injection
    
- prototype beans (generally)
    
- circular dependencies with `@Async` / proxy-heavy setups (sometimes breaks early reference logic)
    

---

# ⚠️ 9. Why Spring allows it at all

Circular dependencies are usually considered bad design, but Spring supports them because:

- legacy codebases rely on it
    
- domain graphs sometimes naturally cycle
    
- setter injection was historically common
    

---

# 🧠 Mental model

Think of Spring like:

```text
"I'll give you a placeholder of A,
so B can be created,
and later I'll finish A"
```

---

# 📌 Summary

Spring resolves circular dependencies using:

- 3-level cache system
    
- early object exposure (ObjectFactory)
    
- partial bean instances
    
- proxies (if needed)
    

But only for:

> singleton + non-constructor injection cases

---

If you want, I can also show:

- exact source-level flow in `AbstractAutowireCapableBeanFactory`
    
- how `@Lazy` breaks the cycle cleanly
    
- or why constructor injection is considered best practice despite this feature


----
#### [[Circular dependencies - Spring Core - Flashcards|Link to flashcards]]



---
### References:

