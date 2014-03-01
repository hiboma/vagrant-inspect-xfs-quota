# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
:
: benri-tools
(
    which strace || yum install -y strace
    which vim    || yum install -y vim-enhanced
    yum install -y  xfsdump xfsprogs
)

:
: setup-xfs-quota
(
    echo vagrant:1  > /etc/projid
    echo 1:/mnt/xfs > /etc/projects

    # perl -pi'*.back' -e 's{/mnt/xfs xfs defaults }{/mnt/xfs xfs defaults,prjquota }' /etc/fstab

    # Limit inodes usage
    xfs_quota -x -c 'limit -p isoft=1000 ihard=1000 vagrant' /mnt/xfs

    # Limit blocks usage
    xfs_quota -x -c 'limit -p bsoft=50M   bhard=50M vagrant' /mnt/xfs

    xfs_quota -x -c 'report'             /mnt/xfs
    xfs_quota -x -c 'report -i'          /mnt/xfs
    xfs_quota -x -c 'project -s vagrant' /mnt/xfs
)

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "CentOS 6.5 x86_64"

  config.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.1/centos65-x86_64-20131205.box"

  config.vm.provision "shell", inline: $script

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
  end

  config.persistent_storage.enabled      = true
  config.persistent_storage.location     = "./disks/sourcehdd.vdi"
  config.persistent_storage.size         = 100
  config.persistent_storage.mountname    = 'xfs'
  config.persistent_storage.filesystem   = 'xfs'
  config.persistent_storage.mountpoint   = '/mnt/xfs'
  config.persistent_storage.mountoptions = ['defaults', 'prjquota']
end
