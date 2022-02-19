IMAGE_NAME = "bento/ubuntu-18.04"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end

    config.vm.define "k8s-nfs-server1" do |nfs|
        nfs.vm.box = IMAGE_NAME
        nfs.vm.network "public_network", ip: "192.168.8.109" , bridge: "wlo1", use_dhcp_assigned_default_route: "true"
        nfs.vm.hostname = "k8s-nfs"



        # default router
        nfs.vm.provision "shell",
            run: "always",
            inline: "route add default gw 192.168.8.1"

        # # default router ipv6
        # nfs.vm.provision "shell",
        #     run: "always",
        #     inline: "route -A inet6 add default gw fc00::1 eth1"

        # delete default gw on eth0
        nfs.vm.provision "shell",
            run: "always",
            inline: "eval `route -n | awk '{ if ($8 ==\"eth0\" && $2 != \"0.0.0.0\") print \"route del default gw \" $2; }'`"


        nfs.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/nfs-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.8.109",
            }
        end
    end
  
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "public_network", ip: "192.168.8.110" , bridge: "wlo1", use_dhcp_assigned_default_route: "true"
        master.vm.hostname = "k8s-master"



        # default router
        master.vm.provision "shell",
            run: "always",
            inline: "route add default gw 192.168.8.1"

        # # default router ipv6
        # master.vm.provision "shell",
        #     run: "always",
        #     inline: "route -A inet6 add default gw fc00::1 eth1"

        # delete default gw on eth0
        master.vm.provision "shell",
            run: "always",
            inline: "eval `route -n | awk '{ if ($8 ==\"eth0\" && $2 != \"0.0.0.0\") print \"route del default gw \" $2; }'`"


        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "kubernetes-setup/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.8.110",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "public_network", ip: "192.168.8.#{i + 110}" ,bridge: "wlo1", use_dhcp_assigned_default_route: "true"
            node.vm.hostname = "node-#{i}"



            # default router
            node.vm.provision "shell",
                run: "always",
                inline: "route add default gw 192.168.8.1"

            # # default router ipv6
            # node.vm.provision "shell",
            #     run: "always",
            #     inline: "route -A inet6 add default gw fc00::1 eth1"

            # delete default gw on eth0
            node.vm.provision "shell",
                run: "always",
                inline: "eval `route -n | awk '{ if ($8 ==\"eth0\" && $2 != \"0.0.0.0\") print \"route del default gw \" $2; }'`"



            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "kubernetes-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.8.#{i + 110}",
                }
            end
        end
    end
end