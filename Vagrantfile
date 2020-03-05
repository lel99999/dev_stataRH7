require 'vagrant-aws'
require 'yaml'

#Vagrant.configure('2') do |config|
#  config.vm.define "vagrantAWS-Stata16" do |stata16|
#    stata16.vm.box = 'aws-dummy'
#    stata16.vm.synced_folder ".", "/vagrant", disabled: true
#    stata16.vm.provider 'aws' do |aws,override|
#      aws_config = YAML::load_file(File.join(Dir.home, ".aws_secrets"))
#      aws.access_key_id = aws_config.fetch("access_key_id")
#      aws.secret_access_key = aws_config.fetch("secret_access_key")
#      aws.keypair_name = aws_config.fetch("keypair_name")
##       aws.instance_type = "t2.medium"
#      aws.instance_type = "t2.small"
##       aws.instance_type = "t2.micro"
#      aws.region = aws_config.fetch("aws_region")
#      aws.ami = aws_config.fetch("aws_ami")
#      aws.security_groups = aws_config.fetch("security_groups")
##       aws.security_groups = 'vagrant'
##       aws.security_groups = ['default']
#      aws.subnet_id = aws_config.fetch("subnet_id")
##       aws.tags = {
##         'Name'=> "vagrantAWS-EOD"
##       }
#
#      aws.tags = {
#        'Name'=> "vagrantAWS-Stata16"
#      }
#      aws.block_device_mapping = [{
#        'DeviceName' => '/dev/sda1',
#        'Ebs.VolumeSize' => 100,
#        'Ebs.DeleteOnTermination' => true,
##         'Ebs.DeleteOnTermination' => false,
#        'Ebs.VolumeType' => 'gp2'
##         'Ebs.VolumeType' => 'io1'
##         'Ebs.Iops' => 1000
##         'DeviceName' => AWS_DEVICE_NAME,
##         'Ebs.ValumeSize' => AWS_DEVICE_SIZE,
##         'Ebs.DeleteOnTermination' => false,
##         'Ebs.VolumeType' => AWS_DEVICE_VOL_TYPE,
#      }]
#
#      override.ssh.username = aws_config.fetch("ssh_username")
#      override.ssh.private_key_path = aws_config.fetch("private_key_path")
#    end
#    config.vm.provision "ansible" do |ansible|
##     ansible.playbook = "deploy_ancillaryRH7.yml"
##     ansible.playbook = "deploy_eodrole.yml"
#      ansible.playbook = "deploy_stataRH7.yml"
##     ansible.groups = {
##       "vagrantAWS" => ["vagrantAWS-Data"]
##     }
#      ansible.inventory_path = ".vagrant_hosts"
#      ansible.verbose = "true"
#    end
#  end
#end

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |v|
    v.memory = "1024"
    v.cpus = 2
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "70"]
#   v.default_nic_type = "82543GC"
  end
# config.trigger.after :up do |trigger|
#   run "subscription-manager register --username <username> --password <password> --auto-attach
#   trigger.name = "After-Up Trigger ..."
#   trigger.info = "Trigger Execution ..."
#   trigger.run = { path:"subscription-manager register --username <username> --password <password> --auto-attach"}
# end
  config.vm.define "stataRH7" do |stataRH7|
    stataRH7.vm.box = "clouddood/RH7.5_baserepo"
    stataRH7.vm.hostname = "stataRH7"
    stataRH7.vm.network "private_network", ip: "192.168.60.157"
#   stataRH7.vm.network "private_network", ip: "192.168.60.157", nic_type: "virtio"
    stataRH7.vm.provision "shell", :inline => "sudo echo '192.168.60.157 stataRH7.local stataRH7' >> /etc/hosts"

    # Default
    stataRH7.vm.provision "main", type: "ansible" do |ansible|
#     ansible.playbook = "deploy_stataRH7_DEV.local.yml"
      ansible.playbook = "deploy_stataRH7_DEV.yml"
#     ansible.playbook = "deploy_stataTestRH7.yml"
      ansible.inventory_path = "vagrant_hosts"
      #ansible.tags = ansible_tags
      #ansible.verbose = ansible_verbosity
      #ansible.extra_vars = ansible_extra_vars
      #ansible.limit = ansible_limit
    end
    # Update
    stataRH7.vm.provision "update", type: "ansible" do |ansible|
      ansible.playbook = "deploy_stataPatchRH7_DEV.local.yml"
#     ansible.playbook = "deploy_stataTestRH7.yml"
      ansible.inventory_path = "vagrant_hosts"
      #ansible.tags = ansible_tags
      #ansible.verbose = ansible_verbosity
      #ansible.extra_vars = ansible_extra_vars
      #ansible.limit = ansible_limit
    end
  end
# config.trigger.before :destroy do |trigger|
#   run "rm -Rf /tmp/abc/*"
    # subscription-manager remove --all
    # subscription-manager unregister
    # subscription-manager clean
#   trigger.name = "Destroy Trigger ..."
#   trigger.info = "Destroy Trigger Execution ..."
#   trigger.run = { path: "subscription-manager remove --all"}
#   trigger.run = { path: "subscription-manager unregister"}
#   trigger.run = { path: "subscription-manager clean"}
# end
end
