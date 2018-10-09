# -*- mode: ruby -*-
# vi: set ft=ruby :

# The "2" in Vagrant.configure configures the configuration version.
# Please don't change it unless you know what you're doing.
Vagrant.configure(2) do |config|
  # https://docs.vagrantup.com.

  # More at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end
  
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
    # sudo apt-get install -qq nodejs
    # sudo apt-get install -qq npm
    # sudo apt-get install -qq ruby

    # Workaround for https://bugs.launchpad.net/ubuntu/+source/ca-certificates-java/+bug/1396760
    sudo /var/lib/dpkg/info/ca-certificates-java.postinst configure


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
    echo "Install Perlbrew + CPANminus"
    cd ~/

    perlbrew init
    echo "source ~/perl5/perlbrew/etc/bashrc" >> ~/.bashrc

    source ~/perl5/perlbrew/etc/bashrc

    perlbrew self-upgrade
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
    # sudo gem install sass

  SHELL
end
