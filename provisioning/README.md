# vagrant-aws
Launch nodes and mount EBS disk

Edit aws_instance.json to declare nodes

vagrant up will:
- create instances
- extend root partition
- create xfs on both devices
- mount partition and update fstab
- reboot instances