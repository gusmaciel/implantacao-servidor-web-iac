Vagrant.configure("2") do |config|
  
  config.vm.hostname = "SRV-WEB"
  config.vm.box = "cloud-image/debian-13"

  config.vm.network "forwarded_port", guest: 80, host: 8443, host_ip: "127.0.0.1"

  config.vm.network "private_network", type: "dhcp"

  config.vm.synced_folder "meu-site/", "/var/www/meu-site"

  config.vm.provider "virtualbox" do |vbox|
    vbox.name = "SRV-WEB"  
    vbox.memory = "1024"
    vbox.cpus = 1
    vbox.check_guest_additions = true
  end
  
  config.vm.provision "shell", inline: <<-SHELL

    # Variáveis
    SITE_NAME="meu-site"
    WEB_ROOT="/var/www/${SITE_NAME}"

    apt update
    apt install -y apache2

    # Cria VirtualHost
    cat > /etc/apache2/sites-available/${SITE_NAME}.conf <<EOF
      <VirtualHost *:80>
          ServerAdmin webmaster@localhost
          ServerName ${SITE_NAME}
          DocumentRoot ${WEB_ROOT}

          <Directory ${WEB_ROOT}>
              Options Indexes FollowSymLinks
              AllowOverride All
              Require all granted
          </Directory>

          ErrorLog \${APACHE_LOG_DIR}/${SITE_NAME}_error.log
          CustomLog \${APACHE_LOG_DIR}/${SITE_NAME}_access.log combined
      </VirtualHost>
EOF

    # Habilita o site
    a2ensite ${SITE_NAME}.conf

    # Desabilita site padrão
    a2dissite 000-default.conf

    # Reinicia Apache
    systemctl restart apache2
    systemctl enable apache2

  SHELL
end
