## Fortinet FortiGate IPsec VPN IKEv2 SAML authentication

### Index
- [LDAP Server](#ldap-server)
- [User Group](#user-group)
- [Phase-1 IPsec config](#phase-1-ipsec-config)
- [Phase-2 IPsec config](#phase-2-ipsec-config)

## LDAP Server
~~~
config user ldap
    edit IPsec-LDAP-Server
        set server ldap.example.com
        set cnid cn
        set dn dc=example,dc=com
        set type regular
        set username username
        set password <secret>
    next
end
~~~

## User Group

~~~
config user group
    edit <groupname>
        set member IPsec-LDAP-Server
    end
end
~~~

## Phase-1 IPsec config

~~~
config vpn ipsec phase1-interface
    edit IPsec-LDAP-VPN
        set type dynamic
        set interface wan
        set ike-version 2
        set peertype one
        set peerid IPsec-LDAP-VPN-PeerID
        set net-device disable
        set mode-cfg enable
        set ipv4-dns-server1 <primary DNS>
        set ipv4-dns-server2 <secondary DNS>
        set proposal aes256-sha256
        set dhgrp 21
        set eap enable
        set eap-identity send-request
        set ipv4-start-ip 172.31.250.10
        set ipv4-end-ip 172.31.250.200
        set client-auto-negotiate enable
        set client-keep-alive enable
        set psksecret <secret>
    next
end
~~~

If you plan to use more than one dialup tunnel I would suggest using network-overlay so the FortiGate can match the incomming request to the right tunnel
~~~
config vpn ipsec phase1-interface
    edit IPsec-LDAP-VPN
        set network-overlay enable
        set network-id <id>
    next
end
~~~

For noisy links such as incomming 4G/5G connections use Forward Error Correction

~~~
config vpn ipsec phase1-interface
    edit IPsec-LDAP-VPN
        set fec-egress enable
        set fec-ingress enable
        set fec-codec rs
    next
end
~~~

## Phase-2 IPsec config
~~~
config vpn ipsec phase2-interface
    edit IPsec-LDAP-VPN-P2
        set phase1name IPsec-LDAP-VPN
        set proposal aes256-sha256
        set dhgrp 21
    next
end
~~~