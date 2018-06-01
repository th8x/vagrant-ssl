# vagrant-ssl
SSL for vagrant local development
## Becoming a (tiny) Certificate Authority
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
  * Go to File > Import Items…
  * Select your private key file (i.e. myCA.pem)
  * Search for whatever you answered as the Common Name name above
  * Double click on your root certificate in the list
  * Expand the Trust section
  * Change the When using this certificate: select box to “Always Trust”

## Creating CA-Signed Certificates for Your Dev Sites
1. Download `ssl.sh` file to your root
2. Copy the file to /usr/local/bin/: `mv ssl.sh /usr/local/bin/ssl`
3. Make it executable `chmod u+x /usr/local/bin/ssl`
4. Create a directory *certs* in your root directory
5. `cd certs` and run `ssl yourlocaldomain.dev`

## Add SSL to vagrant 
1. `vagrant ssh`
2. `sudo nano /etc/apache2/sites-available/default-ssl.conf`
3. Find `SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem` replace with your `.pem` file path.
4. Find `SSLCertificateKeyFile  /etc/ssl/private/ssl-cert-snakeoil.key` replace with your `.key` file path.
5. Edit your virtual host `sudo nano /etc/site-enables/yourlocaldomain.dev.conf`
6. Add bellow `DocumentRoot...`
```
  SSLEngine on
  SSLCertificateFile /var/www/qmac.local/certs/qmac.local.crt
  SSLCertificateKeyFile /var/www/qmac.local/certs/qmac.local.key
  ```
 7. Restart Apache `sudo service apache2 restart`
 
 All done.
 
 ## REF
 * https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/#creating-ca-signed-certificates
 * https://gist.github.com/polevaultweb/c83ac276f51a523a80d8e7f9a61afad0
 
 Thanks @polevaultweb for ssl script.
