# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "precise64-rails-dev"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  #config.vm.box_url = "../packer_box/build/ubuntu-12.04.3-server-amd64.box"

  config.vm.synced_folder "~/Projects/tasboa/rails_env/eventfuel", "/home/vagrant/eventfuel-server"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 5432, host: 5432

  config.vm.hostname = 'rails-dev-box'

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network :private_network, ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider :virtualbox do |vb|
  #   # Don't boot with headless mode
       # vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # Enable provisioning with Puppet stand alone.  Puppet manifests
  # are contained in a directory path relative to this Vagrantfile.
  # You will need to create the manifests directory and a manifest in
  # the file tasboa_rails_dev.pp in the manifests_path directory.
  #
  # An example Puppet manifest to provision the message of the day:
  #
  # # group { "puppet":
  # #   ensure => "present",
  # # }
  # #
  # # File { owner => 0, group => 0, mode => 0644 }
  # #
  # # file { '/etc/motd':
  # #   content => "Welcome to your Vagrant-built virtual machine!
  # #               Managed by Puppet.\n"
  # # }
  #
  # config.vm.provision :puppet do |puppet|
  #   puppet.manifests_path = "manifests"
  #   puppet.manifest_file  = "site.pp"
  # end

  # Enable provisioning with chef solo, specifying a cookbooks path, roles
  # path, and data_bags path (all relative to this Vagrantfile), and adding
  # some recipes and/or roles.
  #
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = "cookbooks"
    
    chef.add_recipe "build-essential"
    chef.add_recipe "apt"
    chef.add_recipe  "git"
    chef.add_recipe 'rvm::vagrant'
    chef.add_recipe 'rvm::system'
    chef.add_recipe "postgresql::server"
    chef.add_recipe "postgresql::libpq"
    chef.add_recipe 'nodejs'
    chef.add_recipe 'libzmq'
    chef.add_recipe 'imagemagick'


    chef.json = {
      "rvm" => {
      # ths instalation of chef for some reason is not in the default path so use this to correct locate the binary 
        'vagrant' => {
          'system_chef_solo' => '/opt/chef/bin/chef-solo'
          },
        #create the default gemset and ruby 
        "default_ruby" => "ruby-1.9.3-p448@eventfuel-server"   
        #use this to add more rubies
        # "rubies" => ["ruby-1.9.3-p448", "ruby-2.1.0"]
      }, 
      "postgresql" => {
        "ssl" => false ,
        "version" => "9.3",
        'listen_addresses' => '*',
        "users"=> [
          {
            "username"=> "eventfuel",
            "password"=> "eventfuel",
            "superuser"=> true,
            "createdb"=> true,
            "login"=> true
          }
        ],
        "databases"=> [
          {
            "name" => "eventfuel_dev",
            "owner" => "eventfuel",
            "template" => "template0",
            "encoding"=> "utf8",
            "locale"=> "en_US.UTF8",
          },
          {
            "name"=> "eventfuel_test",
            "owner"=> "eventfuel",
            "template"=> "template0",
            "encoding"=> "utf8",
            "locale"=> "en_US.UTF8",
          }          
        ],
        "pg_hba" => [
          {:type => 'local', :db => 'all', :user => 'eventfuel', :addr => nil, :method => 'ident'},
          {:type => 'local', :db => 'all', :user => 'all', :addr => nil, :method => 'ident'},
          {:type => 'host', :db => 'all', :user => 'all', :addr => '127.0.0.1/32', :method => 'trust'},
          {:type => 'host', :db => 'all', :user => 'all', :addr => '::1/128', :method => 'trust'},
          {:type => 'host', :db => 'all', :user => 'all', :addr => '10.0.0.0/8', :method => 'trust'}
        ]
      }
    }
    
  end

  # the simplest way to install all the necessary gems cause I havent found a chef recipe to handle it. As rvm by default install bundle this works without problems
   config.vm.provision :shell, :inline => 'cd /home/vagrant/eventfuel-server && bundle install'

  # Enable provisioning with chef server, specifying the chef server URL,
  # and the path to the validation key (relative to this Vagrantfile).
  #
  # The Opscode Platform uses HTTPS. Substitute your organization for
  # ORGNAME in the URL and validation key.
  #
  # If you have your own Chef Server, use the appropriate URL, which may be
  # HTTP instead of HTTPS depending on your configuration. Also change the
  # validation key to validation.pem.
  #
  # config.vm.provision :chef_client do |chef|
  #   chef.chef_server_url = "https://api.opscode.com/organizations/ORGNAME"
  #   chef.validation_key_path = "ORGNAME-validator.pem"
  # end
  #
  # If you're using the Opscode platform, your validator client is
  # ORGNAME-validator, replacing ORGNAME with your organization name.
  #
  # If you have your own Chef Server, the default validation client name is
  # chef-validator, unless you changed the configuration.
  #
  #   chef.validation_client_name = "ORGNAME-validator"
end
