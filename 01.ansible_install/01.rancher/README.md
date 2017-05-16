# Run Rancher server and add hosts
- https://github.com/galal-hussein/Rancher-Ansible 활용

## 1. hosts inventory 설정
- inventories/rancher-hosts
```
[Rancher]
rancher ansible_ssh_port=22 ansible_ssh_host=192.1.x.x ansible_user=rts

[Agents]
agent1 ansible_ssh_port=22 ansible_ssh_host=192.1.x.x ansible_user=rts
```


## 2. docker 설정(vars) 수정

### docker user 설정
- roles/docker/vars/main.yml
```
---
# vars file for docker
docker_users:
  - 'rts'
```

### docker version, repo등 설정
- roles/docker/vars/RedHat.yml
```
__docker_pre_packages:
  - yum-utils
  - python-setuptools

# Old versions
##############
__docker_repo_url: https://yum.dockerproject.org/repo/main/{{ ansible_distribution | lower }}/7/
__docker_package: docker-engine
__docker_version: 1.12.5-1.el7.centos
__docker_repo_key: https://yum.dockerproject.org/gpg
__docker_build: stable
```

## 3. rancher 설정(vars) 수정
- inventories/group_vars/main.yml
```
rancher_name: rancher_server
rancher_server: "{{ hostvars['rancher']['ansible_ssh_host'] }}"
rancher_version: v1.5.6
rancher_port: 8080
rancher_agent_name: "agent"

```

## 4. docker & rancher-server & rancher-host 설치
```
> ansible-playbook -i inventories/staging/rancher-hosts rancher.yml
```
