# -*- mode: ruby -*-
# vi: set ft=ruby :

groups = { 
    "ambari_server" => ["master01"],
    "master01" => ["master01"],
    "master02" => ["master02"],
    "master03" => ["master03"],
    "db_server" => ["master03"],
    "datanodes" => ["datanode01","datanode02","datanode03","datanode04"],
    "all_groups:children" => ["master", "db_server","datanodes"]
}

aws_cfg = (JSON.parse(File.read("aws_instance.json")))

index=0

Vagrant.configure("2") do |config|
  config.vm.box = "dummy"
    config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
    config.vm.synced_folder ".", "/vagrant", disabled: true

    aws_cfg['ec2s'].each do |node|
      node_name  = node[0]
      node_value = node[1]

      # Node specific configuration
      config.vm.define node_name do |config2|
        ec2_tags = node_value['tags']
        ec2_disks = node_value['disks']

        # Spining up EC2 instances
        config2.vm.provider :aws do |ec2, override|
          index += 1
          ec2.access_key_id = ENV['AWS_KEY']
          ec2.secret_access_key = ENV['AWS_SECRET']
          ec2.keypair_name = aws_cfg['keypair']
          ec2.region = aws_cfg['region']
          ec2.availability_zone = aws_cfg['region']+aws_cfg['availability_zone']
          ec2.security_groups = aws_cfg['security_groups']
          ec2.ami = aws_cfg['ami_id']
          ec2.instance_type = node_value['instance_type']
          ec2.block_device_mapping = [
            {
              'DeviceName' => '/dev/sda1',
              'Ebs.VolumeSize' => 100 ,
              'Ebs.DeleteOnTermination' => true
            },
            {
              'DeviceName' => "/dev/sdb",
              'VirtualName' => "hadoop1",
              'Ebs.VolumeSize' => 100,
              'Ebs.DeleteOnTermination' => true
            },
            {
              'DeviceName' => "/dev/sdc",
              'VirtualName' => "hadoop2",
              'Ebs.VolumeSize' => 100,
              'Ebs.DeleteOnTermination' => true
            }
          ]
          ec2.tags = {
              'Name'         => ec2_tags['Name'],
              'Role'         => ec2_tags['Role']
          }
          override.ssh.username = aws_cfg['ssh_username']
          override.ssh.private_key_path = aws_cfg['ssh_private_key']
        end
      end
    end
    # Force ansible to run only once 
    if index == 0
      config.vm.provision :ansible do |ansible|
        ansible.playbook = "provisioning/site.yml"
        ansible.sudo = true
        ansible.limit = "all"
        ansible.groups = groups
        index = 1
      end
    end
end
