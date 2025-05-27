# Authentication Process

### Active Directory Authentication Process (Kerberos-based)

### 1. User Logon

* User enters credentials (username + password).
* Example: `user@domain.local`.

### 2. AS Request (Authentication Service)

* The client sends a request to the **KDC (Key Distribution Center)** to get a **TGT (Ticket Granting Ticket)**.
* The KDC resides on the Domain Controller.

### 3. Verification & TGT Issuance

* The KDC validates the user's credentials.
* If correct, it returns a **TGT**, encrypted with the KDC’s secret key.

### 4. TGS Request (Ticket Granting Service)

* The client uses the TGT to request access to a specific service (e.g., file share).
* It sends the TGT to the **TGS** component of the KDC.

### 5. Service Ticket Issuance

* The TGS verifies the TGT.
* If valid, it issues a **Service Ticket** for the requested service.

### 6. Service Access

* The client presents the **Service Ticket** to the target service (e.g., File Server).
* If valid and authorized, access is granted.

![image.png](<../../../.gitbook/assets/image (20).png>)
