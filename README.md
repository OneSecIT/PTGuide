# Welcome to your organization's demo respository
## Checking Port <br>
#### Powershell <br>
eg: Test-NetConnection -ComputerName target_ip -Port 80 -InformationLevel "Detailed" <br>
https://docs.microsoft.com/en-us/powershell/module/nettcpip/test-netconnection?view=win10-ps <br>

#### Bulk scanning powershell <br>
 1..10 | % {"192.168.0.$_"} | ForEach-Object { Test-Connection -Count 1 -ComputerName $_ 2>$null} <br>


## XSS Payload <br>
https://portswigger.net/web-security/cross-site-scripting/cheat-sheet <br>
## Source Code Review Rule <br>
https://rules.sonarsource.com/php/RSPEC-5335 <br>

## FTP Check (TCP 21) <br>
#### FTP Anonymous Login<br>
#### Tools <br>
nmap -sSV --script ftp-anon.nse -p21 IP -T4 --reason <br>

#### Solution <br>
1. Disable FTP and use SSH <br>
2. Disable FTP Anonymous Login <br>
https://antnix07.blogspot.com/2018/01/disable-anonymous-user-in-ftp-server-on.html <br>
<hr>

## DNS Check (TCP 53) <br>
#### DNS Server Cache Snooping Remote Information Disclosure<br>
#### Tools <br>
nmap -sU -p 53 --script dns-cache-snoop <ip_address> <br>

#### Solution <br>
1. Leave recursion enabled if the DNS Server resides on a corporate network that cannot be reached by untrusted clients <br>
2. Do not allow public access to DNS Servers performing recursion <br>
3. Disable recursion <br>
https://support.microsoft.com/en-us/help/2678371/microsoft-dns-server-vulnerability-to-dns-server-cache-snooping-attack
<hr>

#### DNS Server Spoofed Request Amplification DDoS<br>
#### Tools <br>
msf > use auxiliary/scanner/dns/dns_amp <br>

#### Solution <br>
1. Restrict access to your DNS server from public network or reconfigure it to reject such queries
<hr>

#### DNS Server Recursive Query Cache Poisoning Weakness<br>
#### Tools <br>
nmap -Pn -sU -p 53 --script=dns-recursion <ip_address> <br>

#### Solution <br>
1. Restrict recursive queries to the hosts that should use this nameserver (such as those of the LAN connected to it) <br>
2. If bind 8 is in use, use the instruction 'allow-recursion' in the 'options' section of the named.conf <br>
3. If bind 9 is in use, define a grouping of internal addresses using the 'acl' command <br>
Then, within the options block, you can explicitly state:  <br>
'allow-recursion { hosts_defined_in_acl }' <br>
If another name server is in use, consult its documentation. <br>
<hr>

#### DNS Server Zone Transfer Information Disclosure (AXFR)<br>
#### Tools <br>
dig axfr @<ip_address> <domain.name> <br>

#### Solution <br>
N/A<br>
<hr>

#### Domain misconfiguration scanner <br>
https://mxtoolbox.com/domain/<br>

## Http Testing Server <br>
#openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes<br>
import http.server, ssl<br>

server_address = ('192.168.0.5', 443)<br>
httpd = http.server.HTTPServer(server_address, http.server.SimpleHTTPRequestHandler)<br>
httpd.socket = ssl.wrap_socket(httpd.socket,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server_side=True,<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; certfile='server.pem',<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ssl_version=ssl.PROTOCOL_TLSv1)<br>
httpd.serve_forever()<br>
## Header Check (TCP 80 and 443)<br>
#### Tools <br>
nmap -sV -T5 -Pn -p 80 --script http-headers IPs <br>
https://github.com/koenbuyens/securityheaders <br>
#### Solution <br>
http://securityheaders.io/ <br>
#### Link <br>
##### Content Security Policy Explaination <br>
https://csp.withgoogle.com/docs/index.html <br>
#### Nginx Security Headers <br>
##### Harderning <br>
##### Solution <br>
https://www.attosol.com/http-security-headers-with-nginx/ <br>
#### Remove IIS Server version HTTP Response Header (TCP 80) <br>
##### Solution <br>
https://www.saotn.org/remove-iis-server-version-http-response-header/<br>
https://blogs.msdn.microsoft.com/varunm/2013/04/23/remove-unwanted-http-response-headers/ <br>
#### Etag Headers <br>
What benefit using etag? <br>
The ETag HTTP response header is an identifier for a specific version of a resource. It lets caches be more efficient and save bandwidth, as a web server does not need to resend a full response if the content has not changed. Additionally, etags help prevent simultaneous updates of a resource from overwriting each other <br>
#### link <br>
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag <br>
#### misconfiguration of etag <br>
Sometimes ETag header providing sensitive information that could aid an attacker, such as the inode number <br>
#### link <br>
https://www.tenable.com/plugins/nessus/88098<br>
https://www.securityfocus.com/bid/6939/discuss<br>
#### What the use of inode number? <br>
https://tecadmin.net/what-is-inode-number-in-linux/ <br>
#### Solution <br>
IIS : https://www.saotn.org/remove-etags-http-header-iis/ <br>
<hr>

## HTTP Method Check (TCP 80 and 443) <br>
nmap -sV -T5 -Pn -p 80 --script http-headers IPs -v <br>
#### Solutions <br>
https://hostadvice.com/how-to/how-to-disable-the-vulnerability-of-options-method-in-iis-and-apache/ <br>

## SNMP (TCP/UDP 161) <br>
#### SNMP Enum<br>
#### Tools <br>
nmap -sV -p161 --script snmp-info IP --reason -T4 <br>
nmap -sU -p161 --script snmp-info IP --reason -T4 <br>
snmpwalk -c public/private/any -v1/2c IP <br>

#### Solution <br>
N/A <br>
<hr>

#### SNMP Agent Default Community Name (public)<br>
#### Tools <br>
onesixtyone <ip address> -c /usr/share/doc/onesixtyone/dict.txt <br>

#### Solution <br>
Change the default community string “public” to something complex <br>
<hr>

#### SNMP 'GETBULK' Reflection DDoS<br>
#### Tools <br>
snmpbulkget -v2c -Cn0 -Cr2500 -Os -c public <ip address> 1.3.6.1.2.1 <br>

#### Solution <br>
Restrict and monitor access to this service, and consider changing the default 'public' community string <br>
<hr>

## SSL Check (TCP 443) <br> 
1. Check for cipher strength 
nmap -sSCV -Pn --script ssl-enum-ciphers -p443 IP<br>
sslscan IP <br>
#### Tools<br> 
https://github.com/rbsec/sslscan<br>
https://github.com/nabla-c0d3/sslyze<br>
https://testssl.sh/<br>
#### Solution<br>
Windows: https://www.nartac.com/Products/IISCrypto/ <br>
https://www.phr33fall.co.uk/ssl-tls-issues-server-2012/ (Manual Solution)<br>
<hr>

## SMBv1 Check (TCP 445) <br>
#### Smb protocols <br>
nmap -sV -T5 -Pn -p 445 --script smb-protocols IPs<br>
#### Solution <br>
https://support.microsoft.com/en-us/help/2696547/detect-enable-disable-smbv1-smbv2-smbv3-in-windows-and-windows-server<br>

#### Smb Singing <br>
nmap -sV -T5 -Pn -p 445 --script smb-security-mode IPs <br>
#### Solution <br>
https://support.microsoft.com/en-my/help/161372/how-to-enable-smb-signing-in-windows-nt <br>
<hr>

#### MS17-010 Microsoft Windows SMBv1 Multiple Vulnerabilities (MS17-010) <br>
nmap -p445 --script smb-vuln-ms17-010 <ip address> <br>
#### Solution <br>
Microsoft has released a set of patches for Windows Vista, 2008, 7, 2008 R2, 2012, 8.1, RT 8.1, 2012 R2, 10, and 2016. Microsoft has also released emergency patches for Windows operating systems that are no longer supported, including Windows XP, 2003, and 8.   <br>
<hr>

#### Microsoft Windows SMB NULL Session Authentication <br>
smbclient -L <ip_address> <br>
#### Solution <br>
Disable the SMB service if not required. <br>
<hr>

## RDP Check (TCP 3389) <br>
#### NLA,FIPS,ENC <br>
Network Level Authentication (NLA) Disabled<br>
Terminal Services Encryption Level is Medium or Low, or<br>
Terminal Services Encryption Level is not FIPS-140 Compliant<br>
#### Tools <br>
https://github.com/portcullislabs/rdp-sec-check<br>
#### Solution <br>
videos:https://www.youtube.com/watch?v=nyBOJwvUaKQ<br>
Link:https://www.phr33fall.co.uk/windows-rdp-hardening/<br>
<hr>


## MSSQL Check (TCP 1433) <br>
#### Check TCP port if assign not default <br>
https://www.mssqltips.com/sqlservertip/2495/identify-sql-server-tcp-ip-port-being-used/ <br>
https://www.sqlshack.com/overview-of-sql-server-ports/ <br>
<hr>
