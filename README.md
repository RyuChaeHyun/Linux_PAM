# 🌾 Linux_PAM
VirtualBox에서 Ubuntu VM 환경을 사용하여 **네트워크 충돌을 방지**하고 **보안 강화**를 목표로 고정 IP 설정과 비밀번호 규제를 적용하는 방법을 구현하였습니다.

### 🎖️ 프로젝트 목표

1. 복제된 VM 환경에서 유동 IP(DHCP)를 사용하면 네트워크 관리가 복잡해지기 때문에, 이를 개선하기 위해 **고정 IP를 설정하는 방법**을 다루고 있습니다.
2. 시스템 보안 수준을 높이기 위해 **PAM 모듈을 사용한 비밀번호 규제 설정**을 통해 더 강력한 사용자 인증을 목표로 하게 되었습니다.

<br>

### 📍 고정 ip 설정

동일한 vm을 복제했을 때 dhcp 말고 다른 방법으로 연결해야한다. 

**00-installer-config.yaml 파일 수정**
```xml
sudo nano /etc/netplan/00-installer-config.yaml

또는

sudo vi /etc/netplan/00-installer-config.yaml
```

**설정 파일 편집**

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 10.0.2.21/24  # 변경된 고정 IP 주소
      routes:
        - to: default
          via: 10.0.2.1  # 게이트웨이
      nameservers:
        addresses:
          - 8.8.8.8
      dhcp4: **false**
```

**설정 후 적용**

```yaml
sudo netplan apply
```

**IP 주소가 제대로 적용되었는지 확인하려면 다음 명령어로 네트워크 상태를 확인**
    
    ```yaml
    ip a  또는 ip addr
    ```

<br>

**고정 아이피 할당 완료** <br>
10.0.2.21/24

![pam2](https://github.com/user-attachments/assets/48dff0eb-9765-4b9f-8a69-49e68fc7ad92)


**네트워크 접속을 위한 포트포워딩 설정 추가** <br>
![port](https://github.com/user-attachments/assets/7d11e9dd-de3c-4ea4-98d4-cb82e07f7cd0)



**접속완료**

<br>

MobaXTerm 에서도 접속이 된다. <br>

![mobaxterm](https://github.com/user-attachments/assets/e5c2308a-ae2f-4aa6-acd2-e603b30801ee)


--

### 📍 비밀번호 규제 설정

비밀번호 규제를 설정하려면 PAM(Pluggable Authentication Modules) 설정을 변경해야 한다.

Ubuntu에서는 `libpam-pwquality` 모듈을 통해 비밀번호 규제를 설정할 수 있다. 

1) `libpam-pwquality` 모듈을 설치한다.

```yaml
sudo apt-get install libpam-pwquality
```

2. `/etc/pam.d/common-password` 파일을 편집하여 비밀번호 규제를 설정한다.

```yaml
sudo vi /etc/pam.d/common-password
```

3. 다음과 같은 줄을 찾아 비밀번호 규제를 추가한다.

```yaml
password    requisite     pam_pwquality.so retry=3

변경후

password    requisite     pam_pwquality.so retry=3 minlen=12 dcredit=-1 ucredit=-1 lcredit=-1 ocredit=-1
```

- `minlen=12` : 최소 비밀번호 길이 설정
- `dcredit=-1` : 최소 하나 이상의 숫자 포함
- `ucredit=-1` : 최소 하나 이상의 대문자 포함
- `lcredit=-1` : 최소 하나 이상의 소문자 포함
- `ocredit=-1` : 최소 하나 이상의 특수 문자 포함

비밀번호는 최소 12자리 이상이어야 하며, 숫자, 대문자, 소문자, 특수 문자가 최소 하나씩 포함되어야 하도록 설정하였다.

4. 비밀번호 규제가 적용되었는지 테스트한다.


```yaml
sudo passwd user02
```

5. 규제를 지키지 않고 비밀번호 입력시 비밀번호가 생성되지 않는다.

![pam1](https://github.com/user-attachments/assets/62d0c1b2-c674-4e8c-99f1-89c66e5d8942)

<br>


### 🤔 결론

이번 프로젝트를 통해 **VM 환경에서의 네트워크 설정**과 **보안 강화**에 대한 중요한 경험을 쌓을 수 있었습니다.  
고정 IP 설정을 통한 VM 복제 시 **IP 충돌 방지**와 **강화된 비밀번호 정책**을 구현함으로써 **시스템 안정성**과 **보안성**을 크게 향상시켰습니다.  

---

**앞으로의 방향**:
- 네트워크 보안 강화를 위해 **방화벽 설정** 및 **사용자 접근 제어 정책** 등의 추가적인 보안 설정을 적용할 예정입니다.
- 다양한 환경에서 네트워크 및 보안 정책이 어떻게 작동하는지 더 깊이 연구해보고 싶습니다.
