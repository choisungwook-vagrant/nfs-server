# 개요
* vagrant로 nfs서버 설치
* os: centos7

<br>

# 준비
* vagrant 설치
* vagrant 플러그인 설치
```sh
vagrant plugin install vagrant-disksize
```
* virtualbox 설치

<br>

# 설정
## virtualbox 하드웨어 설정
* config.yml파일을 수정합니다.
  * IP는 사용하고 있지 않아야 합니다. IP설정전 ping테스트를 권장합니다.

```yml
server:
  name: [vagrant 이름]
  box: centos/7 # 리눅스 버전 -> 변경불가
  hostname: [운영체제 hostname]
  ip: [브릿지 IP]
  memory: [메모리]
  cpu: [CPU]
  disksize: [저장공간]
```

## nfs 소프트웨어 설정
* /etc/exports: 192.168.25.0/24 대역 모든활동 허용
```sh
/nfs 192.168.25.* (rw,no_root_squash,rync)
```
* 공유볼륨 경로: /mnt/kubernetes

<br>

# virtualbox 실행
```
vagrant up
```

<br>

# virtualbox 삭제
```
vagrant destroy -f
```

<br>

# 참고자료
* vagrant 공식문서: https://www.vagrantup.com/docs/provisioning/shell#inline-scripts
* custom git gist: https://gist.github.com/bcantoni/f9ca55cd4393ec4929a2
