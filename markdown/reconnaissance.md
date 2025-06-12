# Reconnaissance

*   <mark style="color:red;">**Subdomain enumeration**</mark>

    Subfinder

    ```bash
    # List
    subfinder -dL domains.txt -o subs.com 
    # domain
    subfinder -d test.com -o subs.com 
    ```

    Amass

    ```bash
    # passive
    └─$ amass enum --passive -norecursive -noalts -d example.com -o example.com.subs

    # Lsit 
    └─$ amass enum --passive -norecursive -noalts -df domains.txt-o example.com.subs

    # active
    └─$ amass enum  -src -ip -brute -min-for-recursive 2 -d example.com -o example.com.subs
    ```

    Assetfinder

    ```bash
    └─$ assetfinder [--subs-only] <domain>
    ```

    SubEnum

    ```bash
    # Basic Usage
    └─$ subenum -d target.com

    #Agains List Of Domains
    └─$ subenum -l domains.txt -r
    ```

    TheHarvester

    ```bash
    └─$ theHarvester -d cisco.com -b all 
    ```

    CRT

    ```bash
    # use to filter unique subdomain
    curl -s <https://crt.sh/\\?q\\=inlanefreight.com\\&output\\=json> | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\\\n/,"\\n");}1;' | sort -u
    ```

    [https://securitytrails.com/app/account](https://securitytrails.com/app/account)

    [https://subdomainfinder.c99.nl/](https://subdomainfinder.c99.nl/)

    [https://shrewdeye.app/](https://shrewdeye.app/)

    google dorking

    unique subdomain

    ```bash
    cat subdomains.txt | anew >> unique_subdomains.txt
    ```
*   <mark style="color:red;">**Subdomain Takeover**</mark>

    Subzy

    ```bash
    subzy run --targets unique_subdomains.txt --vuln --hide_fails
    ```

    dig

    ```bash
    dig +noall +answer @8.8.8.8 target.com CNAME
    ```

    nslookup

    ```bash
    nslookup taget.com
    ```
*   <mark style="color:red;">**Live subdomain**</mark>

    Httpx all thing about targets

    <pre class="language-bash"><code class="lang-bash"><strong>cat subs.txt | httpx -sc -title -ip -cname -method -o httpx.txt
    </strong></code></pre>

    Httpx 200 status code

    ```bash
    cat subs.txt | httpx -fc 403,401,302,301,404 -o 200.txt
    ```

    httpx all ips and ports on host

    ```bash
    cat subs.txt | httpx -pa -p -o ips.txt
    ```
*   <mark style="color:red;">**port scanning**</mark>

    ```bash
    # part of ports
    └─$ naabu -list subs.txt -top-ports 1000 -exclude-ports 80,443,21,22,25 -o ports.txt

    # all ports
    └─$ naabu -list subs.txt -p- -exclude-ports 80,443,21,22,25 -o ports.txt

    sudo unicornscan -ImT ip_add:1-2000
    ```

    using Shodan

    ```bash
    # extract IPs
    for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
    ```

    ```bash
    # search in shodan using IPs
    for i in $(cat ip-addresses.txt);do shodan host $i;done

    ##to add API key
    shodan init API_KEY

    ```
*   <mark style="color:red;">**endpoint**</mark>

    waybackurl

    ```bash
    cat 200.txt | waybackurls | tee -a way.txt
    ```

    Katana

    ```bash
    katana -list 200.txt -o katana.txt 
    ```

    gospider

    ```bash
    gospider -s httpx -o gospider_output
    ```

    unique

    ```bash
    cat *.txt | anew >> endpoint.txt
    ```
*   <mark style="color:red;">**auto scan**</mark>

    ```jsx
    nuclei -l live_subs_domain.com.txt -rl 10 -bs 2 -c 2 -as -silent -s critical,high,medium
    ```
*   <mark style="color:red;">**Information about target**</mark>

    Shodan

    [https://github.com/lothos612/shodan](https://github.com/lothos612/shodan)

    ```bash
    dig +noall +answer google.com NS # to get the DNS server
    dig +noall +answer google.com TXT # Some records may include verification keys for third-party services (e.g., Google, Microsoft).
    dig +noall +answer microsoft.com A # to get ipv4 address
    dig +noall +answer microsoft.com MX # mail server
    dig +noall +answer microsoft.com CNAME # alias names
    dig +noall +answer microsoft.com AAAA # ipv6 
    dig +noall +answer microsoft.com ANY # to get all of the above
    dig +noall +answer @spacific_DNS_Server microsoft.com ANY # to search in specific dns server

    ```

    ```bash
    # extract IPs
    ## for search each service is locally or upload on cloud
    for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
    ```

    ```bash
    # search in shodan using IPs
    for i in $(cat ip-addresses.txt);do shodan host $i;done

    ##to add API key
    shodan init API_KEY

    ```

    use for search on file : [https://buckets.grayhatwarfare.com/](https://buckets.grayhatwarfare.com/)

    Censys

    [https://search.censys.io/](https://search.censys.io/)
*   <mark style="color:red;">**Directory**</mark>

    dirsearch

    ```bash
    dirsearch -l 200.txt --full-url --crawl -o dir.txt
    ```

    dirsearch files

    ```bash
    dirsearch -l 200.txt -o dirsearch.txt -i 200  -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,aspx~,asp~,py,py~,rb,rb~,php,php~,bak,bkp,cache,cgi,conf,csv,html,inc,jar,js,json,jsp,jsp~,lock,log,rar,old,sql,sql.gz,<http://sql.zip>,sql.tar.gz,sql~,swp,swp~,tar,tar.bz2,tar.gz,txt,wadl,zip,.log,.xml,.js.,.json
    ```
*   <mark style="color:red;">**Parameter**</mark>

    **ParamMiner**

    for hidden parameter

    ```
    arjun -u <https://example.com/page>
    ```
*   <mark style="color:red;">**JS files**</mark>

    waybackurls

    ```bash
    cat way.txt | grep -e "\\.js" | tee -a jsinv.txt
    ```

    ```bash
    # check, if they are actually available
    cat js-urls.txt | parallel -j50 -q curl -w 'Status:%{http_code}\\t Size:%{size_download}\\t %{url_effective}\\n' -o /dev/null -sk | grep Status:200
    ```
*   <mark style="color:red;">**API leak**</mark>

    mantra

    ```bash
    cat js.txt | mantra
    ```

    nuclei

    ```bash
    nuclei -l js.txt -t /nuclie-temlates/http/exposure -o nuc.txt
    ```
*   <mark style="color:red;">**NMAP**</mark>

    ```bash
    # nmap -iL allsubs.txt -o nmap.txt
    after finding open port as 22,21,25,111,139,445,etc...
    # nmap <ip> -sV
    then search for exploit in google

    to see the scripts of nmap 
    # cd /usr/share/nmap/scripts
    # ls 
    to grep only scripts related to ssh or anything else
    # ls | grep ssh
    to use all scripts related to ssh
    # nmap 192.168.1.1 --scripts=ssh*
    to use specific script for exmaple ssh-brute.nse
    # nmap 192.168.1.1 --script=ssh-brute.nse

    to use all vulnerable scripts to check for vulnerabilities 
    # nmap 192.168.1.1 --script=vuln
    # nmap 192.168.1.1 --script=exploit

    to bypass the firewall 
    # nmap -sS -Pn -n 192.168.1.1 

    to use fragment mode to bypass the firewall 
    # nmap -f 192.168.1.1

    ```
*   <mark style="color:red;">**Http request smuggling**</mark>

    ```bash
    1- use smuggler to check request smuggling vulnerablitiy 
    # cat httpx.txt | smuggler.py | tee -a smuggler.txt
    ```
*   <mark style="color:red;">**Parameters**</mark>

    extract PHP file

    ```bash
    cat allurls.txt | grep -E "\\.php" >> php.txt
    ```

    Arjun

    ```bash
    arjun -i php.txt >> param.txt
    ```

    paramspider

    ```bash
    paramspider -l php.txt >> param.txt
    ```

    unique

    ```bash
    cat param.txt | anew >> uniparam.txt
    ```


* <mark style="color:red;">**OSINT Framework**</mark>
* <mark style="color:red;">**Shodan**</mark>
*   <mark style="color:red;">**Crunch base**</mark>

    information about target like Acquisition, contact info ,emails, etc...
*   <mark style="color:red;">**Whois**</mark>

    information about target may use in subdomain takeover
*   <mark style="color:red;">**403/unauthorized**</mark>

    bypass it by `~/403bypass]`
