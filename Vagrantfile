# -*- mode: ruby -*-
# vi: set ft=ruby :

unless Vagrant.has_plugin?('nugrant')
  warn "[\e[1m\e[31mERROR\e[0m]: Please run: vagrant plugin install nugrant"
  exit -1
end
unless Vagrant.has_plugin?('vagrant-hostmanager')
  warn "[\e[1m\e[31mERROR\e[0m]: Please run: vagrant plugin install vagrant-hostmanager"
  exit -1
end

def setup_defaults()
  {
      'influxdata' => {
          'name' => 'InfluxData Server',
          'cpus' => '1',
          'memory' => '10240'
      },
      'customize' => ['modifyvm', :id,
                      '--nicpromisc2', 'allow-all',
                      '--groups', '/InfluxData'],
      'gui' => false,
      'dockerbox' => 'williamyeh/ubuntu-trusty64-docker',
      'ubuntubox' => 'ubuntu/trusty64'
  }
end

Vagrant.configure("2") do |config|

  # Load configurations
  config.user.defaults = setup_defaults

  # Enable ssh forward agent
  config.ssh.forward_agent = true

  # Enable hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true

  # Create Application server
  # ------------------------------------------------------------------------------------------------
  config.vm.define :influxdata, {:primary => true} do |server|

    server.vm.hostname = :influxdata
    server.vm.box = config.user.dockerbox

    # Network
    server.vm.network :private_network, :ip => '13.13.1.10', :netmask => '255.255.255.0'

    # VM configuration
    server.vm.provider :virtualbox do |vb|
      vb.name = config.user.influxdata.name

      vb.memory = config.user.influxdata.memory
      vb.cpus = config.user.influxdata.cpus

      vb.gui = config.user.gui

      vb.customize config.user.customize
    end

    config.vm.provision :hostmanager

    # The following line terminates all ssh connections. Therefore Vagrant will be forced to reconnect.
    # That's a workaround to have the docker command in the PATH and add Vagrant to the docker group by logging in/out
    config.vm.provision :logout, type: :shell, run: :always do |shell|
      shell.inline = "ps aux | grep 'sshd:' | awk '{print end $2}' | xargs kill"
    end

    server.vm.provision :influxdb, type: :docker, run: :always do |docker|
      docker.run "influxdb", args: "-p 8086:8086 -v \"/vagrant/influxdb:/etc/influxdb\""
    end

    server.vm.provision :telegraf, type: :docker, run: :always do |docker|
      docker.run "telegraf", args: "-p 8092:8092 -p 8125:8125 -p 8094:8094 -v \"/vagrant/telegraf:/etc/telegraf\""
    end

    server.vm.provision :chronograf, type: :docker, run: :always do |docker|
      docker.run "chronograf", args: "-p 8888:8888 -v \"/vagrant/chronograf:/etc/chronograf_tmp\""
    end

    server.vm.provision :kapacitor, type: :docker, run: :always do |docker|
      docker.run "kapacitor", args: "-p 9092:9092 -v \"/vagrant/kapacitor:/etc/kapacitor\""
    end
  end


  # Create Node server
  # ------------------------------------------------------------------------------------------------
  config.vm.define :node1 do |node|

    node.vm.hostname = :node1
    node.vm.box = config.user.ubuntubox

    # Network
    node.vm.network :private_network, :ip => '13.13.1.11', :netmask => '255.255.255.0'

    # VM configuration
    node.vm.provider :virtualbox do |vb|
      vb.name = config.user.influxdata.name + " node 1"

      vb.memory = config.user.influxdata.memory
      vb.cpus = config.user.influxdata.cpus

      vb.gui = config.user.gui

      vb.customize config.user.customize
    end

    $script = <<SCRIPT
wget https://dl.influxdata.com/influxdb/releases/influxdb_0.9.6_amd64.deb
sudo dpkg -i influxdb_0.9.6_amd64.deb
service influxdb stop
sed -i 's/^  hostname.*$/  hostname = "node1"/' /etc/influxdb/influxdb.conf
service influxdb start
SCRIPT

    node.vm.provision :logout, type: :shell, run: :always do |shell|
      shell.inline = $script
    end
  end

  config.vm.define :node2 do |node|

    node.vm.hostname = :node2
    node.vm.box = config.user.ubuntubox

    # Network
    node.vm.network :private_network, :ip => '13.13.1.12', :netmask => '255.255.255.0'

    # VM configuration
    node.vm.provider :virtualbox do |vb|
      vb.name = config.user.influxdata.name + " node 2"

      vb.memory = config.user.influxdata.memory
      vb.cpus = config.user.influxdata.cpus

      vb.gui = config.user.gui

      vb.customize config.user.customize
    end
    $script = <<SCRIPT
wget https://dl.influxdata.com/influxdb/releases/influxdb_0.9.6_amd64.deb
sudo dpkg -i influxdb_0.9.6_amd64.deb
service influxdb stop
sed -i 's/^  hostname.*$/  hostname = "node2"/' /etc/influxdb/influxdb.conf
echo 'INFLUXD_OPTS="-join node1:8088"' >/etc/default/influxdb
service influxdb start
SCRIPT

    node.vm.provision :logout, type: :shell, run: :always do |shell|
      shell.inline = $script
    end
  end

  config.vm.define :node3 do |node|

    node.vm.hostname = :node3
    node.vm.box = config.user.ubuntubox

    # Network
    node.vm.network :private_network, :ip => '13.13.1.13', :netmask => '255.255.255.0'

    # VM configuration
    node.vm.provider :virtualbox do |vb|
      vb.name = config.user.influxdata.name + " node 3"

      vb.memory = config.user.influxdata.memory
      vb.cpus = config.user.influxdata.cpus

      vb.gui = config.user.gui

      vb.customize config.user.customize
    end

    $script = <<SCRIPT
wget https://dl.influxdata.com/influxdb/releases/influxdb_0.9.6_amd64.deb
sudo dpkg -i influxdb_0.9.6_amd64.deb
service influxdb stop
sed -i 's/^  hostname.*$/  hostname = "node3"/' /etc/influxdb/influxdb.conf
echo 'INFLUXD_OPTS="-join node1:8088,node2:8088"' >/etc/default/influxdb
service influxdb start
SCRIPT

    node.vm.provision :logout, type: :shell, run: :always do |shell|
      shell.inline = $script
    end
  end
end
