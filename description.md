# CSC 249 – Project 3 Secure RPC - Description

1. Overview of Application
This project implements a secure Remote Procedure Call (RPC) communication system. The application builds a simulated secure client and server, allowing client to communicate with server through a VPN. It uses a simulated TLS handshake to ensure the confidentiality and correctness of messages. A certificate authority issues and verifies server certificates. The application operates on TCP sockets, supporting messages of up to 1000 bytes.

2. Format of an Unsigned Certificate
An unsigned certificate should contain the following components:

* The server's public key, represented as a tuple of (modulus, exponent), such as (36257, 56533).
* The server's IP and port, in the form of IP|PORT, such as 127.0.0.1|65432.
* issuer: The certificate authority's public key, such as (7525, 56533).
Example of an unsigned certificate:
'(36257, 56533)|127.0.0.1|65432'

3. Example Output
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

4. Walkthrough of the TLS Handshake
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

5. Possible Failure or Exploits
* In the this simulation, only the server's certificate is authenticated. Client identity is not proved or authenticated. Thus, it may lead to communicating with wrong clients. A malicious third party could impersonate the client and gain unauthorized access to the server leading to the leak of information. Maybe adding client certificates or a mutual authentication can improve security.
* The use of eval() function for processing messages can lead to a security risk. If the input is not sanitized properly, a malicious party could implement harmful code, thus lead to network attack.
This allows injection attacks and the attacker could execute untrusted code on users.

6. Acknowledgements
I discussed with Zhirou Liu, Quinn He, and Isabelle Wang on this project.

