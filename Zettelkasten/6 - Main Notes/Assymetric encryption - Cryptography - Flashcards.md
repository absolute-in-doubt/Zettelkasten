
Theory for the cards: [[Digital signatures & Assymetric encryption - Cryptography]]

FILE TAGS: cryptography

Q: Как работает ассиметричное шифрование?
A:  Используем public и private keys.
	
> Public и Private keys противоположны друг другу:
>
>![[Pasted image 20260628181902.png|462]]
>
> С помощью private key отправитель шифрует данные, а получатель при помощи public key расшифровывает их.
	
	
![[Pasted image 20260628182223.png]]
	
1. Signer берет хэш файла (например SHA256), шифрует её при помощи private key
	
2. Verifier получет Document + encoded signature. Дешифрует полученную signature, берёт хэш файла и сравнивает.
	
Если они совпадают, значит файл не был изменён кем-то посередине.
<!--ID: 1782666121651-->
