# basic-dns

Domain Setup and HTTPS Configuration
1️⃣ Buy a Domain

Purchase a domain from any registrar (e.g., Namecheap, Cloudflare, Google Domains).

In your domain settings, set the nameservers to DigitalOcean:
```bash
ns1.digitalocean.com
ns2.digitalocean.com
ns3.digitalocean.com
```

2️⃣ Configure DNS on DigitalOcean

Go to Networking → Domains in your DigitalOcean dashboard and add your domain (e.g., hydranoid.site).

Create DNS records:

Type	Hostname	Value
A	@	Your droplet’s public IP
CNAME	www	<your_site>.site.

3️⃣ Configure Nginx

Create a new site configuration file:
```bash
sudo nano /etc/nginx/sites-available/hydranoid.site
```

Add the following:
```bash
server {
    listen 80;
    server_name hydranoid.site www.hydranoid.site;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name hydranoid.site www.hydranoid.site;

    ssl_certificate /etc/ssl/hydranoid/hydranoid.crt;
    ssl_certificate_key /etc/ssl/hydranoid/hydranoid.key;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

4️⃣ Enable the Site

Activate your site by creating a symbolic link:
```bash
sudo ln -s /etc/nginx/sites-available/hydranoid.site /etc/nginx/sites-enabled/
```

To avoid conflicts, remove the default config:
```bash
sudo rm /etc/nginx/sites-enabled/default
```

5️⃣ Test and Reload Nginx
```bash
sudo nginx -t
sudo systemctl reload nginx
```

6️⃣ Install and Run Certbot

Install Certbot with the Nginx plugin:
```bash
sudo apt install certbot python3-certbot-nginx -y
```

Automatically issue and configure HTTPS certificates:
```bash
sudo certbot --nginx -d hydranoid.site -d www.hydranoid.site
```

Certbot will:

- Verify your domain

- Get a free SSL certificate from Let’s Encrypt

- Update your Nginx configuration

- Enable HTTPS

7️⃣ Verify Configuration

Check that everything works:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

Visit your website in a browser:

https://<your_site>.site

8️⃣ Test Auto-Renewal
```bash
sudo certbot renew --dry-run
```
This simulates certificate renewal to confirm that automatic updates will work.
