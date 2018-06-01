# vagrant-ssl
SSL for vagrant local development
## Becoming a (tiny) Certificate Authority
On macOS, open terminal, go to your vagrant folder (vagrant needs to have access to these files):
1. Generate private key:
```
openssl genrsa -des3 -out myCA.key 2048
```
2. Generate root certificate
```
openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem
```
3. Adding the Root Certificate to macOS Keychain

  * Open the macOS Keychain app
  * Draft your private key file (myCA.pem) to *System > Certificates*
  * Double click on your certificate in the list
  * Expand the Trust section
  * Change the When using this certificate: select box to “Always Trust”

## Creating CA-Signed Certificates for Your Dev Sites
1. Download `ssl.sh` file to your root folder.
2. Copy the file to /usr/local/bin/: `mv ssl.sh /usr/local/bin/ssl`
3. Make it executable `chmod u+x /usr/local/bin/ssl`
5. Run `ssl yourlocaldomain.local` to create certificates for your local domain.

## Install SSL on Vagrant’s Apache
1. Go to `vagrant ssh`
2. Edit defautl-ssl.conf `sudo nano /etc/apache2/sites-available/default-ssl.conf`
3. Find `SSLCertificateFile` replace */etc/ssl/certs/ssl-cert-snakeoil.pem* with your `.pem` file path (i.e /var/www/myCA.pem).
4. Find `SSLCertificateKeyFile` replace */etc/ssl/private/ssl-cert-snakeoil.key* with your `.key` file path (i.e /var/www/myCA.key).

## Install SSL on your virtual host
1. Edit your virtual host `sudo nano /etc/site-enables/yourlocaldomain.local.conf`
2. Change default listen port `<VirtualHost *:80>` to `<VirtualHost *:443>`
3. Add this code bellow `DocumentRoot...`
```
  SSLEngine on
  SSLCertificateFile /var/www/yourlocaldomain.local.crt
  SSLCertificateKeyFile /var/www/yourlocaldomain.local.key
  ```
 4. Redirect http to https, add this code at the top (before `<VirtualHost *:443>` block)
 ```
 <VirtualHost *:80>
        ServerName yourlocaldomain.dev
        DocumentRoot /var/www/yourlocaldomain.local/public
        Redirect permanent / https://yourlocaldomain.local/
</VirtualHost>
 ```
 5. Restart Apache `sudo service apache2 restart`
 
 All done.
 
 ## REF
 * https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/#creating-ca-signed-certificates
 * https://gist.github.com/polevaultweb/c83ac276f51a523a80d8e7f9a61afad0
 
 Thanks @polevaultweb for ssl script.
