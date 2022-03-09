# Rsyslog
Rsyslog for PLURA

### After modifying rsyslog.conf, we must check the config.

### 1. Run/debug configuration

    rsyslogd -N1

### 2. Configure PLURA Log Collector Server to receive logs from Client Servers.

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/70-imudp-plura.conf -o /etc/rsyslog.d/
    
    systemctl restart rsyslog

### 3. Configure Client Servers

    curl https://raw.githubusercontent.com/QubitSecurity/rsyslog.d/main/v8-stable/plain/80-postfix.conf -o /etc/rsyslog.d/
    

#### 3.1 Change @PLURA_Log_Collector_Server:514

    if $programname == 'postfix' then @PLURA_Log_Collector_Server:514 #UDP
    :programname, isequal, "postfix"  ~
    
    systemctl restart rsyslog

### 4. Check on PLURA Log Collector Server

    ls -al /var/log/plura/


### Reference

https://www.rsyslog.com/doc/v8-stable/configuration/templates.html
