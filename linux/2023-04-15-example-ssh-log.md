```
$ ssh -vvv ez-bastion
OpenSSH_9.2p1, OpenSSL 1.1.1t  7 Feb 2023 (which version of SSH and SSL client is using)
debug1: Reading configuration data /c/Users/eoyeb/.ssh/config (reading ssh config file)
debug1: /c/Users/eoyeb/.ssh/config line 8: Applying options for ez-bastion (located which configuation client will use to authenticate) 
debug1: Reading configuration data /etc/ssh/ssh_config (reading configuration of ssh)
debug2: resolve_canonicalize: hostname 54.86.217.61 is address (attempting to resolve the hostname)
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts' -> '/c/Users/eoyeb/.ssh/known_hosts' (creating knownhost file )
debug3: expanded UserKnownHostsFile '~/.ssh/known_hosts2' -> '/c/Users/eoyeb/.ssh/known_hosts2' (creating knownhost2 file)
debug3: ssh_connect_direct: entering (ssh client is attempting to use direct connection to the server rather than a proxy)
debug1: Connecting to 54.86.217.61 [54.86.217.61] port 22. (client has successfully resolved the hostname and is attempting to connect at port 22)
debug3: set_sock_tos: set socket 4 IP_TOS 0x48 
debug1: Connection established.
(below indicates whcih SSH private keys th client is attempting to use for authentication, and whether or not they have public key certificates)
debug1: identity file /c/Users/eoyeb/.ssh/id_ed25519 type 3
debug1: identity file /c/Users/eoyeb/.ssh/id_ed25519-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_9.2 (version of SSH the client is using in the SSH protocol)
debug1: Remote protocol version 2.0, remote software version OpenSSH_8.7 (the SSH protocol version and the SSH version the remote server is using)
debug1: compat_banner: match: OpenSSH_8.7 pat OpenSSH* compat 0x04000000 (the ssh client is checking whether the serves software and OS are compatible with its own and whether it needs to adjust it's behavior accordingly)
debug2: fd 4 setting O_NONBLOCK (client is setting a file descriptor to non-blocking to allow it to perform other tasks while waiting for data from the server)
debug1: Authenticating to 54.86.217.61:22 as 'excellent' (client attempting to authenticate to the server with the username 'excellent')
debug3: record_hostkey: found key type ED25519 in file /c/Users/eoyeb/.ssh/known_hosts:1 (SSH client is reading its known host to fine a list of hostnames and their associated public keys; the client found an ECDSA public key in known hosts that matches host key)
debug3: record_hostkey: found key type ECDSA in file /c/Users/eoyeb/.ssh/known_hosts:2
debug3: load_hostkeys_file: loaded 2 keys from 54.86.217.61 (client has loaded one public key for the server)
debug1: load_hostkeys: fopen /c/Users/eoyeb/.ssh/known_hosts2: No such file or directory 
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts2: No such file or directory
debug3: order_hostkeyalgs: have matching best-preference key type ssh-ed25519-cert-v01@openssh.com, using HostkeyAlgorithms verbatim (client is specifying which host key algo it prefers to use in its authentication process)
debug3: send packet: type 20 (client sent a packet to server)
debug1: SSH2_MSG_KEXINIT sent (packet sent is a SSH key exchange init message)
debug3: receive packet: type 20 (client recieved a packet from the server)
debug1: SSH2_MSG_KEXINIT received (packet recieved is a SSH key init message)
debug2: local client KEXINIT proposal (client's proposal for the key exhange algo, encryption and hash algo and other cryptographic parameters)
debug2: KEX algorithms: sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256,ext-info-c
(key exchange algo that the client supports)
debug2: host key algorithms: ssh-ed25519-cert-v01@openssh.com,ecdsa-sha2-nistp256-cert-v01@openssh.com,ecdsa-sha2-nistp384-cert-v01@openssh.com,ecdsa-sha2-nistp521-cert-v01@openssh.com,sk-ssh-ed25519-cert-v01@openssh.com,sk-ecdsa-sha2-nistp256-cert-v01@openssh.com,rsa-sha2-512-cert-v01@openssh.com,rsa-sha2-256-cert-v01@openssh.com,ssh-ed25519,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,sk-ssh-ed25519@openssh.com,sk-ecdsa-sha2-nistp256@openssh.com,rsa-sha2-512,rsa-sha2-256
(host key algos that the client supports)
debug2: ciphers ctos: chacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com (cipher algos the client supports)
debug2: ciphers stoc: chacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com (cipher algos the server supports
debug2: MACs ctos: umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1(Message authentication code (MAC) algos the client supports)
debug2: MACs stoc: umac-64-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-64@openssh.com,umac-128@openssh.com,hmac-sha2-256,hmac-sha2-512,hmac-sha1 (MAC algos the server supports)
debug2: compression ctos: none,zlib@openssh.com,zlib (compression algos the client supports)
debug2: compression stoc: none,zlib@openssh.com,zlib (compression algos teh server supports)
debug2: languages ctos: (languages the client supports)
debug2: languages stoc: (languages the server supports)
debug2: first_kex_follows 0 (indicates the client did not request a specific KEX algo and is waiting for the server to propose one)
debug2: reserved 0 (line is reserved for a future use, not in use right now)
debug2: peer server KEXINIT proposal (server's proposal for the KEX algo, encryption and hash algo, and other parameters)
debug2: KEX algorithms: curve25519-sha256,curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha256,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512 (KEX algos the server supports)
debug2: host key algorithms: ecdsa-sha2-nistp256,ssh-ed25519 (host key algos the server supports)
debug2: ciphers ctos: aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes128-gcm@openssh.com,aes128-ctr (cipher algos the client supports)
debug2: ciphers stoc: aes256-gcm@openssh.com,chacha20-poly1305@openssh.com,aes256-ctr,aes128-gcm@openssh.com,aes128-ctr (cipher algos the server supports)
debug2: MACs ctos: hmac-sha2-256-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha1,umac-128@openssh.com,hmac-sha2-512 (MAC the client supports)
debug2: MACs stoc: hmac-sha2-256-etm@openssh.com,hmac-sha1-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha1,umac-128@openssh.com,hmac-sha2-512 (MAV the server supports)
debug2: compression ctos: none,zlib@openssh.com (compression algo the client supports)
debug2: compression stoc: none,zlib@openssh.com (compression algos the server supports
debug2: languages ctoc: (languages the client supports)
debug2: languages stoc: (languages the server supports)
debug2: first_kex_follows 0
debug2: reserved 0
debug1: kex: algorithm: curve25519-sha256 (indicates the KEX algo selected for the connection)
debug1: kex: host key algorithm: ssh-ed25519 (indicated the host key algo selected for connection
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none (indicates the cipher, MAC, and compression algo selected for encryption of data from the server to client)
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none (indicates the cipher, MAC, and compression algo selected for encryption of data from the client to the server)
debug3: send packet: type 30 (client sent a SSH2_MSG_KEX_ECDH_INIT message)
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY (client waiting fro reply)
debug3: receive packet: type 31
debug1: SSH2_MSG_KEX_ECDH_REPLY received (client recieved a reply)
(client will record host key in known hosts file)
debug3: record_hostkey: found key type ED25519 in file /c/Users/eoyeb/.ssh/known_hosts:1
debug3: record_hostkey: found key type ECDSA in file /c/Users/eoyeb/.ssh/known_hosts:2
debug3: load_hostkeys_file: loaded 2 keys from 54.86.217.61
debug1: load_hostkeys: fopen /c/Users/eoyeb/.ssh/known_hosts2: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts: No such file or directory
debug1: load_hostkeys: fopen /etc/ssh/ssh_known_hosts2: No such file or directory
debug1: Host '54.86.217.61' is known and matches the ED25519 host key. (client authenticates that host is found in known_hosts file and the host key assocaited matches key found in known_hosts file)     
debug1: Found key in /c/Users/eoyeb/.ssh/known_hosts:1
debug3: send packet: type 21 (client is sending packet to server)
debug2: ssh_set_newkeys: mode 1 (client is indicating it is switching to a new set of keys for outbound traffic
debug1: rekey out after 134217728 blocks (indicating it will auto rekey after a certain number of blocks of outbound traffic has been sent)
debug1: SSH2_MSG_NEWKEYS sent (client is indicating that it is now using new set of keys for outbound traffic)
debug1: expecting SSH2_MSG_NEWKEYS (waiting for message from server indicating server is also using a new set of keys)
debug3: receive packet: type 21
debug1: SSH2_MSG_NEWKEYS received (recieved message)
debug2: ssh_set_newkeys: mode 0 (client is indicating it is switching to a new set of keys for inbound traffic)
debug1: rekey in after 134217728 blocks 
debug3: ssh_get_authentication_socket_path: path '/tmp/ssh-VSDj5fBAphcx/agent.2366' 
debug1: get_agent_identities: bound agent to hostkey (identity file selected for ssh)
debug1: get_agent_identities: agent returned 1 keys (only one key was found that was inputted)
debug1: Will attempt key: /c/Users/eoyeb/.ssh/id_ed25519 ED25519 SHA256:es3Gj0vx5eacw9dh0TPwg/TG7q6teMv1+wayvkkeoJ8 explicit agent (will attempt to authenticate with the specified key)
debug2: pubkey_prepare: done(client has finished preparing public key for authentication)
debug3: send packet: type 5 (client sent SSH protocol packets: SSH2_MSG_KEX_DH_GEX_REQUEST: KEX packet used in Diffie-Hellman KEX process)
debug3: receive packet: type 7 (client received SSH protocol packets: SSH2_MSG_KEX_DH_GEX_GROUP: a response to eariler request)
debug1: SSH2_MSG_EXT_INFO received (ssh has recieved extended info from the server)
debug1: kex_input_ext_info: server-sig-algs=<ssh-ed25519,sk-ssh-ed25519@openssh.com,ssh-rsa,rsa-sha2-256,rsa-sha2-512,ssh-dss,ecdsa-sha2-nistp256,ecdsa-sha2-nistp384,ecdsa-sha2-nistp521,sk-ecdsa-sha2-nistp256@openssh.com,webauthn-sk-ecdsa-sha2-nistp256@openssh.com>
debug3: receive packet: type 6
debug2: service_accept: ssh-userauth
debug1: SSH2_MSG_SERVICE_ACCEPT received
debug3: send packet: type 50
debug3: receive packet: type 51
debug1: Authentications that can continue: publickey,gssapi-keyex,gssapi-with-mic
debug3: start over, passed a different list publickey,gssapi-keyex,gssapi-with-mic
debug3: preferred publickey,keyboard-interactive,password
debug3: authmethod_lookup publickey
debug3: remaining preferred: keyboard-interactive,password
debug3: authmethod_is_enabled publickey
debug1: Next authentication method: publickey
debug1: Offering public key: /c/Users/eoyeb/.ssh/id_ed25519 ED25519 SHA256:es3Gj0vx5eacw9dh0TPwg/TG7q6teMv1+wayvkkeoJ8 explicit agent
debug3: send packet: type 50
debug2: we sent a publickey packet, wait for reply
debug3: receive packet: type 60
debug1: Server accepts key: /c/Users/eoyeb/.ssh/id_ed25519 ED25519 SHA256:es3Gj0vx5eacw9dh0TPwg/TG7q6teMv1+wayvkkeoJ8 explicit agent
debug3: sign_and_send_pubkey: using publickey with ED25519 SHA256:es3Gj0vx5eacw9dh0TPwg/TG7q6teMv1+wayvkkeoJ8
debug3: sign_and_send_pubkey: signing using ssh-ed25519 SHA256:es3Gj0vx5eacw9dh0TPwg/TG7q6teMv1+wayvkkeoJ8
debug3: send packet: type 50
debug3: receive packet: type 52
Authenticated to 54.86.217.61 ([54.86.217.61]:22) using "publickey".       
debug1: channel 0: new session [client-session] (inactive timeout: 0)      
debug3: ssh_session2_open: channel_new: 0
debug2: channel 0: send open
debug3: send packet: type 90
debug1: Requesting no-more-sessions@openssh.com
debug3: send packet: type 80
debug1: Entering interactive session.
debug1: pledge: filesystem
debug3: client_repledge: enter
debug3: receive packet: type 80
debug1: client_input_global_request: rtype hostkeys-00@openssh.com want_reply 0
debug3: client_input_hostkeys: received ECDSA key SHA256:ZitYnby0rcRADpn4X36vrFSN8nY5wPSxUVgFoWB5r5Y
debug3: client_input_hostkeys: received ED25519 key SHA256:qdhoKJzhSD2OlHThq8MifESXBu6gOw1bWMS7Gm4KaGY
debug1: client_input_hostkeys: searching /c/Users/eoyeb/.ssh/known_hosts for 54.86.217.61 / (none)
debug3: hostkeys_foreach: reading file "/c/Users/eoyeb/.ssh/known_hosts"   
debug3: hostkeys_find: found ssh-ed25519 key at /c/Users/eoyeb/.ssh/known_hosts:1
debug3: hostkeys_find: found ecdsa-sha2-nistp256 key at /c/Users/eoyeb/.ssh/known_hosts:2
debug1: client_input_hostkeys: searching /c/Users/eoyeb/.ssh/known_hosts2 for 54.86.217.61 / (none)
debug1: client_input_hostkeys: hostkeys file /c/Users/eoyeb/.ssh/known_hosts2 does not exist
debug3: client_input_hostkeys: 2 server keys: 0 new, 2 retained, 0 incomplete match. 0 to remove
debug1: client_input_hostkeys: no new or deprecated keys from server       
debug3: client_repledge: enter
debug3: receive packet: type 4
debug1: Remote: /home/excellent/.ssh/authorized_keys:1: key options: agent-forwarding port-forwarding pty user-rc x11-forwarding
debug3: receive packet: type 4
debug1: Remote: /home/excellent/.ssh/authorized_keys:1: key options: agent-forwarding port-forwarding pty user-rc x11-forwarding
debug3: receive packet: type 91
debug2: channel_input_open_confirmation: channel 0: callback start
debug3: ssh_get_authentication_socket_path: path '/tmp/ssh-VSDj5fBAphcx/agent.2366'
debug1: Requesting authentication agent forwarding.
debug2: channel 0: request auth-agent-req@openssh.com confirm 0
debug3: send packet: type 98
debug2: fd 4 setting TCP_NODELAY
debug3: set_sock_tos: set socket 4 IP_TOS 0x48
debug2: client_session2_setup: id 0
debug2: channel 0: request pty-req confirm 1
debug3: send packet: type 98
debug2: channel 0: request shell confirm 1
debug3: send packet: type 98
debug3: client_repledge: enter
debug1: pledge: agent
debug2: channel_input_open_confirmation: channel 0: callback done
debug2: channel 0: open confirm rwindow 0 rmax 32768
debug3: receive packet: type 99
debug2: channel_input_status_confirm: type 99 id 0
debug2: PTY allocation request accepted on channel 0
debug2: channel 0: rcvd adjust 2097152
debug3: receive packet: type 99
debug2: channel_input_status_confirm: type 99 id 0
debug2: shell request accepted on channel 0
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023        
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
Last login: Sat Apr 15 20:54:32 2023 from 209.194.208.149
[excellent@ip-ip-addr ~]$ 
```
