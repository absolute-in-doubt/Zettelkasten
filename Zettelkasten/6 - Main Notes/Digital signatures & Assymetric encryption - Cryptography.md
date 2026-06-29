
28-06-2026 19:12

Status:

Tags: [[Cryptography]]

---
# Assymetric encryption - Cryptography


Используем Public & Private keys. 

> Public и Private keys противоположны друг другу:
>
>![[Pasted image 20260628181902.png|462]]
>
> С помощью private key отправитель шифрует данные, а получатель при помощи public key расшифровывает их.


![[Pasted image 20260628182223.png]]

1. Signer берет хэш файла (например SHA256), шифрует её при помощи private key
	
2. Verifier получет Document + encoded signature. Дешифрует полученную signature, берёт хэш файла и сравнивает.

Если они совпадают, значит файл не был изменён кем-то посередине.

> В TLS public key передаётся периодически отдельным certificate-сообщением. Оно содержит:
> - public key;
> - encrypted hashes of several previous messages



> [!note]
> Шифровать весь документ - не вариант:
>- для больших файлов - долго и сложно;
>- для коротких - ненадёжно.
>
>Поэтому используем отдельную подпись, а не шифруем весь файл.
> Этого достаточно, чтобы проверить источник файла. 


----
#### [[Assymetric encryption - Cryptography - Flashcards|Link to flashcards]]



---
### References:

