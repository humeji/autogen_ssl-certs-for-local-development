# Generate Self-Signed SSL Certificates - version 1.1

***For Local Development on a macOS PC***

***Have you ever needed to test or develop on WebApps using HTTPS locally?***

To make it work you need to generate an SSL certificate of your own and tell your computer to trust them. If you have multiple web apps managing several certs is problematic.

You can just create your own CA (Certifying Authority) certificate. Then your self-signed certs, signed by your own CA cert, will all be accepted without you needing to load each one.

## How to Use This Script

- Clone or download a zip of this repo
- Navigate to the directory containing `generate-ssl.sh`
- Edit the `options.conf` file to add all of the domain names you want included in the certificate
- Edit the `certificate-authority-options.conf` file if you want to tweak the settings used to generate the certificate authority certificate (optional)
- Run `sudo ./generate-ssl.sh local.dev` where `local.dev` is the name of the cert that will be generated
- The script will ask you for your system password, which it needs to add the root certificate to Keychain
- The next prompts for passwords will be just for the generation of the certs. It doesn't matter what your password is so long as you enter the same one every time it asks
- That's it! Your certificate and private key will be in the `your-certs/` directory. Do with them what you like.

Here is the sample output after running the script:

 ```bash
sudo ./generate-ssl.sh local.dev
Password:
Generating RSA private key, 2048 bit long modulus
..........+++
..............................................+++
e is 65537 (0x10001)
Enter pass phrase for tmp/local.devCA.key:
Verifying - Enter pass phrase for tmp/local.devCA.key:
Enter pass phrase for tmp/local.devCA.key:
Generating RSA private key, 2048 bit long modulus
...............+++
.........+++
e is 65537 (0x10001)
Signature ok
subject=/C=US/ST=Fake State/L=Fake Locality/O=Fake Company/CN=local.dev
Getting CA Private Key
Enter pass phrase for tmp/local.devCA.key:
All done! Check the your-certs directory for your certs.
 ```

## This Script Will Generate the Following

When you run this script it will

- Create a fake certificate authority used to sign your own SSL certificate so your browser will trust it.
- Run all of the `openssl` commands to generate a root certificate (`.pem`) so your system will trust your self-signed certificate, a public certificate (`.crt`) that your server sends to end-user connections, and a private key for the certificate (`.key`) to encrypt and decrypt data sent between the server and client.
- Add the generated root certificate to macOS' Keychain so the operating system can trust the certificates you sign yourself.
- Reads all options from two configuration files so you can customize things to meet your needs.

## "It Didn't Work"

- `-bash: ./generate-ssl.sh: Permission denied` If the script isn't executable you can make it executable by typing: `chmod +x generate-ssl.sh`

- `OpenSSL isn't installed. You need that to generate SSL certificates.` You can install `openssl` using Brew: `brew install openssl`. If you don't have Brew see <https://brew.sh/>

- Open keychain and search for a certificate with the name you passed as an argument to the script. If you ran the script as `sudo ./generate-ssl.sh local.dev` then look for a certificate named `local.dev`

- Firefox doesn't use the macOS keychain (it maintains its own certificate store), In order for your CA-signed certificates to be recognized by Firefox you'll need to go into the Firefox settings and manually add the `pem` file to Firefox.

## Other Scripts

- If you need a more robust solution for Windows or Linux check out the <https://github.com/FiloSottile/mkcert> Project.

## 05.04.2019 Update

***THIS IS STIll EXPERMIENTAL USE AT YOUR OWN RISK***

I recently came across having to help a coworker accomplish this task and decided to create this project since following multiple manual script lines was very error prompt ([A follow-up to my Original publication in Medium](https://medium.com/@hugomejia74/how-to-create-your-own-ssl-certificate-authority-for-local-https-development-3b97573c7bb5)).

## Known Bugs

- 05/24/2019 - In some cases the shell will fail to add the root PEM cert to the KeyChain Access app in macOS Mojave version 10.14.5. In that case just add the root PEM cert to your KeyChain Manually.
