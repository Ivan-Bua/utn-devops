# Configuración de Vagrant
Vagrant.configure("2") do |config|
  # Box base: Ubuntu 18.04 LTS
  config.vm.box = "ubuntu/bionic64"

  # Redirección de puertos: el puerto 80 de la VM al puerto 8080 del host
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Sincronización de carpetas: el directorio del proyecto se mapea con /vagrant en la VM
  config.vm.synced_folder ".", "/vagrant"

  # Copiar el archivo de configuración del servidor web desde la máquina host a la VM
  config.vm.provision "file", source: "./web_config.conf", destination: "/tmp/web_config.conf"

  # Script de aprovisionamiento
  config.vm.provision "shell", inline: <<-SHELL
    # Actualizar paquetes e instalar dependencias
    sudo apt-get update -y
    sudo apt-get install -y apache2 php git curl unzip

    # Mover el archivo de configuración del servidor web al directorio de Apache
    sudo mv /tmp/web_config.conf /etc/apache2/sites-available/000-default.conf
    sudo systemctl restart apache2

    # Descargar el código de la aplicación desde el repositorio de GitHub
    sudo rm -rf /var/www/html/*
    cd /var/www/html
    sudo git clone https://github.com/Fichen/utn-devops-app.git .
    sudo chmod -R 755 /var/www/html

    # Instalar dependencias de la aplicación si es necesario (puedes ajustar esto según el lenguaje)
    if [ -f "package.json" ]; then
      sudo apt-get install -y npm
      sudo npm install
    fi
    if [ -f "composer.json" ]; then
      sudo apt-get install -y composer
      composer install
    fi

    # Reiniciar Apache para cargar el sitio
    sudo systemctl restart apache2
  SHELL
end

