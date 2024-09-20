# 🌾 Linux_PAM
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

**고정 아이피 할당 완료** <br>
10.0.2.21/24

![pam2](https://github.com/user-attachments/assets/48dff0eb-9765-4b9f-8a69-49e68fc7ad92)


**네트워크 접속을 위한 포트포워딩 설정 추가** <br>
![port](https://github.com/user-attachments/assets/7d11e9dd-de3c-4ea4-98d4-cb82e07f7cd0)



**접속완료**
<br>
MobaXTerm 에서도 접속이 된다. <br> 
![moba](https://github.com/user-attachments/assets/7e99cc36-782f-46a9-a049-010df9bb36ae)

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

<br>

![pam1](https://github.com/user-attachments/assets/62d0c1b2-c674-4e8c-99f1-89c66e5d8942)

<br>

