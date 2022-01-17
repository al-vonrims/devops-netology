# devops-netology

## Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"

#### 1. Установите Bitwarden плагин для браузера. Зарегистрируйтесь и сохраните несколько паролей.
Выполнено:  
![Bitwarden](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-09-security/img/Bitwarden.JPG)

#### 2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
Выполнено:  
![auth](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-09-security/img/auth.JPG)
#### 3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
Установка apache2:  
```bash
apt-get install apache2
a2enmod ssl
systemctl restart apache2
```

Генерация сертификата:
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/apache-test.key \
-out /etc/ssl/certs/apache-test.crt \
-subj "/C=RU/ST=Moscow/L=Moscow/O=Company Name/OU=Org/CN=www.apache-test.com"
```

Настройка конфигурации apache для сайта:

`nano /etc/apache2/sites-available/apache-test.conf`:
```
<VirtualHost *:443>
ServerName www.apache-test.com
DocumentRoot /var/www/apache-test
SSLEngine on
SSLCertificateFile /etc/ssl/certs/apache-test.crt
SSLCertificateKeyFile /etc/ssl/private/apache-test.key
</VirtualHost>
```

`mkdir /var/www/apache-test`  
`nano /var/www/apache-test/index.html`  
```
<h1>Hello World!</h1>`
```

`nano /etc/hosts`
```
127.0.0.1    www.apache-test.com
```

Активируем сайт в apache:
```
root@vagrant:/etc/apache2# a2ensite apache-test.conf
Enabling site apache-test.
To activate the new configuration, you need to run:
  systemctl reload apache2
root@vagrant:/etc/apache2# apache2ctl configtest
Syntax OK
root@vagrant:/etc/apache2# systemctl reload apache2
```

Проверяем доступность сайта:
```
root@vagrant:/etc/apache2# curl -vk https://www.apache-test.com
*   Trying 127.0.0.1:443...
* TCP_NODELAY set
* Connected to www.apache-test.com (127.0.0.1) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use http/1.1
* Server certificate:
*  subject: C=RU; ST=Moscow; L=Moscow; O=Company Name; OU=Org; CN=www.apache-test.com
*  start date: Jan 17 09:01:22 2022 GMT
*  expire date: Jan 17 09:01:22 2023 GMT
*  issuer: C=RU; ST=Moscow; L=Moscow; O=Company Name; OU=Org; CN=www.apache-test.com
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
> GET / HTTP/1.1
> Host: www.apache-test.com
> User-Agent: curl/7.68.0
> Accept: */*
>
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 17 Jan 2022 09:23:48 GMT
< Server: Apache/2.4.41 (Ubuntu)
< Last-Modified: Mon, 17 Jan 2022 09:12:12 GMT
< ETag: "16-5d5c38c440e35"
< Accept-Ranges: bytes
< Content-Length: 22
< Content-Type: text/html
<
<h1>Hello World!</h1>
* Connection #0 to host www.apache-test.com left intact
```

#### 4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
Выполнено:
```bash
git clone --depth 1 https://github.com/drwetter/testssl.sh.git  
cd testssl.sh
./testssl.sh -U --sneaky https://pinning-test.badssl.com/
```

#### 5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
Выполнено:
```bash
apt install openssh-server  
systemctl start sshd.service  
systemctl enable sshd.service

ssh-keygen  
ssh-copy-id vagrant@192.168.10.137  
ssh vagrant@192.168.10.137
```

#### 6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
Выполнено:
```
mv ~/.ssh/id_rsa ~/.ssh/test-server 
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```

`nano ~/.ssh/config`  
```
Host test-server
    HostName 192.168.10.137
    IdentityFile ~/.ssh/test-server
    User vagrant
```  
`ssh test-server`


#### 7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.

`tcpdump -i eth0 -c 100 -w eth0_100.pcap`  
![Wireshark](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/03-sysadmin-09-security/img/Wireshark.JPG)