# ---------------------------------------------------------
# Vagrantfile - Cross-platform (Windows/Linux/macOS 지원)
# ---------------------------------------------------------

VAGRANT_BOX   = "ubuntu/jammy64"
NAT_NETWORK   = "vagrant-net"
MASTER_IP     = "192.168.56.10"
WORKER1_IP    = "192.168.56.11"
WORKER2_IP    = "192.168.56.12"

# 호스트 OS 자동 감지
HOST_OS = case RUBY_PLATFORM
when /win32|mingw|cygwin/ then :windows
when /darwin/             then :mac
else :linux
end

# Windows → ansible_local / Linux·mac → ansible
PROVISIONER = HOST_OS == :windows ? "ansible_local" : "ansible"

Vagrant.configure("2") do |config|
  config.vm.box_check_update = false

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus   = 2
  end

  # -------------------------------------------------
  # 공통 ansible 설정 함수 (limit 옵션을 OS별로 제어)
  # -------------------------------------------------
  def setup_ansible(ansible, limit = nil, inventory_file = nil)
    ansible.playbook = "ansible/playbook.yml"

    if RUBY_PLATFORM =~ /win32|mingw|cygwin/
      # Windows에서는 ansible_local 사용 → inventory 파일 개별 지정
      ansible.inventory_path = inventory_file
      ansible.limit = nil
    else
      ansible.inventory_path = "ansible/inventory.ini"
      ansible.limit = limit unless limit.nil?
    end
  end

  # -------------------------------
  # Master Node
  # -------------------------------
  config.vm.define "k8s-master" do |node|
    node.vm.box      = VAGRANT_BOX
    node.vm.hostname = "k8s-master"
    node.vm.network "private_network",
      ip: MASTER_IP,
      virtualbox__intnet: NAT_NETWORK

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus   = 2
    end

    node.vm.provision PROVISIONER do |ansible|
      if HOST_OS == :windows
        setup_ansible(ansible, nil, "/vagrant/ansible/local_inventory_master.ini")
      else
        setup_ansible(ansible, "k8s-master")
      end
    end
  end

  # -------------------------------
  # Worker Node 1
  # -------------------------------
  config.vm.define "k8s-worker1" do |node|
    node.vm.box      = VAGRANT_BOX
    node.vm.hostname = "k8s-worker1"
    node.vm.network "private_network",
      ip: WORKER1_IP,
      virtualbox__intnet: NAT_NETWORK

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 1
    end

    node.vm.provision PROVISIONER do |ansible|
      if HOST_OS == :windows
        setup_ansible(ansible, nil, "/vagrant/ansible/local_inventory_worker.ini")
      else
        setup_ansible(ansible, "k8s-worker1")
      end
    end
  end

  # -------------------------------
  # Worker Node 2
  # -------------------------------
  config.vm.define "k8s-worker2" do |node|
    node.vm.box      = VAGRANT_BOX
    node.vm.hostname = "k8s-worker2"
    node.vm.network "private_network",
      ip: WORKER2_IP,
      virtualbox__intnet: NAT_NETWORK

    node.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus   = 1
    end

    node.vm.provision PROVISIONER do |ansible|
      if HOST_OS == :windows
        setup_ansible(ansible, nil, "/vagrant/ansible/local_inventory_worker.ini")
      else
        setup_ansible(ansible, "k8s-worker2")
      end
    end
  end
end
