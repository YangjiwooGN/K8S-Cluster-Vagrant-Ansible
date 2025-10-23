# 🧩 Vagrant + Ansible 기반 Kubernetes Cluster 자동 구축 환경 (Virtual Box 환경)

> Windows / macOS / Linux에서 `vagrant up` 한 줄로  
> 3노드 쿠버네티스 클러스터(Master + 2 Worker)를 자동으로 설치하고 구성합니다.

---

## 🏗️ 프로젝트 개요

이 프로젝트는 Vagrant와 Ansible을 활용해  
로컬 환경에서 손쉽게 Kubernetes 클러스터를 구성할 수 있도록 자동화한 IaC(Infrastructure as Code) 구성입니다.

- Cross-platform 지원 (Windows, macOS, Linux)
- VM 생성부터 Kubernetes 설치까지 완전 자동화
- Containerd 런타임 + kubeadm 기반 설치
- Flannel CNI 네트워크 자동 배포
- OS별 Ansible 프로비저너 자동 선택 (`ansible` / `ansible_local`)
- Join 명령 자동 생성 및 워커 노드 자동 조인

---

## 🧰 구성 요소

| 구성 | 설명 |
|------|------|
| **Vagrantfile** | VirtualBox 기반 VM 정의 (Master + 2 Worker) |
| **Ansible Playbooks** | 시스템 업데이트, Containerd 설치, kubeadm 초기화, 클러스터 조인 등 자동화 |
| **roles/** | Ansible 역할 단위 구성 (common, master, worker) |
| **inventory 파일** | OS에 따라 동적으로 선택되는 인벤토리 구성 |
| **Flannel CNI** | Pod 네트워킹 자동 구성 (CIDR `10.244.0.0/16`) |

---

## 🖥️ VM 구성

| 노드명 | 역할 | IP | CPU | Memory |
|--------|------|----|-----|--------|
| `k8s-master` | Control Plane | `192.168.56.10` | 2 | 2048MB |
| `k8s-worker1` | Worker Node | `192.168.56.11` | 1 | 1024MB |
| `k8s-worker2` | Worker Node | `192.168.56.12` | 1 | 1024MB |

---

## 🚀 설치 가이드

### 필수 도구 설치

| OS | 필수 도구 |
|----|------------|
| **Windows** | [VirtualBox](https://www.virtualbox.org/wiki/Downloads), [Vagrant](https://developer.hashicorp.com/vagrant/downloads) |
| **macOS** | `brew install virtualbox vagrant` |
| **Ubuntu/Linux** | `sudo apt install virtualbox vagrant -y` |

---

### 저장소 클론

git clone https://github.com/<YOUR_USERNAME>/vagrant-k8s-cluster.git
cd vagrant-k8s-cluster


### 클러스터 생성

- Windows 사용자
ansible_local 프로비저너 자동 사용
vagrant up

- macOS / Linux 사용자
로컬 ansible 사용
(Ansible 미설치 시 sudo apt install ansible -y 또는 brew install ansible)
vagrant up

💡 자동으로 Master → Worker 순으로 부팅 및 조인됩니다.

### 클러스터 확인
모든 VM이 실행된 후, k8s-master 접속:

vagrant ssh k8s-master
kubectl get nodes


출력 예시:
NAME          STATUS   ROLES           AGE   VERSION
k8s-master    Ready    control-plane   5m    v1.30.14
k8s-worker1   Ready    <none>          3m    v1.30.14
k8s-worker2   Ready    <none>          3m    v1.30.14

🧩 프로젝트 구조

vagrant-k8s-cluster/
├── Vagrantfile
├── ansible/
│   ├── playbook.yml
│   ├── inventory.ini
│   ├── local_inventory_master.ini
│   ├── local_inventory_worker.ini
│   ├── roles/
│   │   ├── common/
│   │   │   └── tasks/main.yml
│   │   │   └── handlers/main.yml
│   │   ├── master/
│   │   │   └── tasks/main.yml
│   │   └── worker/
│   │       └── tasks/main.yml
└── README.md
