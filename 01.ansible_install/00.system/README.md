# Set ssh connection login with password
- Rancher Server와 Rancher host간 ssh connection을 설정
- Rancher Server에서 docker image를 생성하고,
- 생성된 image를 rancher host에 scp로 복사하여, image load하기 위해 필요함.

## 1. create_ssh_key
- rancher server에서 ssh key를 생성하고
- 생성된 ssh public key를 ansible manager server로 복사한다.

## 2. copy-ssh-id
###  rancher server의 pulic key를 host서버에 복사
- 여기까지만 하면 "Are you sure you want to continue connecting (yes / no)?" 가 리턴되어 ssh 접속이 되지 않는다.
- 위 메세지가 보이지 않도록 rancher server의 known_hosts에 host들의 key정보를 등록해야 한다.
### Add/update the public key in the '{{ ssh_known_hosts_file }}'
- ssh-keyscan 명령어를 사용하여, host들의 public key를 수집하고,
- 이를 known_hosts에 등록한다.

### 실행
```
> ansible-playbook -i inventories/staging/hosts ssh.yml
```
