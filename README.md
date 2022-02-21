# nginxplus-ubuntu-20.04-userdata-aws
User data script to install nginx plus in Ubuntu 20.04 on AWS
Copy and paste the text below in your user-data field when launching new ubuntu-20.04 instance on AWS
Remember to fill your own nginx-repo.crt/key in the placeholders below.

```
#!/bin/bash
# use this in user-data (aws)
apt-get update
apt-get upgrade -y
mkdir /etc/ssl/nginx
cat << EOF > /etc/ssl/nginx/nginx-repo.crt
-----BEGIN CERTIFICATE-----
< your own nginx-repo.crt content goes here >
-----END CERTIFICATE-----
EOF

cat << EOF > /etc/ssl/nginx/nginx-repo.key
-----BEGIN PRIVATE KEY-----
< your own nginx-repo.key content goes here >
-----END PRIVATE KEY-----
EOF

# for nginx plus installation
sudo wget https://cs.nginx.com/static/keys/nginx_signing.key && sudo apt-key add nginx_signing.key
sudo wget https://cs.nginx.com/static/keys/app-protect-security-updates.key && sudo apt-key add app-protect-security-updates.key
sudo apt-get install apt-transport-https lsb-release ca-certificates -y
printf "deb https://pkgs.nginx.com/plus/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-plus.list
printf "deb https://pkgs.nginx.com/app-protect/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-app-protect.list
printf "deb https://pkgs.nginx.com/app-protect-security-updates/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee -a /etc/apt/sources.list.d/nginx-app-protect.list
sudo wget -P /etc/apt/apt.conf.d https://cs.nginx.com/static/files/90pkgs-nginx
sudo apt-get update
sudo apt-get install -y nginx-plus
sudo systemctl enable nginx
sudo systemctl restart nginx

# below is for nginx app-protect
# will take more time..
# and you need to load the module in nginx.conf
## load_module modules/ngx_http_app_protect_module.so;

printf "deb https://pkgs.nginx.com/app-protect/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-app-protect.list
printf "deb https://pkgs.nginx.com/app-protect-security-updates/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee -a /etc/apt/sources.list.d/nginx-app-protect.list
sudo apt-get update
sudo apt-get install -y app-protect app-protect-attack-signatures
sudo reboot
```
