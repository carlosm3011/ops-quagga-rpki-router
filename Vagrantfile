# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder "config", "/home/shared/config"
  config.vm.synced_folder "dist", "/home/shared/dist"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
     # Display the VirtualBox GUI when booting the machine
     vb.gui = false

     # Customize the amount of memory on the VM:
     vb.memory = "2048"

     # name
     vb.name = "qrpki-router"

     config.vm.define "qrpki-router" do |q|
     end
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # install base packages from repos
  config.vm.provision "shell", inline: <<-SHELL
    sudo mkdir -p /home/shared/dist || /bin/true
    sudo mkdir -p /home/shared/config || /bin/true
    sudo apt-get -y install wget
    sudo apt-get -y install git
    sudo apt-get -y install cmake
  SHELL

  # building rtrlib and its dependencies
  config.vm.provision "shell", inline: <<-SHELL
    # FALTA: zlib1g
    sudo apt-get -y install zlib1g-dev
    sudo apt-get -y install libssl-dev
    sudo apt-get -y install g++
    # FALTA: libssl-dev
    # FALTA: g++
    HOME="/home/vagrant"
    SSHLIB_SRC="https://red.libssh.org/attachments/download/140/libssh-0.7.0.tar.xz"
    RTRLIB_SRC="https://github.com/rtrlib/rtrlib/archive/v0.3.0.tar.gz"
    #build libssh
    echo %%% Building libssh
    cd $HOME
    mkdir sshlib
    /usr/bin/wget -q --output-document=$HOME/sshlib.tar.xz $SSHLIB_SRC
    /bin/tar xvfJ sshlib.tar.xz -C sshlib --strip-components=1
    cd sshlib
    mkdir build
    cd build
    /usr/bin/cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release ..
    /usr/bin/make
    /usr/bin/make install
    #build rtrlib
    echo %%% Building rtrlib
    cd $HOME
    mkdir rtrlib
    /usr/bin/wget -q --output-document=$HOME/rtrlib.tar.gz $RTRLIB_SRC
    /bin/tar xzvf rtrlib.tar.gz -C rtrlib --strip-components=1
    cd rtrlib
    /usr/bin/cmake -D CMAKE_BUILD_TYPE=Release .
    /usr/bin/make
    /usr/bin/make install
    #
    #build quagga
    echo %%% Building quagga-rtr
    sudo apt-get -y install automake autoconf libtool libreadline-dev texinfo
    cd $HOME
    /usr/bin/git clone https://github.com/rtrlib/quagga-rtrlib.git quagga-rtrlib
    cd quagga-rtrlib
    ./update-autotools
    ./configure --enable-rpki --enable-user=root --enable-group=root --with-cflags=-ggdb \
      --sysconfdir=/usr/local/etc/quagga --enable-vtysh \
      --localstatedir=/var/run/quagga
    /usr/bin/make
    /usr/bin/make install
    #
    echo "/usr/local/lib" > /etc/ld.so.conf.d/usr.local.lib.conf
    echo "/usr/local/lib/x86_64-linux-gnu/" >> /etc/ld.so.conf.d/usr.local.lib.conf
    mkdir -p /var/run/quagga
    /sbin/ldconfig
    #
    echo "%%% Copying configuration files"
    mkdir -p /usr/local/etc/quagga/
    mkdir -p /var/run/quagga/
    cp -v /home/shared/config/vtysh.conf /usr/local/etc/quagga/
    cp -v /home/shared/config/bgpd.conf /usr/local/etc/quagga/
  SHELL

end
