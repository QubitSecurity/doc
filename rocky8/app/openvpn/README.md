# OpenVPN

## 1. Install for Server

### 1.1 Install

    dnf --enablerepo=epel -y install openvpn easy-rsa net-tools
            
### 1.2 Create CA and Certificates
    
    cd /usr/share/easy-rsa/3
    
    # initialize
    easyrsa init-pki
    
    # create CA
    easyrsa build-ca
    
    # set any name
    Common Name (eg: your user, host, or server name) [Easy-RSA CA]:PLURA-CA
    
    easyrsa build-server-full server1 nopass

    # generate Diffie Hellman ( DH ) parameter
    easyrsa gen-dh
    
    # create TLS-Auth key
    openvpn --genkey --secret ./pki/ta.key


### 1.3 Copy

    cp -pR /usr/share/easy-rsa/3/pki/{issued,private,ca.crt,dh.pem,ta.key} /etc/openvpn/server/
       
### 1.4 Config

    vi /etc/openvpn/server/server.conf

### 1.5 Register service

    mkdir /etc/openvpn/scritps/
    
    vi /etc/openvpn/scritps/add-bridge.sh
    
    vi /etc/openvpn/scritps/remove-bridge.sh


### 1.6 

    chmod 700 /etc/openvpn/scritps/{add-bridge.sh,remove-bridge.sh}
    
    systemctl edit openvpn-server@server
    
    [Service]
    ExecStartPost=/etc/openvpn/scritps/add-bridge.sh
    ExecStopPost=/etc/openvpn/scritps/remove-bridge.sh

### 1.7

    firewall-cmd --get-default-zone
    firewall-cmd --get-active-zones
    
    firewall-cmd --permanent --zone=trusted --add-interface=enp1s0
    
### 1.8

    chcon -u system_u /etc/openvpn/scripts/add-bridge.sh
    chcon -u system_u /etc/openvpn/scripts/remove-bridge.sh
    
    ls -Z /etc/openvpn/scripts/add-bridge.sh
    
    grep "type=AVC" /var/log/audit/audit.log
    
    grep "type=AVC" /var/log/audit/audit.log | audit2allow -a -M openvpn_rule
    
    semodule -i openvpn_rule.pp
    
### 1.9

    systemctl enable --now openvpn-server@server
    
    systemctl restart openvpn-server@server
    
    systemctl status openvpn-server@server

## 2 Install for Client

### 2.1 Create Key

    easyrsa build-client-full Client1
    
    mkdir /root/Client1
    cp /usr/share/easy-rsa/3/pki/private/Client1.key /root/Client1
    cp /usr/share/easy-rsa/3/pki/issued/Client1.crt /root/Client1
    cp /etc/openvpn/scripts/ta.key /root/Client1
    
    zip -xi Client1.zip ca.crt Client1.crt Client1.key ta.key

## X. Useful Links

    https://www.server-world.info/en/note?os=Rocky_Linux_8&p=openvpn&f=1