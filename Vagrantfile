# Definice proměnné pro box (platí v celém Vagrantfile)
IMAGE_NAME = "bento/ubuntu-24.04"

Vagrant.configure("2") do |config|
    config.vm.boot_timeout = 600

    # Globální nastavení pro VirtualBox (platí pro všechny VM)
    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end

    # ---- Zabbix server ----
    config.vm.define "zabbix-srv01" do |zabbix|
        zabbix.vm.provider "virtualbox" do |v|
            v.name = "Zabbix-SRV01"
        end
        zabbix.vm.box = IMAGE_NAME
        zabbix.vm.hostname = "zabbix"
        zabbix.vm.network "forwarded_port", guest: 22, host: 2202, host_ip: "127.0.0.1"
        zabbix.vm.network "forwarded_port", guest: 80, host: 8002, host_ip: "127.0.0.1"
        zabbix.vm.network "private_network", ip: "192.168.1.2", virtualbox__intnet: "intnet"
    end

    # ---- Grafana server ----
    config.vm.define "grafana-srv02" do |grafana|
        grafana.vm.provider "virtualbox" do |v|
            v.name = "Grafana-SRV02"
        end
        grafana.vm.box = IMAGE_NAME
        grafana.vm.hostname = "grafana"
        grafana.vm.network "forwarded_port", guest: 22, host: 2203, host_ip: "127.0.0.1"
        grafana.vm.network "forwarded_port", guest: 3000, host: 3000, host_ip: "127.0.0.1"
        grafana.vm.network "private_network", ip: "192.168.1.3", virtualbox__intnet: "intnet"
    end

    # ---- Bastion (s Ansible provision) ----
    config.vm.define "bastion-srv03" do |bastion|
        bastion.vm.provider "virtualbox" do |v|
            v.memory = 1024   # přepis globálního nastavení (méně RAM)
            v.name = "Bastion-SRV03"
        end
        bastion.vm.box = IMAGE_NAME
        bastion.vm.hostname = "bastion"
        bastion.vm.network "private_network", ip: "192.168.1.5", virtualbox__intnet: "intnet"

        # Provision pomocí Ansible (nainstaluje se automaticky do bastionu)
        bastion.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "ansible_provision.yml"
            ansible.install_mode = "default"   # nainstaluje Ansible na VM
        end
    end
end