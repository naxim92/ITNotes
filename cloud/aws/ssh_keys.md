#aws #ssh

- Если у тебя есть ключ key.pem, как узнать, подходит ли он к чему то из aws?
	В aws можно посмотреть ключи (Key pairs) и сравнить fingerprint со своим ключем
	`openssl pkcs8 -in /key.pem -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c`