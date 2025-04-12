# Introduction to Attacking Common Applications

CMS → control management system which responsible to manage content of application like (WordPress, Jomlaa, Drupal)

* Web applications are everywhere and are a key target for penetration testers.
* Common applications include CMSs, intranet portals, code repositories, network monitoring tools, ticketing systems, and more.
* The same applications can be found in different environments, with some being misconfigured or outdated, making them vulnerable.
* Understanding how to enumerate and attack these applications is essential.

#### **Web Applications & Their Structure**

* Web applications follow a **client-server architecture**:
  * **Front-end (client-side):** The user interface, runs in the browser.
  * **Back-end (server-side):** The application logic, database, and processing.
* Vulnerabilities exist in all types of web applications:
  * **Commercial, open-source, and custom applications.**
  * Common risks align with the **OWASP Top 10** (SQLi, XSS, RCE, etc.).
  * Attackers also exploit built-in functionality, not just known vulnerabilities.

#### **Why Web Applications Are a Key Target?**

* Companies **harden** their external perimeter, making web apps a primary attack vector.
* The shift to **remote work** has led to more applications being exposed online.
* Web applications can provide:
  * A **foothold into internal networks** during an external assessment.
  * Lateral movement opportunities during an internal assessment.

#### **Statistics on Application Security (2021 Survey by Barracuda)**

* **72%** of companies suffered at least one breach due to an application vulnerability.
* **32%** had two breaches, and **14%** had three breaches.
* Main challenges faced:
  * **Bot attacks (43%)**
  * **Software supply chain attacks (39%)**
  * **Vulnerability detection (38%)**
  * **Securing APIs (37%)**

***

#### **Commonly Encountered Applications in Penetration Tests**

| **Category**                                       | **Examples**                                                     |
| -------------------------------------------------- | ---------------------------------------------------------------- |
| **Web Content Management**                         | Joomla, Drupal, WordPress, DotNetNuke                            |
| **Application Servers**                            | Apache Tomcat, Phusion Passenger, Oracle WebLogic, IBM WebSphere |
| **Security Information & Event Management (SIEM)** | Splunk, Trustwave, LogRhythm                                     |
| **Network Management**                             | PRTG Network Monitor, ManageEngine OpManager                     |
| **IT Management**                                  | Nagios, Puppet, Zabbix, ManageEngine ServiceDesk Plus            |
| **Software Frameworks**                            | JBoss, Axis2                                                     |
| **Customer Service Management**                    | osTicket, Zendesk                                                |
| **Search Engines**                                 | Elasticsearch, Apache Solr                                       |
| **Software Configuration Management**              | Atlassian JIRA, GitHub, GitLab, Bugzilla, Bugsnag, Bitbucket     |
| **Software Development Tools**                     | Jenkins, Atlassian Confluence, phpMyAdmin                        |
| **Enterprise Application Integration**             | Oracle Fusion Middleware, BizTalk Server, Apache ActiveMQ        |

* Many of these applications have **known exploits** or **abusable functionalities** that allow attackers to:
  * Gain **remote code execution (RCE)**.
  * Steal **credentials**.
  * Access **sensitive data** even without authentication.

***

#### **Understanding How to Attack Applications**

* Instead of just exploiting known vulnerabilities, testers must understand:
  * How the **application works**.
  * Why **misconfigurations** exist.
  * How **built-in functionality** can be abused.
* A penetration tester should approach applications with a **critical mindset** to identify new attack vectors.

#### **Example Attack Scenario (Sonatype Nexus Repository OSS)**

1. **External penetration test** → Encountered **Nexus Repository OSS**.
2. **Default admin credentials (admin:admin123) were unchanged** → Gained admin access.
3. **Exploited API functionality** to achieve **RCE**.
4. **Encountered the same application later** but this time:
   * **Abused the "Tasks" functionality** to execute a **Groovy script** and gain **RCE**.
5. **Lesson:** Applications should never be overlooked as they might be the only way "in."

***

#### **Deep Dive Into Key Applications**

#### **1. WordPress**

* **Open-source CMS** used for blogs, forums, and business websites.
* Highly **customizable** but prone to **vulnerabilities in third-party plugins & themes**.
* Typically runs on **Apache with MySQL backend**.

#### **2. Drupal**

* Another **popular open-source CMS** written in PHP.
* Uses **MySQL, PostgreSQL, or SQLite** as the backend.
* Supports **themes and modules**, making it customizable but also vulnerable.

#### **3. Joomla**

* **PHP-based CMS** with support for MySQL, PostgreSQL, or SQLite.
* Used for **e-commerce, forums, blogs, etc.**
* **Third most used CMS** after WordPress and Shopify.

#### **4. Apache Tomcat**

* **Java-based web server** designed for running Java Servlets & JSP scripts.
* Used by many **Java frameworks like Spring**.
* Often misconfigured, leading to **exploitable admin panels**.

#### **5. Jenkins**

* **Automation server** for software development.
* **Runs on Tomcat** and allows **continuous integration/deployment**.
* Has **RCE vulnerabilities**, some of which do not require authentication.

#### **6. Splunk**

* **Log analytics tool**, often used for security monitoring.
* Contains **sensitive logs & security data**, making it a valuable target.
* Not many public exploits exist, but older versions had **RCE vulnerabilities**.

#### **7. PRTG Network Monitor**

* **Network monitoring system** for uptime/bandwidth tracking.
* Uses **ICMP, WMI, SNMP, NetFlow** to gather data.
* **Written in Delphi**, with known exploits allowing **RCE**.

#### **8. osTicket**

* **Open-source ticketing system** for customer support.
* Written in **PHP** and runs on **Apache/IIS with MySQL**.

#### **9. GitLab**

* **Git repository manager** with built-in CI/CD, issue tracking, and code review.
* Written in **Ruby on Rails, Go, and Vue.js**.
* Both **community (free)** and **enterprise (paid)** versions exist.

***

#### **Setting Up Targets for Testing**

* Web applications use **Vhosts** (virtual hosts) to run multiple apps on one machine.
* To interact with test environments, **update the /etc/hosts file** on the attacker’s machine:

```bash
IP=10.129.42.195
printf "%s\\t%s\\n\\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```

* After running this command, **/etc/hosts file** should contain:

```
10.129.42.195    app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local
```

* If a spawned target **isn't accessible via IP**, **check the hosts file** and update it if needed.

***

#### **Key Takeaways**

* Web applications are **major attack vectors** for penetration testers.
* Many applications share **common vulnerabilities & misconfigurations**.
* Attackers exploit **both public exploits & built-in functionalities**.
* Understanding application behavior is **crucial** for identifying attack paths.
* Applications should **never be overlooked**, as they might be the only way into a well-secured environment.
