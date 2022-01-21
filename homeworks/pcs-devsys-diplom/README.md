# devops-netology

## Курсовая работа по итогам модуля "DevOps и системное администрирование"
#### 1. Создайте виртуальную машину Linux.
Виртуальная машина создана:  
![VirtualBox](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/pcs-devsys-diplom/img/VirtualBox.JPG)

#### 2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.
По умолчанию `ufw` уже установлен в Ubuntu 20.04.3:
```bash
root@coursework:~# cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.3 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.3 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
root@coursework:~#
root@coursework:~# ufw --version
ufw 0.36
Copyright 2008-2015 Canonical Ltd.
```

Настройка правил `ufw`:
```
root@coursework:~# ufw allow 22
Rules updated
Rules updated (v6)
root@coursework:~# ufw allow 443
Rules updated
Rules updated (v6)
root@coursework:~# ufw allow from 127.0.0.0/8
Rules updated
root@coursework:~# ufw allow out on lo
Rules updated
Rules updated (v6)
root@coursework:~# ufw allow in on lo
Rules updated
Rules updated (v6)
root@coursework:~# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```


#### 3. Установите hashicorp vault ([инструкция по ссылке](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault)).
Установка `vault`:
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vault
```
```
root@coursework:~# vault --version
Vault v1.9.2 (f4c6d873e2767c0d6853b5d9ffc77b0d297bfbdf)
```

Изменим конфигурационный файл:  
`vim /etc/vault.d/vault.hcl`:
```
ui = true

storage "file" {
  path = "/opt/vault/data"
}

listener "tcp" {
  address = "127.0.0.1:8200"
  tls_disable = 1
}
```

Добавим `vault` в автозагрузку и выполним первичную инициализацию:
```
systemctl enable vault --now
export VAULT_ADDR='http://127.0.0.1:8200'
vault operator init
```
Сохраним полученные ключи для распечатки хранилища в файл:
```
vim /etc/vault.d/init.keys
chown vault:vault /etc/vault.d/init.keys
```

Распечатаем хранилище тремя ключами, полученными при инициализации:  
`vault operator unseal`

Добавим скрипт для автоматической распечатки после перезапуска vault:

```
touch /etc/vault.d/vault_unseal.sh
chmod +x /etc/vault.d/vault_unseal.sh
chown vault:vault /etc/vault.d/vault_unseal.sh
```

`vim /etc/vault.d/vault_unseal.sh`

```bash
#!/usr/bin/env bash

sleep 30
export VAULT_ADDR='http://127.0.0.1:8200'
log=/var/log/unseal.log

date >> $log
while read i
do
        vault operator unseal $i > /dev/null 2>> $log
        vault_status=$(vault status -format "json" 2>> $log | jq --raw-output '.sealed' )
done < /etc/vault.d/init.keys

if [[ $vault_status == 'false' ]]
then
        echo Vault unsealed  >> $log
else
        echo Vault useal failed >> $log

fi
````

Изменим unit-файл и добавим скрипт в автозапуск после успешного старта vault:  
`systemctl edit vault`

```
[Service]
ExecStartPost=/etc/vault.d/vault_unseal.sh
```

Проверим, что хранилище распечатывается при перезапуске vault:
```
root@coursework:~# systemctl restart vault
root@coursework:~# vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    5
Threshold       3
Version         1.9.2
Storage Type    file
Cluster Name    vault-cluster-c71d73f3
Cluster ID      108ec401-974a-b0f6-b15d-c767f28f824e
HA Enabled      false
root@coursework:~#
root@coursework:~# cat /var/log/unseal.log
Wed 21 Jan 16:02:50 MSK 2022
Vault unsealed
```

#### 4. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
Установим парсер JSON:  
`apt install jq`


Создание корневого ЦС:

```
root@coursework:/etc/vault.d# export VAULT_TOKEN="******************"
root@coursework:/etc/vault.d# vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
root@coursework:/etc/vault.d# vault secrets tune -max-lease-ttl=87600h pki
Success! Tuned the secrets engine at: pki/
root@coursework:/etc/vault.d# vault write -field=certificate pki/root/generate/internal \
>      common_name="example.com" \
>      ttl=87600h > CA_cert.crt
```

Создание промежуточного ЦС:

```
root@coursework:/etc/vault.d# vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
root@coursework:/etc/vault.d# vault secrets tune -max-lease-ttl=43800h pki_int
Success! Tuned the secrets engine at: pki_int/
root@coursework:/etc/vault.d# vault write -format=json pki_int/intermediate/generate/internal \
>      common_name="example.com Intermediate Authority" \
>      | jq -r '.data.csr' > pki_intermediate.csr
root@coursework:/etc/vault.d# vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
>      format=pem_bundle ttl="43800h" \
>      | jq -r '.data.certificate' > intermediate.cert.pem
root@coursework:/etc/vault.d# vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed
```

Создание роли:

```
root@coursework:/etc/vault.d# vault write pki_int/roles/example-dot-com \
>      allowed_domains="example.com" \
>      allow_subdomains=true \
>      max_ttl="744h"
Success! Data written to: pki_int/roles/example-dot-com
```

Выпуск сертификата:

```
vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="744h" > test.example.com.cert.json
```

#### 5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
Копируем сертификат `CA_cert.crt` и устанавливаем в доверенные корневые ЦС:
![TrustedCA](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/pcs-devsys-diplom/img/TrustedCA.JPG)

#### 6. Установите nginx.
`nginx` установлен командой `apt install nginx`:

```
root@coursework:~# nginx -v
nginx version: nginx/1.18.0 (Ubuntu)
```


#### 7. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
   - можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
   - можно использовать и другой html файл, сделанный вами;

Создадим папку:  
`mkdir -p /etc/nginx/ssl/example.com`

И поместим в неё сертификат и ключ:
```
jq -r .data.certificate < test.example.com.cert.json > /etc/nginx/ssl/example.com/test.example.com.crt
jq -r .data.issuing_ca < test.example.com.cert.json >> /etc/nginx/ssl/example.com/test.example.com.crt
jq -r .data.private_key < test.example.com.cert.json > /etc/nginx/ssl/example.com/test.example.com.key
```
Создадим конфигурационный файл для сайта:  
`vim /etc/nginx/conf.d/test.example.com.conf`:

```
server {
        listen 443 ssl;

        server_name test.example.com;

        ssl_certificate /etc/nginx/ssl/example.com/test.example.com.crt;
        ssl_certificate_key /etc/nginx/ssl/example.com/test.example.com.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;

        root /var/www/html;
        index index.nginx-debian.html;
        location / {
                try_files $uri $uri/ =404;
    }
}

```

Проверяем и перезапускаем nginx:
```
root@coursework:/etc/nginx/conf.d# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@coursework:/etc/nginx/conf.d# systemctl restart nginx

```

#### 8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
Добавляем A-запись для test.example.com на роутере:  
![DNS](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/pcs-devsys-diplom/img/DNS.jpg)

Проверяем сайт в браузере:  
![Browser](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/pcs-devsys-diplom/img/Browser.jpg)  
![cert](https://github.com/al-vonrims/devops-netology/blob/main/homeworks/pcs-devsys-diplom/img/cert.jpg)


#### 9. Создайте скрипт, который будет генерировать новый сертификат в vault:
  - генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
  - перезапускаем nginx для применения нового сертификата.

Создадим файл-скрипта и установим разрешение на исполнение:  
```bash
touch gen_nginx_cert.sh
chmod +x gen_nginx_cert.sh
```

Добавим скрипт:  
`vim gen_nginx_cert.sh`:
```bash
#!/usr/bin/env bash

export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN="************************"
log=~/gen_cert_logs/gen_nginx_cert.log
json=~/test.example.com.cert.json

date >> $log
vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="744h" > $json 2>> $log
if (($?==0))
then
        jq -r .data.certificate < $json > /etc/nginx/ssl/example.com/test.example.com.crt
        jq -r .data.issuing_ca < $json >> /etc/nginx/ssl/example.com/test.example.com.crt
        jq -r .data.private_key < $json > /etc/nginx/ssl/example.com/test.example.com.key
        systemctl restart nginx 2>> $log
        if (($?==0))
        then
                echo nginx restarted successfully >> $log
        else
                echo nginx restart failed >> $log
                exit 1
        fi
        echo Certificate issued successfully >> $log
        exit 0
else
        echo Certificate Issue Error >> $log
        exit 1
fi
```

#### 10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.

Добавление скрипта в crontab:  
`root@coursework:~# crontab -e`
```
# m h  dom mon dow   command
00 20 21 * * /root/gen_nginx_cert.sh
```

Проверим выполнение скрипта по логам:  
`root@coursework:~# cat /var/log/syslog`
```
Jan 21 20:00:01 coursework cron[702]: (root) RELOAD (crontabs/root)
Jan 21 20:00:01 coursework CRON[1234]: (root) CMD (/root/gen_nginx_cert.sh)
Jan 21 20:00:01 coursework systemd[1]: Stopping A high performance web server and a reverse proxy server...
Jan 21 20:00:01 coursework systemd[1]: nginx.service: Succeeded.
Jan 21 20:00:01 coursework systemd[1]: Stopped A high performance web server and a reverse proxy server.
Jan 21 20:00:01 coursework systemd[1]: Starting A high performance web server and a reverse proxy server...
Jan 21 20:00:01 coursework systemd[1]: Started A high performance web server and a reverse proxy server.
```

`root@coursework:~# cat gen_cert_logs/gen_nginx_cert.log`
```
Fri 21 Jan 20:00:01 MSK 2022
nginx restarted successfully
Certificate issued successfully
```
