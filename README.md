# deploying-a-static-website-using-NGINX-on-an-Ubuntu-server

#!/bin/bash

# Update the system
sudo apt update && sudo apt upgrade -y

# Check if update was successful
if [ $? -ne 0 ]; then
    echo "System update failed."
    exit 1
fi

# Install Git, NGINX, and curl
sudo apt install git nginx curl -y

# Check if installations were successful
if [ $? -ne 0 ]; then
    echo "Installation of required packages failed."
    exit 1
fi

# Clone your GitHub repository
git clone https://[github.com/your-username/your-repo-name.git
](https://github.com/Gbenga001/deploying-a-static-website-using-NGINX-on-an-Ubuntu-server/edit/main/README.md)
# Check if cloning was successful
if [ $? -ne 0 ]; then
    echo "Git clone failed."
    exit 1
fi

# Start NGINX and enable it to start on boot
sudo systemctl start nginx
sudo systemctl enable nginx

# Move your website files to the NGINX web root
sudo mv your-repo-name/* /var/www/html/

# Check if move was successful
if [ $? -ne 0 ]; then
    echo "Moving website files failed."
    exit 1
fi

# Set proper permissions
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html

# Configure NGINX
cat << EOF | sudo tee /etc/nginx/sites-available/default
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;
    server_name _;
    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF

# Test NGINX configuration
sudo nginx -t

# If the test is successful, restart NGINX
if [ $? -eq 0 ]; then
    sudo systemctl restart nginx
    echo "Deployment successful! Your website should now be accessible."
    echo "Public IP: $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4)"
else
    echo "NGINX configuration test failed. Please check your configuration."
fi
