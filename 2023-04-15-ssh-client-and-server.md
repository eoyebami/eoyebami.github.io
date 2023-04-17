<h2>OpenSSH Server & Client Configuration</h2>

OpenSSH forms a server connection between the Remote server and the SSH client (default port: 22)
Server has a public key (Specific User has the key)
Client has the private key 

<h2>What happens during this ssh connection?</h2>
  1. Client sends a connection request (via a TCP SYN packet) to the server on port 22 (default) and sends its public key as part of the authentication process
  2. Server responds to the request (with a SYN-ACK packet, client responds with a ACK-packet {3-way handshake})
    a. server and client negotiate the ssh protocol version and identification strings 
    b. server sends the public host key to the client as part of the ssh key exchange process, the client generates a hash (typically sha256 cryptographic function; which can be a sha-256 or md5, and this hash becomes the fingerprint.) of the host public key to be verified by the user (then the host public key will be save into the user's known_hosts file for future connections, which will be automatic because the client will recognize the host).     
    c. the client generates a key pair consisting of the public key and private key (ssh-keygen). the public key is sent to the server. along with a request to authenticate a specific user account within the server.
    d. the server retrieves the public key of the specific user from its authorized_keys file and then generates a randon challenge message (a sequence of bytes). 
    e. this challenge message is sent to the client as a SSH_MSG_USERAUTH_REQUEST message, along with a request for the client to sign the message with its private key
  3. The client signs the message with its private key and sends it back to the server as a SSH_MSG_USERAUTH_RESPONSE message.
  4. The server verifies the signature with the clients public key (that it received earlier, to verify the public key matches the private ket signature) and if it is valid, the connection is established
This is done via a cryptographic authentication, a handshake is down. The connection is encrypted and the crypted keys will verfied between the public and private keys
If they match, connection is continued, and if the do not match, then the server will immediately terminate the connection for failed authentication.  

<h2>What is a fingerprint?</h2>

The fingerprint of a host key is the hash of the ssh host key of a remote server itself, which is a short, human-readable string that can be used to verify the authenticitiy of the host key. 

A fingerprint is based on the host' public key, used to verify the host you are connecting to.
If the fingerprint changes, the machine you are connecting to has changed their public key, or you could be connecting to a different machine with the same domain/ip.
When you answer yes, the fingerprint will be stored within the .ssh/known_hosts. Once this key is stored in the known_hosts file, then the client system can connect to the server automatically, without needing any approvals because the host key will authenticate the connection. 

<h2>What is a hash?</h2>
A mathematical function that takes input data of arbitrary size and prodcues a fixed-size ouput, which is usually a sequence of characters or digits. Also known as a "hash value" or "message digest".

Hash functions are deterministics, meaning if given the same imput data, they will always produce the same output data (similar to a base64 encode). Which makes its good for checking and authentication purposes (you can verify its the right key because the hash value will always be the same). 














