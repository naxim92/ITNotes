#cert #openssl

-  Создать web сертификат без участия пользователя
  `openssl req -days 365 -nodes -new -x509 -subj '/C=US/L=City/O=Test/CN='${nginx_host} -keyout privkey.pem -out fullchain.pem`