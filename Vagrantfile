# number of worker nodes
NUM_WORKERS = 1
# number of extra disks per worker
NUM_DISKS = 1
# size of each disk in gigabytes
DISK_GBS = 10

MASTER_IP = "192.168.73.100"
WORKER_IP_BASE = "192.168.73.2" # 200, 201, ...
TOKEN = "yi6muo.4ytkfl3l6vl8zfpk"

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider :libvirt do |libvirt|
    libvirt.cpu_mode = 'host-passthrough'
    libvirt.graphics_type = 'none'
    libvirt.memory = 2048
    libvirt.cpus = 2
  end

  config.vm.provision "shell", path: "common.sh"

  config.vm.define "master" do |master|
    master.vm.hostname = "master"
    master.vm.network :private_network, ip: MASTER_IP
    master.vm.provision "shell", path: "master.sh",
      env: { "MASTER_IP" => MASTER_IP, "TOKEN" => TOKEN }
  end

  (0..NUM_WORKERS-1).each do |i|
    config.vm.define "worker#{i}" do |worker|
      worker.vm.hostname = "worker#{i}"
      worker.vm.network :private_network, ip: "#{WORKER_IP_BASE}" + i.to_s.rjust(2, '0')
      (1..NUM_DISKS).each do |j|
        worker.vm.provider :libvirt do |libvirt|
          libvirt.storage :file, :size => "#{DISK_GBS}G"
        end
      end
      worker.vm.provision "shell", path: "worker.sh",
        env: { "MASTER_IP" => MASTER_IP, "TOKEN" => TOKEN }
    end
  end
end
