Vagrant router demo

```ruby
Vagrant.configure("2") do |config|
  config.vm.define "client1" do |client1|
    client1.vm.box = "ubuntu/trusty64"
    client1.vm.network "private_network", ip: "192.168.100.100"
    client1.vm.provision "shell", inline: <<-SHELL
      apt-get update
    SHELL
    client1.vm.provision "shell", run: "always", inline: "route add -net 192.168.200.0/24 gw 192.168.100.254"
    client1.vm.provision "shell", inline: "echo client1 finished"
  end

  config.vm.define "router" do |router|
    router.vm.box = "ubuntu/trusty64"
    router.vm.network "private_network", ip: "192.168.100.254"
    router.vm.network "private_network", ip: "192.168.200.254"
    router.vm.provision "shell", inline: <<-SHELL
      apt-get update
    SHELL
    router.vm.provision "shell", run: "always", inline: "route del default"
    router.vm.provision "shell", run: "always", inline: "sysctl -w net.ipv4.ip_forward=1"
    router.vm.provision "shell", inline: "echo router finished"
  end

  config.vm.define "client2" do |client2|
    client2.vm.box = "ubuntu/trusty64"
    client2.vm.network "private_network", ip: "192.168.200.100"
    client2.vm.provision "shell", inline: <<-SHELL
      apt-get update
    SHELL
    client2.vm.provision "shell", run: "always", inline: "route add -net 192.168.100.0/24 gw 192.168.200.254"
    client2.vm.provision "shell", inline: "echo client2 finished"
  end
end
```