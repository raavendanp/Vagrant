# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  

  config.vm.define "db" do |db|
    db.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    git clone https://github.com/raavendanp/movie-analyst-api
    sudo apt-get install -y mysql-server
    sudo systemctl start mysql
    sudo systemctl enable mysql
    sudo sed -i "s/.*bind-address.*/bind-address = 0.0.0.0/" /etc/mysql/mysql.conf.d/mysqld.cnf
    sudo systemctl restart mysql
    sudo mysql -u root -ppass
    sudo mysql -u root -ppass -e "CREATE DATABASE movie_db;"
    sudo mysql -u root -ppass -e "use movie_db;"
    sudo mysql -u root -ppass -e "source /home/vagrant/movie-analyst-api/data_model/table_creation_and_inserts.sql"
    sudo mysql -u root -ppass -e "CREATE USER 'admin_db'@'%' IDENTIFIED BY '1234';"
    sudo mysql -u root -ppass -e "GRANT ALL PRIVILEGES ON *.* TO 'admin_db'@'%' WITH GRANT OPTION;"
    sudo mysql -u root -ppass -e "flush privileges;"
    sudo mysql -u root -ppass -e "ALTER USER 'admin_db'@'%' IDENTIFIED WITH mysql_native_password BY '1234';"
    sudo mysql -u root -ppass -e "flush privileges;"
  SHELL
    db.vm.box = "ubuntu/focal64"
    db.vm.network "public_network", ip: "192.168.1.32" , bridge: "Realtek PCIe GbE Family Controller"
    db.vm.provider "virtualbox" do |vb|
        vb.memory = 1048
        vb.cpus = 1
    end
  end

  
  

  config.vm.define "front" do |front|
    front.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y nodejs
    sudo apt-get install -y npm
    git clone https://github.com/raavendanp/movie-analyst-ui
    cd movie-analyst-ui
    npm install
    npm install pm2 -g
    sudo printf "BACK_HOST = 192.168.1.30\n" > .env
    sudo pm2 start server.js
    

  SHELL
    front.vm.box = "ubuntu/focal64"
    front.vm.network "public_network", ip: "192.168.1.31", bridge: "Realtek PCIe GbE Family Controller"
    front.vm.provider "virtualbox" do |vb|
        vb.memory = 1048
        vb.cpus = 1
    end
  end

  config.vm.define "back" do |back|
    back.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y nodejs
    sudo apt-get install -y npm
    git clone https://github.com/raavendanp/movie-analyst-api
    cd movie-analyst-api
    npm install
    npm install pm2 -g
    sudo printf "DB_HOST = 192.168.1.32\nDB_USER = admin_db\nDB_PASS = 1234\nDB_NAME = movie_db\nPORT=3000\n" > .env
    sudo pm2 start server.js
   
  SHELL
    back.vm.box = "ubuntu/focal64"
    back.vm.network "public_network", ip: "192.168.1.30" , bridge: "Realtek PCIe GbE Family Controller"
    back.vm.provider "virtualbox" do |vb|
        vb.memory = 1048
        vb.cpus = 1
    end
  end

  


  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
