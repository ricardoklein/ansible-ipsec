ANSIBLE IPSEC
=========

This role installs StrongSwan on a single "client" with simple pre-shared key auth.

This role does not set any Firewall rules.

This role does not set any packet forwarding, so if you need to setup a site-to-site 
connection, you need extra effort. This could be done by this role, if you need it,
feel free to pay a coffee and I may do it ;-).

Currently supports:
* openSUSE Leap 15

CURRENT ISSUES:
only works with one connection as I can't figure out why in this section of 
swanctl.conf:

```
# IKE preshared secret section for a specific secret.
secrets {
  ike-1 {
      id-1a  = left-ip-addr
      id-1b  = right-ip-addr
      secret = some-secret-password
  }
```

I can't name the secret anything but `ike-1`.
I tried to name it `connectionname` and `connnectionname-1`, but then none of
the SAS come up.
If you know why, please open an issue and tell me :-)

Requirements
------------



Role Variables
--------------

Almost everything is setup by defaults/main.yml (you can check there).
But you need to set at least 1 connection (there is a fake one in 
defaults/main.yml so you can see how it works).

Be very carefull with values, Ansible will change yes/no to True/False and will 
try to interpret things with {} like 'subnets' ipsec option. Trust me, I lost
a few hours before discover that that was my problem, check my defaults file.

Dependencies
------------

Create a vault protected secret string for the ipsec secret:
```
  echo -ne 'supersecret' | ansible-vault encrypt_string  --ask-vault-pass --stdin-name 'secret'
```

Example Playbook
----------------

```
    - hosts: all
      roles:
         - { role: kleinstuff.ansible_ipsec }
      vars:
        ansible_ipsec__conns:
          ipsec_conn_name:
            left: OUR LOCAL IP (can be the same as the public)
            right: THE OTHER SIDE PUBLIC IP
            virtual_ip: 192.168.88.1 # '{ The virtual ip of this machine in the tunnel }'
            name : conn01
            ike_version: 1
            ike_proposals: aes256-sha256-modp1536
            reauth_time: 86400s
            dpd_delay: 120s
            local_ts: 192.168.88.0/30           # tunnel internal network for left  - routing
            remote_ts: 10.1.1.1/32,10.2.2.0/24  # tunnel internal network for right - routing
            esp_proposals: aes256-sha256-modp1536 # usualy esp and ike proposals are the same
            life_time: 28800s    # child lifetime
            start_action: start  # always auto-start the tunnel
            secret: !vault |
                  $ANSIBLE_VAULT;1.1;AES256
                  32633463653339363465616363346363363330313336386563326638343366653934353333346331
                  3533333038343332356632656166616339383637373039380a303431323635653634343339363831
                  37663664616133396237363531336435306537376632396362663662613134386336336665313838
                  6632396236376439620a363264643661396165363538343466303061623762336162643333373462
                  6636
```
You can add as many "ipsec_conns" as you need.

License
-------

GPL

Author Information
------------------

If you want to suggest changes or request new features, please feel free to create a issue or send a pull request.

If you find this useful, you can help donating Monero:

`86srKqPRnzpLCkihCHJ9ZB8sjg7B1QxBmT7xS6ebsL52JgCSHVsDsTqDCAqQveasfGh95AZei11Dc1fwjwrE42t3QhzHkdm`
