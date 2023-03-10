# YetiForceCRM 6.4.0 - Debian 10 Buster
Suporte para Instalação do YetiForce CRM 6.4.0 no Debian 10 Buster

• Instalação das dependências
```
sudo apt-get update && sudo apt-get upgrade -y && sudo apt -y install lsb-release apt-transport-https ca-certificates && sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list && sudo apt-get update && sudo apt install apache2 mariadb-server php7.4 libapache2-mod-php7.4 php7.4-common php7.4-sqlite3 php7.4-json php7.4-curl php7.4-intl php7.4-mbstring php7.4-xmlrpc php7.4-mysql php7.4-gd php7.4-xml php7.4-cli php7.4-zip php7.4-soap php7.4-imap php7.4-bcmath wget rename unzip -y && sudo timedatectl set-timezone America/Manaus && sudo apt-get -y autoclean
```
• Alterações no arquivo /etc/php/7.4/apache2/php.ini
```
max_execution_time = 600    
max_input_time = 600
memory_limit = 512M
upload_max_filesize = 100M
post_max_size = 100M
date.timezone = America/Manaus          //substituir pelo timezone de sua região
session.use_strict_mode = 1
session.cookie_samesite = Strict
error_reporting = E_ALL & ~E_NOTICE 	//procurar o que tá habilitado e alterar
default_socket_timeout = 600
short_open_tag = On		                //procurar o que tá habilitado e alterar
max_input_vars = 10000
output_buffering=On 		            //procurar o que tá habilitado e alterar
session.gc_probability = 1
auto_detect_line_endings=On
opcache.enable_cli=1
opcache.max_accelerated_files=40000
opcache.memory_consumption=1024
opcache.interned_strings_buffer = 100
opcache.revalidate_freq=0
opcache.save_comments=0
opcache.file_update_protection = 0
realpath_cache_ttl = 600
mysqlnd.collect_statistics = Off
error_log = php_errors.log
disable_functions =exec,passthru,shell_exec,system,popen
```
• Criação e Configuração do BD
```
sudo mysql -u root -p
CREATE DATABASE yetiforce;
CREATE USER 'yetiforceuser'@'localhost' IDENTIFIED BY 'CRIAR UMA SENHA SEGURA';
GRANT ALL ON yetiforce.* TO 'yetiforceuser'@'localhost' WITH GRANT OPTION; 
FLUSH PRIVILEGES; 
EXIT 
```
• Alterações no arquivo /etc/mysql/my.cnf
```
table_definition_cache=2400
innodb_lock_wait_timeout=600
innodb_large_prefix=On
```
• Reiniciar o BD
```
sudo systemctl restart mariadb.service
```
• Download do YetiForce CRM 6.4.0
```
wget -O YetiForceCRM.zip https://excellmedia.dl.sourceforge.net/project/yetiforce/YetiForce%20CRM%206.x.x/6.4.0/YetiForceCRM-6.4.0-complete.zip && wget https://raw.githubusercontent.com/YetiForceCompany/YetiForceCRMLanguages/master/6.4.0/pt-BR.zip
sudo unzip YetiForceCRM.zip -d /var/www/yetiforce && sudo mkdir /var/www/yetiforce/config/Modules
```
• Criação do arquivo /etc/cron.d/yf_crm
```
*/2 * * * * root /var/www/yetiforce/cron/cron.sh > /var/yetiforce/cache/logs/cron.log 2>&1
```
• Reiniciar o cron
```
sudo systemctl restart cron && sudo systemctl status cron
```
• Sobrescrver os arquivos de instalação para PT-BR
```
sudo unzip pt-BR.zip -d /tmp/yeti && sudo rm -v /tmp/yeti/manifest.xml
cd /tmp/yeti/ && sudo rename 's/languages\\pt-BR\\//g' *.json
sudo mkdir Settings && sudo mv -v Settings* Settings && cd Settings/ && sudo rename -v 's/Settings\\//g' *.json
sudo mkdir Other && sudo mv -v Other* Other && cd Other/ && sudo rename -v 's/Other\\//g' *.json
cd /home/lancnet && sudo cp -rfv /tmp/yeti/* /var/www/yetiforce/languages/en-US/
```
• Alterar propriedade e permissões
```
sudo chown -Rv www-data:www-data /var/www/yetiforce && sudo chmod -Rv 755 /var/www/yetiforce
```
• Criação do arquivo /etc/apache2/sites-available/yetiforce.conf
```
<VirtualHost *:80>
    ServerAdmin seu@email.com.br
    DocumentRoot /var/www/yetiforce
    ServerName localhost
    ServerAlias localhost
    <Directory /var/www/yetiforce/>
          Options FollowSymlinks
          AllowOverride All
          Require all granted
     </Directory>
     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
• Ajustes no apache
```
cd /etc/apache2/sites-available/ && sudo a2dissite 000-default.conf && sudo a2ensite yetiforce.conf && sudo a2enmod rewrite && sudo systemctl restart apache2
```

• WEB Instalation Wizard

    Base de Dados: yetiforceuser, SENHA CRIADA ANTERIORMENTE

    Moeda: Brazil, Reais(R$)

    Admin: Administrador, CRIAR UMA SENHA SEGURA, seu@email.com.br, dd/mm/yyyy, America/Manaus

• Configurações Posteriores

    Encryption method: aes-256-cdc 
    Encryption Key: **CRIAR UMA SENHA SEGURA** 
    Inicialization vector: **CRIAR UMA SENHA SEGURA**
