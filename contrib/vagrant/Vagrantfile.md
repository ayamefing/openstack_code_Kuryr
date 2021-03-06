### Vagrantfile
vagrant 主配置文件。

基于 ubuntu trusty。

```sh
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  VM_MEMORY    = ENV.fetch('VAGRANT_KURYR_VM_MEMORY', 4096)
  VM_CPUS      = ENV.fetch('VAGRANT_KURYR_VM_CPUS', 2)
  RUN_DEVSTACK = ENV.fetch('VAGRANT_KURYR_RUN_DEVSTACK', 'true')

  config.vm.hostname = 'devstack'

  config.vm.provider 'virtualbox' do |v, override|
    override.vm.box = ENV.fetch('VAGRANT_KURYR_VM_BOX', 'trusty')
    v.memory        = VM_MEMORY
    v.cpus          = VM_CPUS
  end

  config.vm.provider 'parallels' do |v, override|
    override.vm.box = ENV.fetch('VAGRANT_KURYR_VM_BOX', 'boxcutter/ubuntu1404')
    v.memory        = VM_MEMORY
    v.cpus          = VM_CPUS
    v.customize ['set', :id, '--nested-virt', 'on']
  end

  config.vm.provider 'libvirt' do |v, override|
    override.vm.box = ENV.fetch('VAGRANT_KURYR_VM_BOX', 'celebdor/trusty64')
    v.memory        = VM_MEMORY
    v.cpus          = VM_CPUS
    v.nested        = true
    v.graphics_type = 'spice'
    v.video_type    = 'qxl'
  end

  config.vm.synced_folder '../../devstack/', '/devstack'
  # For CentOS machines it needs to be specified
  config.vm.synced_folder '.', '/vagrant'

  config.vm.provision :shell do |s|
    s.path = 'vagrant.sh'
    s.args = RUN_DEVSTACK
  end

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.scope = :box
  end

end
```
