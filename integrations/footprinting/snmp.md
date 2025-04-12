# SNMP

**Simple Network Management Protocol (SNMP)** is used to **monitor and manage network devices remotely**.

**Uses UDP port 161** to exchange data and execute commands via **agents**.

**Supports "Traps" on UDP port 162**, allowing servers to send alerts to clients without prior requests.

**MIB (Management Information Base)** stores information about network-connected devices.

**OID (Object Identifier)** is a unique identifier for each object in the MIB, used for retrieving specific data.

**Traps** are notifications sent from devices to the server when critical changes occur.

**Community Strings** act as passwords in SNMPv1 and SNMPv2c but are transmitted in plaintext, making them vulnerable to interception.

***

#### **Footprinting**

involves gathering information about the SNMP service using tools like `snmpwalk`, `onesixtyone`, and `braa`.

*   **`snmpwalk`** retrieves OIDs and their associated information.

    ```bash
    snmpwalk -v2c -c public 10.129.14.128

    # -v 1|2c|3  specifies SNMP version to use
    # -c COMMUNITY     set the community string
    ```
*   **`onesixtyone`** is used for brute-forcing **Community Strings**, which administrators may customize.

    ```bash
    onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

    # output
    Scanning 1 hosts, 3220 communities
    10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
    ```
*   **`braa`** helps brute-force OIDs when a **Community String** is known.

    ```bash
    braa public@10.129.14.128:.1.3.6.*
    # braa public@10.129.14.128:.1.3.6.*

    #ouput
    0.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
    10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
    10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
    ```

If SNMP is misconfigured and doesn’t require authentication (versions 1 & 2c), internal system details like installed packages and hostnames can be retrieved.

If the **Community String** is unknown, `onesixtyone` with SecLists wordlists can help find it. Once found, `braa` can be used to explore OIDs more efficiently.

Setting up an SNMP-enabled VM for hands-on practice is recommended, as SNMP can be both a powerful tool for IT admins and a security risk if improperly configured.

***

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-18 163447.png" alt=""><figcaption></figcaption></figure>
