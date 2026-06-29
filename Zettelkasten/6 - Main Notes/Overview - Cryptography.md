
28-06-2026 18:03

Status:

Tags: [[Cryptography]]

---
# Overview - Cryptography



### Цели защиты данных (самая база)

Изучи, какие свойства вообще нужно обеспечить:

- **Confidentiality** (конфиденциальность)
- **Integrity** (целостность)
- **Authenticity** (подлинность)
- **Non-repudiation** (неотказуемость)




- **Encoding** (Base64, URL Encoding, HTML Encoding)
- **Hashing** (SHA-256, bcrypt, Argon2)
- **Encryption**
    - Symmetric (AES)
    - Asymmetric (RSA, ECC)
- **Digital Signatures**
- **Salting** (для хранения паролей)

- **Signing vs Encryption** — очень любят спрашивать на интервью.
- **Integrity, Confidentiality, Authenticity, Non-repudiation** — какие свойства обеспечивают разные механизмы.

### Encoding

#### Symmetric Encryption

- AES;
- один ключ для шифрования/расшифровки;
- быстро;
- проблема передачи ключа.

#### Asymmetric Encryption

- RSA;
- Public/Private Key;
- обмен ключами;
- медленнее симметричного.



|Mechanism|Purpose|
|---|---|
|Encoding|Representation/compatibility|
|Hashing|Integrity, password storage|
|Encryption|Confidentiality|
|Digital Signature|Authenticity + Integrity + Non-repudiation|

----
#### [[Overview - Cryptography - Flashcards|Link to flashcards]]



---
### References:

