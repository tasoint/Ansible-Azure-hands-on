# Chapter 1: 基本編 - VMの作成 (Roleベース)

この章では、Ansible PlaybookをRoleを使用してAzure仮想マシン（VM）を作成します。

## プロジェクト構成

以下のディレクトリ構成でAnsibleプロジェクトを作成します。

```
Chapter1/
├── ansible.cfg
├── inventory.yml
├── main.yml
└── roles/
    ├── network/
    │   └── tasks/
    │       └── main.yml
    ├── resource_group/
    │   └── tasks/
    │       └── main.yml
    └── vm/
        └── tasks/
            └── main.yml
```

## Ansible設定ファイル (ansible.cfg)

```ini
[defaults]
inventory = ./inventory.yml
roles_path = ./roles
```

## インベントリファイル (inventory.yml)

```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
```

## Playbook (main.yml)

以下のPlaybookを使用してVMを作成します。

```yaml
- hosts: localhost
  tasks:
    - ansible.builtin.import_role:
        name: resource_group
    - ansible.builtin.import_role:
        name: network
    - ansible.builtin.import_role:
        name: vm
```

## Role: resource\_group (roles/resource\_group/tasks/main.yml)

リソースグループを作成するRoleです。

```yaml
- name: Create resource group
  azure.azcollection.azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## Role: network (roles/network/tasks/main.yml)

ネットワークリソースを作成するRoleです。

```yaml
- name: Create virtual network
  azure.azcollection.azure_rm_virtualnetwork:
    name: myVNet
    resource_group: myResourceGroup
    location: eastus
    address_prefixes:
      - 10.0.0.0/16

- name: Create subnet
  azure.azcollection.azure_rm_subnet:
    name: mySubnet
    resource_group: myResourceGroup
    virtual_network_name: myVNet
    address_prefix: 10.0.1.0/24

- name: Create public IP address
  azure.azcollection.azure_rm_publicipaddress:
    name: myPublicIP
    resource_group: myResourceGroup
    location: eastus
    allocation_method: Dynamic

- name: Create network interface card
  azure.azcollection.azure_rm_networkinterface:
    name: myNIC
    resource_group: myResourceGroup
    location: eastus
    virtual_network_name: myVNet
    subnet_name: mySubnet
    public_ip_name: myPublicIP
```

## Role: vm (roles/vm/tasks/main.yml)

VMを作成するRoleです。

```yaml
- name: Create virtual machine
  azure.azcollection.azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_B1s
    location: eastus
    image:
      publisher: Canonical
      offer: UbuntuServer
      sku: 18.04-LTS
      version: latest
    admin_username: azureuser
    admin_password: 'YourPassword123!'
    network_interface_names:
      - myNIC
```

## 主要なオプション解説

- `resource_group`: リソースグループ名
- `name`: VM名
- `vm_size`: VMサイズ
- `location`: リージョン
- `image`: VMイメージ
- `admin_username`: 管理者ユーザー名
- `admin_password`: 管理者パスワード
- `network_interface_names`: ネットワークインターフェース名

## Playbookの実行

以下のコマンドでPlaybookを実行します。

```bash
ansible-playbook main.yml
```

## 作成したVMへの接続確認

作成したVMにSSHで接続し、正常に作成されていることを確認します。

```bash
ssh azureuser@<VMのPublic IPアドレス>
