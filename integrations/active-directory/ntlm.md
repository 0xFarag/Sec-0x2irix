# NTLM

### NTLM (NT LAN Manager)

* Legacy authentication protocol.
* Used when:
  * The machine is not part of a domain **(Local Machine)**
  * Kerberos fails.
* Challenge-response mechanism.
* Vulnerable to attacks:
  * Pass-the-Hash
  * NTLM Relay
  * Overpass-the-Hash

> **type of hash for SAM File is NTLM Hash**
