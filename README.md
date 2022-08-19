# Rsyslog
Example using postfix rsyslog.conf 

### After modifying rsyslog.conf, we must check the config.

### 1. Run/debug configuration

    rsyslogd -N1

### 2. Configure PLURA Log Collector Server to receive logs from Client Servers.

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/70-imudp-plura.conf -o /etc/rsyslog.d/
    
    systemctl restart rsyslog

### 3. Configure Client

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/80-postfix.conf -o /etc/rsyslog.d/
    

#### 3.1 Change @PLURA_Log_Collector_Server:514

    if $programname == 'postfix' then @PLURA_Log_Collector_Server:514 #UDP
    :programname, isequal, "postfix"  ~
    
    systemctl restart rsyslog

### 4. Configure Server

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/99-fromhost_ip_msgonly.conf -o /etc/rsyslog.d/
    
    systemctl restart rsyslog

#### 4.1 Allow service ports on Server

    firewall-cmd --add-port={514/tcp,514/udp} --permanent
    firewall-cmd --reload

### 5. Check on PLURA Log Collector Server

    ls -al /var/log/plura/


### Reference

#### 1) https://www.rsyslog.com/doc/v8-stable/configuration/templates.html

#### 2) https://www.rsyslog.com/doc/v8-stable/rainerscript/control_structures.html

#### 3) https://kifarunix.com/a-basic-introduction-to-rsyslog-filters/2/

#### 4) https://docs.docker.com/config/containers/logging/syslog/
