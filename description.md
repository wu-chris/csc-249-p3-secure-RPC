# CSC 249 – Project 3 Secure RPC - Description

## Overview of Application
This project implements a secure Remote Procedure Call (RPC) communication system. The application builds a simulated secure client and server, allowing client to communicate with server through a VPN. It uses a simulated TLS handshake to ensure the confidentiality and correctness of messages. A certificate authority issues and verifies server certificates. The application operates on TCP sockets, supporting messages of up to 1000 bytes.

## Format of an Unsigned Certificate
An unsigned certificate should contain the following components:

* The server's public key, represented as a tuple of (modulus, exponent), such as (36257, 56533).
* The server's IP and port, in the form of IP|PORT, such as 127.0.0.1|65432.
* issuer: The certificate authority's public key, such as (7525, 56533).
Example of an unsigned certificate:
'(36257, 56533)|127.0.0.1|65432'

## Example Output
certificate authority input:
Received client message: 'b'$(36257, 56533)|127.0.0.1|65432'' [31 bytes]

client input: 
--server_IP 127.0.0.1 --server_port 65432 --VPN_IP 127.0.0.1 --VPN_port 55554 --CA_IP 127.0.0.1 --CA_port 55553 --message Hello, Secure Server!

VPN input:
Received client message: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes], forwarding to server

VPN output:
Received server response: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes], forwarding to client

response output:
Received raw response: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes]
Decoded message 'Hello, Secure Server!' from server

## Walkthrough of the TLS Handshake
Client Request:
The client initiates the handshake by requesting the server’s certificate from the certificate authority.

Certificate Validation:
The certificate authority sign the server's certificate, ensuring it is valid and can be trust.

Server's Signed Certificate:
The client receives the signed certificate and validates it using the certificate authority's public key.

Symmetric Key Exchange:
The client generates a symmetric key for encryption and encrypts it using the server's public key.
The server decrypts the symmetric key using its private key.

Handshake Completion:
The server acknowledges the receipt of the symmetric key.
Now, the handshake is completed, and the session is secured.

Message Communication:
The client and server exchange encrypted messages using the established symmetric key.

## Possible Failure or Exploits
* In the this simulation, only the server's certificate is authenticated. Client identity is not proved or authenticated. Thus, it may lead to communicating with wrong clients. A malicious third party could impersonate the client and gain unauthorized access to the server leading to the leak of information. Maybe adding client certificates or a mutual authentication can improve security.
* The use of eval() function for processing messages can lead to a security risk. If the input is not sanitized properly, a malicious party could implement harmful code, thus lead to network attack.
This allows injection attacks and the attacker could execute untrusted code on users.

## Acknowledgements
I discussed with Zhirou Liu, Quinn He, and Isabelle Wang on this project.

## Command-line traces
### Certificate Authority Trace
python3 certificate_authority.py
Certificate Authority started using public key '(7525, 56533)' and private key '49008'
Certificate authority starting - listening for connections at IP 127.0.0.1 and port 55553
Connected established with ('127.0.0.1', 60712)
Received client message: 'b'$(36257, 56533)|127.0.0.1|65432'' [31 bytes]
Signing '(36257, 56533)|127.0.0.1|65432' and returning it to the client.
Received client message: 'b'done'' [4 bytes]
('127.0.0.1', 60712) has closed the remote connection - listening 
Connected established with ('127.0.0.1', 60713)
Received client message: 'b'key'' [3 bytes]
Sending the certificate authority's public key (7525, 56533) to the client
Received client message: 'b'done'' [4 bytes]

### Server Trace
python3 secure_server.py
Generated public key '(36257, 56533)' and private key '20276'
Connecting to the certificate authority at IP 127.0.0.1 and port 55553
Prepared the formatted unsigned certificate '(36257, 56533)|127.0.0.1|65432'
Connection established, sending certificate '(36257, 56533)|127.0.0.1|65432' to the certificate authority to be signed
Received signed certificate 'D_(49008, 56533)[(36257, 56533)|127.0.0.1|65432]' from the certificate authority
server starting - listening for connections at IP 127.0.0.1 and port 65432
Connected established with ('127.0.0.1', 60715)
Received handshake request: TLS_HANDSHAKE_INIT
Sending signed certificate 'D_(49008, 56533)[(36257, 56533)|127.0.0.1|65432]' to the client
Received encrypted symmetric key 'E_(36257, 56533)[52859]' from the client
Decrypted symmetric key: 52859
TLS handshake complete: established symmetric key '52859', acknowledging to client
Received client message: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes]
Decoded message 'Hello, Secure Server!' from client
Responding 'Hello, Secure Server!' to the client
Sending encoded response 'HMAC_32162[symmetric_52859[Hello, Secure Server!]]' back to the client
server is done!
### VPN Trace
python3 VPN.py
VPN starting - listening for connections at IP 127.0.0.1 and port 55554
Connected established with ('127.0.0.1', 60714)
Received client message: 'b'127.0.0.1~IP~65432~port~TLS_HANDSHAKE_INIT'' [42 bytes]
connecting to server at IP 127.0.0.1 and port 65432
server connection established, sending message 'TLS_HANDSHAKE_INIT'
message sent to server, waiting for reply
Received server response: 'b'D_(49008, 56533)[(36257, 56533)|127.0.0.1|65432]'' [48 bytes], forwarding to client
Received client message: 'b'E_(36257, 56533)[52859]'' [23 bytes], forwarding to server
Received server response: 'b"symmetric_52859[Symmetric key '52859' received]"' [47 bytes], forwarding to client
Received client message: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes], forwarding to server
Received server response: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes], forwarding to client
VPN is done!
### Client Trace
python3 secure_client.py --server_IP 127.0.0.1 --server_port 65432 --VPN_IP 127.0.0.1 --VPN_port 55554 --CA_IP 127.0.0.1 --CA_port 55553 --message Hello, Secure Server!
Connecting to the certificate authority at IP 127.0.0.1 and port 55553
Connection established, requesting public key
Received public key (7525, 56533) from the certificate authority for verifying certificates
Client starting - connecting to VPN at IP 127.0.0.1 and port 55554
Received signed certificate: D_(49008, 56533)[(36257, 56533)|127.0.0.1|65432]
TLS handshake complete: sent symmetric key '52859', waiting for acknowledgement
Received acknowledgement 'Symmetric key '52859' received', preparing to send message
Sending message 'HMAC_32162[symmetric_52859[Hello, Secure Server!]]' to the server
Message sent, waiting for reply
Received raw response: 'b'HMAC_32162[symmetric_52859[Hello, Secure Server!]]'' [50 bytes]
Decoded message 'Hello, Secure Server!' from server
client is done!
