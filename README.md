# Rsyslog

## 📌 rsyslog 기본 설치 여부 및 버전 정리

아래 표는 **표준(일반) 설치 기준**으로 정리한 내용입니다.
> ⚠️ *Minimal 설치, 커스텀 이미지, 컨테이너 환경에서는 rsyslog가 기본 설치되지 않을 수 있습니다.*

| 운영체제 | rsyslog 기본 설치 | 비고 | 기본 rsyslog 버전(배포판 기준) |
|---|---:|---|---|
| RHEL 8 | ✔️ | 대부분 설치됨 | 8.37.0 |
| RHEL 9 | ✔️ | 대부분 설치됨 | 8.2102.0 |
| RHEL 10 | ✔️ | 대부분 설치됨 | 8.2412.0 |
| Ubuntu 20.04 (Focal) | ✔️ | Minimal 설치 시 미설치 가능 | 8.2001.0-1ubuntu1 |
| Ubuntu 22.04 (Jammy) | ✔️ | Minimal 설치 시 미설치 가능 | 8.2112.0-2ubuntu2 |
| Ubuntu 24.04 (Noble) | ✔️ | Minimal 설치 시 미설치 가능 | 8.2312.0-3ubuntu9 |

> 📎 참고
> - Ubuntu는 보안 업데이트에 따라 패키지 릴리스 번호가 증가할 수 있습니다.
> - 실제 운영 환경에서는 `rsyslogd -v` 명령으로 정확한 버전을 확인하세요.

---

Example using postfix rsyslog.conf

### After modifying rsyslog.conf, we must check the config.

### 1. Run/debug configuration

    rsyslogd -N 1

### 2. Configure PLURA Log Collector Server to receive logs from Client Servers.

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/70-imudp-plura.conf -o /etc/rsyslog.d/
    
    systemctl restart rsyslog

### 3. Configure Client

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/80-postfix.conf -o /etc/rsyslog.d/
    

#### 3.1 Change @PLURA_Log_Collector_Server:514

    local7.info     @PLURA_Log_Collector_Server:514
    
    systemctl restart rsyslog

### 4. Configure Server

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/99-fromhost_ip_msgonly.conf -o /etc/rsyslog.d/
    
    systemctl restart rsyslog

#### 4.1 Allow service ports on Server

    firewall-cmd --add-port={514/tcp,514/udp} --permanent
    firewall-cmd --reload

### 5. Check on PLURA Log Collector Server

    ls -al /var/log/plura/


### Useful Links

#### 1) https://www.rsyslog.com/doc/v8-stable/configuration/templates.html

#### 2) https://www.rsyslog.com/doc/v8-stable/rainerscript/control_structures.html

#### 3) https://kifarunix.com/a-basic-introduction-to-rsyslog-filters/2/

#### 4) https://docs.docker.com/config/containers/logging/syslog/

#### 5) https://www.ibm.com/support/pages/qradar-how-configure-rsyslog-ubuntu-forward-apache-http-access-logs

#### 6) https://techviewleo.com/configure-rsyslog-system-logging-on-rocky-almalinux/

