## Fortinet FortiGate IPsec VPN IKEv2 SAML authentication

### Index
- [RADIUS Server](#radius-server)
- [User Group](#user-group)
- [Phase-1 IPsec config](#phase-1-ipsec-config)
- [Phase-2 IPsec config](#phase-2-ipsec-config)

## RADIUS Server
~~~
config user radius
    edit "IPsec-RADIUS-Server"
        set server "radius.example.com"
        set secret <secret>
    next
end
~~~

## User Group

~~~
config user group
    edit <groupname>
        set member IPsec-RADIUS-Server
    end
end
~~~

## Phase-1 IPsec config

~~~
config vpn ipsec phase1-interface
    edit IPsec-RADIUS-VPN
        set type dynamic
        set interface wan
        set ike-version 2
        set peertype one
        set peerid IPsec-RADIUS-VPN-PeerID
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
    edit IPsec-RADIUS-VPN
        set network-overlay enable
        set network-id <id>
    next
end
~~~

For noisy links such as incomming 4G/5G connections use Forward Error Correction

~~~
config vpn ipsec phase1-interface
    edit IPsec-RADIUS-VPN
        set fec-egress enable
        set fec-ingress enable
        set fec-codec rs
    next
end
~~~

## Phase-2 IPsec config
~~~
config vpn ipsec phase2-interface
    edit IPsec-RADIUS-VPN-P2
        set phase1name IPsec-RADIUS-VPN
        set proposal aes256-sha256
        set dhgrp 21
    next
end
~~~