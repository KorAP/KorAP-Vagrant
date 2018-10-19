# -*- mode: ruby -*-
# vi: set ft=ruby :

# The "2" in Vagrant.configure configures the configuration version.
# Please don't change it unless you know what you're doing.
Vagrant.configure(2) do |config|

  # https://docs.vagrantup.com.

  # More at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Memory size is set for installation of Krill
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end

  config.vm.network "forwarded_port", guest: 5555, host: 5555
  config.vm.network "forwarded_port", guest: 5556, host: 5556
  
  config.vm.box_download_insecure = true
  
  # Provisioning of KorAP with a Shell script
  config.vm.provision "shell", privileged: false, inline: <<-SHELL

    ###############################################
    echo "Install Packages"

    # Add repository for OpenJDK
    sudo add-apt-repository ppa:openjdk-r/ppa

    sudo apt-get update

    echo "Install dependencies"
    sudo apt-get install -qq git
    sudo apt-get install -qq openjdk-8-jdk
    sudo apt-get install -qq perlbrew
    sudo apt-get install -qq emacs
    sudo apt-get install -qq maven
    sudo apt-get install -qq nodejs
    sudo apt-get install -qq npm
    sudo apt-get install -qq ruby

    ###############################################
    echo "Install Koral"
    cd ~/
    if [ -e ./Koral ] && [ -d ./Koral ]
     then
       cd Koral
       git pull origin master
     else
       git clone https://github.com/KorAP/Koral.git Koral
       cd Koral
    fi
    mvn clean install -Dhttps.protocols=TLSv1.2    


    ###############################################
    echo "Install Krill"
    cd ~/
    if [ -e ./Krill ] && [ -d ./Krill ]
     then
       cd Krill
       git pull origin master
     else
       git clone https://github.com/KorAP/Krill.git Krill
       cd Krill
    fi
    mvn clean install


    ###############################################
    echo "Install Kustvakt"
    cd ~/
    if [ -e ./Kustvakt ] && [ -d ./Kustvakt ]
     then
       cd Kustvakt
       git pull origin master
     else
       git clone https://github.com/KorAP/Kustvakt.git Kustvakt
       cd Kustvakt
    fi

    cd ~/Kustvakt/core
    mvn clean install

    cd ~/Kustvakt/lite
    mvn clean package


    ###############################################
    echo "Install NodeJS"
    # This is required unfortunately
    if [ ! -e ~/tmp ]; then
      mkdir ~/tmp
    fi
    npm set ca null
    sudo npm install -g n
    sudo n stable
    sudo npm install -g sass
    sudo npm install -g grunt-cli
    sudo npm install grunt


    ###############################################
    echo "Install Perlbrew + CPANminus"
    cd ~/

    perlbrew init
    echo "source ~/perl5/perlbrew/etc/bashrc" >> ~/.bashrc

    source ~/perl5/perlbrew/etc/bashrc

    perlbrew install -q perl-5.24.0

    perlbrew switch perl-5.24.0
    perlbrew install-cpanm


    ###############################################
    echo "Install Kalamar server-side dependencies"
    cpanm git://github.com/Akron/Mojolicious-Plugin-Search.git
    cpanm git://github.com/Akron/Mojolicious-Plugin-Localize.git
    cpanm git://github.com/Akron/Mojolicious-Plugin-TagHelpers-ContentBlock.git


    ###############################################
    echo "Install Kalamar"
    cd ~/
    if [ -e ./Kalamar ] && [ -d ./Kalamar ]
     then
       cd Kalamar
       git pull origin master
     else
       git clone git://github.com/KorAP/Kalamar.git Kalamar
       cd Kalamar
    fi

    cpanm --installdeps .

    ###############################################
    # echo "Install Kalamar client-side dependencies"
    npm install
    grunt


    ###############################################
    echo "Prepare Kustvakt"
    cd ~/

    if [ ! -e ./Built ]; then
      mkdir Built
    fi

    # Copy the jar file to the built folder
    # This will do so for all files - but the last one will be kept
    find ~/Kustvakt/lite/target/Kustvakt-lite-*.jar -exec mv {} ~/Built/Kustvakt-lite.jar ';'

    # Rewrite the configuration file
    sed -e 's#^krill\.indexDir\s*=\s*.*$#krill.indexDir=../Kustvakt/sample-index#gm' \
        -e 's#^server\.port\s*=\s*.*$#server.port=5556#gm' \
        ~/Kustvakt/lite/src/main/resources/kustvakt-lite.conf \
        > ~/Built/kustvakt-lite.conf

    # Start the server
    cd ~/Built

    # Kill Kustvakt before restarting
    if [ -f ./kustvakt.pid ]
      then
        echo 'Shudown Kustvakt server'
        kill -9 `cat ./kustvakt.pid`
    fi

    echo "Start Kustvakt"
    nohup java -jar ./Kustvakt-lite.jar & echo $! > ./kustvakt.pid


    ###############################################
    echo "Start Kalamar"
    cd ~/
    cd Kalamar

    # Add new configuration
    echo "{hypnotoad=>{listen=>['http://*:5555']}}" \
      > kalamar.vagrant.conf

    echo "not really secret" > kalamar.secret

    # Start the server
    KALAMAR_API="http://localhost:5556/api/" \
      MOJO_MODE=vagrant \
      hypnotoad script/kalamar


    ###############################################
    echo "Establish systemd"

    echo "[Unit]
Description=Kustvakt
After=network.target

[Service]
User=root
Type=forking
ExecStart=/bin/su - vagrant -c 'cd /home/vagrant/Built ; nohup java -jar Kustvakt-lite.jar & echo $! > kustvakt.pid'
PIDFile=/home/vagrant/Built/kustvakt.pid
KillMode=process

[Install]
WantedBy=multi-user.target" | sudo tee /lib/systemd/system/kustvakt.service

    echo "[Unit]
Description=Kalamar
After=network.target

[Service]
User=root
Type=forking
PIDFile=/home/vagrant/Kalamar/script/hypnotoad.pid
ExecStart=/bin/su - vagrant -c 'MOJO_MODE=vagrant KALAMAR_API=\"http://localhost:5556/api/\" /home/vagrant/perl5/perlbrew/perls/perl-5.24.0/bin/hypnotoad /home/vagrant/Kalamar/script/kalamar'
ExecStop=/bin/su - vagrant -c 'MOJO_MODE=vagrant /home/vagrant/perl5/perlbrew/perls/perl-5.24.0/bin/hypnotoad -s /home/vagrant/Kalamar/script/kalamar'
ExecReload=/bin/su - vagrant -c 'MOJO_MODE=vagrant KALAMAR_API=\"http://localhost:5556/api/\" /home/vagrant/perl5/perlbrew/perls/perl-5.24.0/bin/hypnotoad /home/vagrant/Kalamar/script/kalamar'
killMode=process

[Install]
WantedBy=multi-user.target" | sudo tee /lib/systemd/system/kalamar.service

    sudo systemctl enable kustvakt
    sudo systemctl enable kalamar

  SHELL
end
