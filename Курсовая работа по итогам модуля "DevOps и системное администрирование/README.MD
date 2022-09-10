1.Создайте виртуальную машину Linux.

2.Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.

agrant@vagrant:~$ sudo apt-get install ufw
Reading package lists... Done
Building dependency tree       
Reading state information... Done
ufw is already the newest version (0.36-6ubuntu1).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
vagrant@vagrant:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
vagrant@vagrant:~$ sudo ufw allow in on lo from 0.0.0.0/0
Rule added
vagrant@vagrant:~$ sudo ufw allow 22
Rule added
Rule added (v6)
vagrant@vagrant:~$ sudo ufw allow 443
Rule added
Rule added (v6)
vagrant@vagrant:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
Anywhere on lo             ALLOW IN    Anywhere                  
22                         ALLOW IN    Anywhere                  
443                        ALLOW IN    Anywhere                  
22 (v6)                    ALLOW IN    Anywhere (v6)             
443 (v6)                   ALLOW IN    Anywhere (v6)  


3.Установите hashicorp vault (инструкция по ссылке).

$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -                                 
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install vault


4 Cоздайте центр сертификации по инструкции (ссылка) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
$ vault server -devvi -dev-root-token-id root                     

$ export VAULT_ADDR=http://127.0.0.1:8200                         
$ export VAULT_TOKEN=root
$ vault secrets enable pki                                        
Success! Enabled the pki secrets engine at: pki/
$ vault secrets tune -max-lease-ttl=87600h pki                    
Success! Tuned the secrets engine at: pki/
$ vault write -field=certificate pki/root/generate/internal \     
> common_name="example.coursework.com" \
> ttl=87600h > root_cert.crt

$ vault write pki/config/urls \                                   
> issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
> crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
$ vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
$ vault secrets tune -max-lease-ttl=87600h pki
Success! Tuned the secrets engine at: pki/
$ vault write -field=certificate pki/root/generate/internal \
>      common_name="example.com" \
>      ttl=87600h > CA_cert.crt
$ vault write pki/config/urls \
>      issuing_certificates="$VAULT_ADDR/v1/pki/ca" \
>      crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
Success! Data written to: pki/config/urls
$ vault secrets enable -path=pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
$ vault secrets tune -max-lease-ttl=43800h pki_int
Success! Tuned the secrets engine at: pki_int/
$ vault write -format=json pki_int/intermediate/generate/internal \
>      common_name="example.com Intermediate Authority" \
>      | jq -r '.data.csr' > pki_intermediate.csr
$ vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr \
>      format=pem_bundle ttl="43800h" \
>      | jq -r '.data.certificate' > intermediate.cert.pem
$ vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
Success! Data written to: pki_int/intermediate/set-signed
$ vault write pki_int/roles/example-dot-com \
>      allowed_domains="example.com" \
>      allow_subdomains=true \
max_ttl>      max_ttl="744h"
Success! Data written to: pki_int/roles/example-dot-com

6 Установите nginx.

$ sudo apt-get install nginx -y
$ sudo vi /etc/nginx/sites-available/default
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        # SSL configuration
        listen 443 ssl default_server;
        listen [::]:443 ssl default_server;
        #
        root /var/www/html;
        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;
        server_name test.example.com;
        ssl_certificate     /home/vagrant/ssl/test.example.com.crt;
        ssl_certificate_key /home/vagrant/ssl/test.example.com.key;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;
        
        

7. По инструкции (ссылка) настройте nginx на https, используя ранее подготовленный сертификат:

sudo vi ~/script.sh

 #!/usr/bin/env bash
 vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="744h" > cert.json
 jq -r '.data.private_key' ./cert.json > ./ssl/test.example.com.key
 jq -r '.data.certificate' ./cert.json > ./ssl/test.example.com.crt

sudo service nginx restart


vagrant@vagrant:~$ crontab -l

21 17 * * * /home/vagrant/script.sh >/dev/null 2>&1
$ grep CRON /var/log/syslog
...
Sep 09 10:07:01 vagrant CRON[641]: (vagrant) CMD (/home/vagrant/new_cert.sh >/dev/null 2>&1)
