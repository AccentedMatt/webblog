---
layout: post
title: Linux - Signing your own certificates
subtitle: How to become your own Root CA to create your own TLS/SSL certificates using Linux
excerpt_image: /assets/images/excerpts/2026-01-12-root.jpg
tags: tutorial RootCA openssl X.509 TLS SSL PKI 509 certificates Linux PublicKeyInfrastructure
categories: tutorial linux
---

## Preface

I recommend if you are deploying a live service to investigate ACME. These certificates should be
automatically recognised by the end user for any ACME provider. An example provider would be 
[Let's Encrypt](https://letsencrypt.org/docs/client-options/)  who offer their services for free. 

## What is TLS?

Transport Layer Security (TLS) is a cryptographic protocol that supersedes SSL (Secure Sockets Layer).
A majority of the internet, from IoT devices to the web browser you are viewing this on, uses TLS/SSL. 98%
of webpages loaded using a Chrome browser in the United States utilise HTTPS, which is supported by TLS/SSL [^1] .

[^1]: [HTTPS usage in Chrome worldwide](https://transparencyreport.google.com/https/overview)

TLS/SSL is everywhere and it requires almost 0% effort from the user to use (they have to ensure they are using https 
if they are viewing webpages of course). This makes it a viable tool of everyday internet security. TLS works by
ensuring traffic between the client and the server are encrypted, reducing the likelihood of Man-in-the-Middle attacks.
This is a gross oversimplification but for our purposes, we only need to understand that it is here to help encrypted traffic 
from client to server and vice versa. 

Like many other things in life, TLS works by utilising a chain of trust. There are multiple layers of trust placed here, starting
from the Root Certificate Authority who give their stamp of approval all the way down to an End-Entity Certificate (that's what
we need).

## Ensuring OpenSSL is installed

The first step we must look at when becoming our own Root Certificate Authority, or Root CA for short, is ensuring we have the 
OpenSSL library installed:

~~~console
user@my-pc:~$ openssl version
OpenSSL 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024)
~~~

If you don't get an output similar to above, we must install OpenSSL. We can do that with this command: 

~~~console
user@my-pc:~$ sudo apt update -y && sudo apt install openssl
~~~

This command will update our packages list and then get the openssl library. Following this, run the version command again to ensure 
that OpenSSL is installed. Once installed, we can now start to consider generating our Root Certificate. 

## Generating Root's Private Key

Here, we can take two approaches to key generation. If all of our internal infrastructure is fairly modern we can use ED25519. If not, 
we can resort back to RSA. ED25519 is generally considered more secure than RSA but RSA is supported by many more devices than ED25519. 
If you are having doubts on which key algorithm to use, please resort to the RSA section.

### Using ED25519 for key generation

We can use elliptic-curve cryptography for generating the key our root authority will use. Much of the public infrastructure will not support
elliptic-curve cryptography. However, for our purposes in our own environment, using ECC is significantly faster and more secure than other key
generation methods as we do not rely on data that could be grabbed through a side-channel attack [^2] .

To generate our ED25519 key, we will run the following command:

~~~console
user@my-pc:~$ openssl genpkey -algorithm ED25519 -out root.key 
~~~

What we will see in the directory that we executed this in is a root.key file. This file will contain our private key that we can use in generation
of our Root Certificate. 

[^2]: [High-speed high-security signatures; Daniel J. Bernstein, Niels Duif ,Tanja Lange, Peter Schwabe and Bo-Yin Yang](https://ed25519.cr.yp.to/ed25519-20110926.pdf)

### Using RSA for key generation

For reliability purposes, RSA key generation can be used. RSA is more widely supported than more modern cryptographic suites. To generate 
a RSA key for use in our Root Certificate, we will execute the following command:

~~~console
user@my-pc:~$ openssl genrsa -out root.key 2048
~~~

What we will see in the directory that we executed this in is a root.key file. This file will contain our private key that we can use in generation
of our Root Certificate. The last value 2048 is our key size. We can customise this value to be a 2^n value (512, 1024, 2048, 4096). 
We cannot go lower than 512 [^3] .

[^3]: [OpenSSL Documentation: openssl-genrsa](https://docs.openssl.org/3.0/man1/openssl-genrsa/)

## Generating the Root Certificate

Now we're at the part of becoming our creating our own Root Authority Certificate. At this point, we will need to pass in significantly 
more flags than we did for the last step:

~~~console
user@my-pc:~$ openssl req -x509 -new -noenc -key root.key -sha256 -days 1827 -out root.crt
~~~
 
The req command is usually used for creating a certificate request (CSR) but in our case, it can be used to generate a self-signed certificate. 
The first flag we will pass in is the -x509 flag. This flag will inform the OpenSSL tool that we will want to generate a certificate instead of a 
certificate request. -new will generate a certificate request, we will look at that below in a second. -noenc ensures that if a private key is
created that it will not be encrypted. -key allows us to pass in the key we just generated. -sha256 hashes our key using the SHA-256 method (this
flag is actually ignored if we generated a ED25519 key as ED25519 utilises an internal hash function). -days is how many days this certificate will 
remain valid (1827 days is five years) and finally the -out flag, which tells OpenSSL to output our certificate to the ca.crt file.

Once this command has been run, you'll be prompted for some additional information. Remember the -new flag I said we'd talk about it in a second? 
Well we're looking at it in action now. This flag will prompt us for information to fill out our Root Certificate.

~~~console
Country Name (2 letter code) [AU]: GB
State or Province Name (full name) [Some-State]: .
Locality Name (eg, city) []: .
Organization Name (eg, company) [Internet Widgits Pty Ltd]: Matt's Lab
Organizational Unit Name (eg, section) []: .
Common Name (e.g server FQDN or Your Name) []: Matthew Neilly
Email Address []: matthew@neilly.tech
~~~

As you might have noticed, for some I placed a full-stop. That instructs within our CSR that we want that field to be left blank. Do not press enter
on its own as it will auto-fill the information contained between the square brackets. Luckily this is a lab environment but your certificate will
not match what you want. You can customise this to match your local environment requirements. Once completed, we will be able to see the ca.crt file 
appear in the same directory that our root.key is. This ca.crt file will feature a header and footer showing where the certificate starts and ends. 
Contained between the header and footer is the body of our certificate that is encoded in a base64 format.

Although not mandatory, we can check our certificate matches what we input. To do so, we can run the following command:

~~~console
openssl x509 -in root.crt -text -noout
~~~

This will output the contents of the certificate to the screen, in a slightly easier format to read than decrypting the base64 itself.

Congratulations! You will have now created your Root Certificate that we can use to sign more certificates. Now onto the server certificate.

## Generating the Server Certificate

Much like last time, we need to generate a new unique private key to use for our server. We wouldn't want to have the RootCA's private key be
identical to the server's private key. To do so, we will run the same command as last time, changing the name from root.key to server.key:

~~~console
user@my-pc:~$ openssl genrsa -out server.key 2048
~~~

You can also use ED25519 at this stage too. To do so, run the same command as last time ensuring you change the root.key to server.key. If you lose 
the root.key, you will not be able to sign certificates for your server.

At this stage, we can now look at creating the CSR for our server. I highly recommend creating a servercsr.conf file rather than relying on the 
prompt from OpenSSL. We can customise our certificate much more this way. To create the CSR, we will first run touch then we can enter the file using
the text editor of our choice. 

~~~
[ req ]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = GB
ST = State-Chaos
L = At Home
O = Matt's Lab
OU = Home Lab
CN = webserver.lan

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = webserver
DNS.2 = webserver.lan
IP.1 = 10.10.10.10
IP.2 = 10.10.10.20
~~~

Please adjust to your requirements, we can remove DNS entries or IP entries if we do not want them to be valid. I am using a fictional web server that 
is hosted locally and is mapped to webserver.lan. If we try to access the domain using an IP when we only have DNS mapped, it will tell us that the domain
is not secure and vice versa if we omit DNS entries but then access the server using DNS. This error would be because we've told the certificate that we
only want users to access using either IP/DNS. Within the [ dn ] section, this includes all of our organisational info. If needed, look up letters that 
can be accepted in this step.

Okay, we have a configuration file. We now need to actually create the CSR request using the private key for the server and the configuration file we just 
created:

~~~console
user@my-pc:~$ openssl req -new -key server.key -out server.csr -config servercsr.conf
~~~

This will generate a CSR that will be saved at server.csr. We now need to use this, along with a handful of other components to generate the TLS/SSL certificate
for our server. To do so, we will run:

~~~console
user@my-pc:~$ openssl x509 -req -in server.csr -CA root.crt -CAkey root.key -CAcreateserial \
              -out server.crt -days 365 -extensions req_ext -extfile servercsr.conf
~~~

Quite a large command, I know however we are just compiling everything from the root authority and the server csr together to generate one big certificate
that can be located in the server.crt file. Once again, this certificate will be encoded in base64 with a header and footer for where the certificate starts
and end. This server.crt file along with the server.key kept on the server of use is the file we can use to secure traffic.

## Trusting Root

If you have gone through and set up the private key and certificate on your server. You may have utilised it for securing HTTPS traffic. You may have also
noticed that it is warning us that our traffic is not secure. What's up with that? Remember back at the start, I mentioned that everything works based on trust?
This chain of trust is pre-installed on every machine trusting only a finite number of Root Authorities. To make our certificate worthwhile, we can take our 
root certificate and install it on our local machine. To do so, we must export the root.crt file onto all of our local machines. To install on Windows, we
can import our root certificate by running "certlm.msc" in our run bar. This will open us to the certificate manager. From here, we can "visit 'Action', 
then go to All Tasks → Import". From here, you can follow the instructions prompted by Windows to install the certificate. 

On Linux, it's slightly easier. We will add our root certificate to our certificate store. To do so we will copy it to the local certificate store and then
run the update certificates command to propagate our new root certificate throughout the system:

~~~console
sudo cp root.crt /usr/local/share/ca-certificates/ && sudo update-ca-certificates
~~~

At this stage, after adding your root certificate to your local machine, some browsers like to act up. To remediate this, restart your browser
so that it refreshes the certificate store that the browser caches.

## Conclusion

Congratulations, you have now become your own Root Authority and can sign server certificates to your heart's content. Please be mindful of how long
you have set your certificate validity period to. Once the certificate expires, it will complain a lot about an insecure connection. Additionally, you 
can lose your server.key and server.crt's (not that I advise it) and generate a new cert quite easily. If you lose your root.key, you may find yourself
having a hard time and will be required to conduct this entire process again.
