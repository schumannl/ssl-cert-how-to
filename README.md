# Setting up SSL/TLS cert for a domain

## Generate CSR with OpenSSL

### Generate private key
```
	openssl genrsa -out yourdomain.key 2048
```

### Generate CSR request
**Note:** *CSR stands for Certificate Sign Request*
```
	openssl req -new -key yourdomain.key -out yourdomain.csr
```

### Single command
```
	openssl req -new -newkey rsa:2048 -keyout yourdomain.key -out yourdomain.csr
```

### Check .csr
```
	openssl req -text -in yourdomain.key -noout
```

## Sign up for cert 
Use any paid certificate authority (e.g.: Comodo, GoDaddy, Verisign, Thawte, etc...) to request certification with the .csr file.

## Setup Apache vhost

### Add cert to config
```
	<VirtualHost *:443>
		...
		ServerName yourdomain.com
		...
		SSLEngine on
		SSLCertificateFile /path/to/yourdomain.crt
		SSLCertificateKeyFile /path/to/yourdomain.key
		SSLCertificateChainFile /path/to/yourdomain.crt
		...
	</VirtualHost>
```
**Note:** *Don't forget to check the config (`apachectl configtest`) and restart (`apachectl restart` / `apachectl graceful`).*

### Redirect http to https
```
	<VirtualHost *:80>
		...
		Redirect permanent / https://yourdomain.com
		...
	</VirtualHost>
```

### Setup HSTS
**Note:** *HSTS stands for HTTP Strict Transport Security which means responding with a header which makes the browser always sending HTTPS requests*
```
	<VirtualHost *:443>
		...
		Header always set Strict-Transport-Security "max-age=300; includeSubdomains;"
		...
	</VirtualHost>
```

## HSTS Preload
**Note:** *HSTS Preload helps to avoid the very first insecure call to the server by browsers using a list to check whether a domain is https only. see: https://hstspreload.org/*
