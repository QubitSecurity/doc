# Xrdp
xrdp from EPEL and with using tigervnc

## 1. Install

### 1.1 Install

    dnf --enablerepo=epel -y install xrdp
    
### 1.2 Config

    vi /etc/xrdp/xrdp.ini
    
    [Xvnc]
    name=Xvnc
    lib=libvnc.so
    username=ask
    password=ask
    ip=127.0.0.1
    #port=-1
    port=5999

### 1.3 Run

    systemctl enable --now xrdp
    
### 1.4 Setting rules to firewalld

    firewall-cmd --add-port=3389/tcp
    
    firewall-cmd --runtime-to-permanent

### 1.5 Check

    netstat -tnlp

## X. Useful Links

    https://github.com/QubitSecurity/doc/tree/main/rocky8/app/tigervnc
