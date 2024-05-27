

# Task 1: Becoming a Certificate Authority (CA)

```bash
cp /usr/lib/ssl/openssl.cnf ./openssl.cnf
```

```bash
mkdir -p demoCA/newcerts
```

```bash
mkdir -p demoCA/certs
```

```bash
mkdir -p demoCA/crl
```

```bash
touch demoCA/index.txt
```

```bash
echo 1000 > demoCA/serial
```

```bash
openssl req -new -x509 -keyout ca.key -out ca.crt -config openssl.cnf
```

# Task 2: Creating a Certificate for SEEDPKILab2020.com

```bash
openssl genrsa -aes128 -out server.key 1024
```

```bash
openssl rsa -in server.key -text
```

```bash
openssl req -new -key server.key -out server.csr -config openssl.cnf
```

```bash
openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key -config openssl.cnf
```

# Task 3: Deploying Certificate in an HTTPS Web Server

## Step 1: Configuring DNS (back to default)

```bash
sudo nano /etc/hosts
```

Add the following line:

```
127.0.0.1 SEEDPKILab2020.com
```

Save the file and exit.

## Step 2: Configuring the Web Server

```bash
cp server.key server.pem
```

```bash
cat server.crt >> server.pem
```

```bash
openssl s_server -cert server.pem -www
```

Now search in the browser https://SEEDPKILab2020.com:4433/

## Step 3: Getting the Browser to Accept Your CA Certificate

Edit -> Preferences -> Privacy & Security -> View Certificates

## Step 4: Testing Your HTTPS Website

```bash
sudo nano server.pem
```

```bash
openssl s_server -cert server.pem -www
```

Access the Server Using localhost: Navigate to https://localhost:4433/

# Task 4: Deploying Certificate in an Apache-Based HTTPS Website

```bash
sudo mkdir /etc/apache2/ssl
```

```bash
sudo cp server.crt /etc/apache2/ssl/seedpki_cert.pem
```

```bash
sudo cp server.key /etc/apache2/ssl/seedpki_key.pem
```

```bash
sudo mkdir /var/www/seedpkilab2020
```

```bash
echo "<html><body><h1>Welcome to SEEDPKILab2020.com</h1></body></html>" | sudo tee /var/www/seedpkilab2020/index.html
```

```bash
sudo apachectl configtest
```

```bash
sudo a2enmod ssl
```

```bash
sudo a2ensite default-ssl
```

```bash
sudo service apache2 restart
```

Open your web browser and navigate to https://SEEDPKILab2020.com.

```bash
sudo nano /etc/apache2/ssl/seedpki_key.pem
```

```bash
sudo service apache2 restart
```

# Task 5: Launching a Man-In-The-Middle Attack

## Step 1: Setting up the Malicious Website

Follow the instructions from Task 4 to set up a VirtualHost entry in Apache's SSL configuration file (default-ssl.conf). Use the target website's domain name (e.g., example.com) as the ServerName.

## Step 2: Becoming the Man-in-the-Middle

```bash
sudo nano /etc/hosts
```

Add the following line:

```
malicious_ip example.com
```

## Step 3: Browse the Target Website

Open a web browser on the victim's machine and navigate to the target website (e.g., example.com)

# Task 6: Launching a Man-In-The-Middle Attack with a Compromised CA

```bash
openssl req -new -key server.key -out instagram.csr -config openssl.cnf
```

```bash
openssl ca -in instagram.csr -out instagram.crt -cert ca.crt -keyfile ca.key -config openssl.cnf
```

```bash
cp server.key instagram.pem
```

```bash
cat instagram.crt >> instagram.pem
```

```bash
cp instagram.crt cert2.pem
```

```bash
sudo mv cert2.pem /etc/apache2/ssl
```

