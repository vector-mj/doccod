---
comments: true
---
# CURL

![curl](../../assets/curl.jpg){width=700}

##  cURL ~ Client URL
cURL is a computer software project providing a library and command-line tool for transferring data using various network protocols.


## 🌱 Commands
```sh title="🟢 Save the Output to a File"
curl -o react.js https://test.net/react.js
curl -O https://test.net/react.js
```

```sh title="🟢 Resume a Download"
curl -C - -O https://test.net/react.js
```

```sh title="🟢 Get the HTTP Headers of a URL"
curl -I https://test.net/react.js
```
```sh title="🟢 Test if a Website Supports HTTP/2"
curl -I --http2 https://test.net/react.js
```
```sh title="🟢 Follow Redirects"
curl -L https://test.net/react.js
```
```sh title="🟢 Change User-Agent"
curl -A "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0" https://test.net/react.js
```
```sh title="🟢 Specify a Maximum Transfer Rate"
curl --limit-rate 1m -O https://test.net/react.js
```
```sh title="🟢 FTP"
curl -u FTP_USERNAME:FTP_PASSWORD ftp://ftp.example.com/
curl -T newfile.tar.gz -u FTP_USERNAME:FTP_PASSWORD ftp://ftp.example.com/
```
```sh title="🟢 Send Cookie"
curl -b "oraclelicense=a" https://test.net/react.js
```
```sh title="🟢 Using Proxies"
curl -U username:password -x 192.168.44.1:8888 https://test.net/react.js
```
```sh title="🟢 Multiple Url"
curl http://url.com/archive[2010-2020]/vol[1-4]/part{a,b,c}.html
```
```sh title="🟢 Post request with data"
curl -XPOST -d "name=react,type=js"  https://test.net/react.js
```
```sh title="🟢 Referer Url"
curl -e aparat.com https://test.net
```
```sh title="🟢 Curl with specified certificate file"
curl --cacert ./ca.crt https://test.net
```
```sh title="🟢 Fail silent"
curl -f https://test.net/react.js
```
```sh title="🟢 Form page"
# @ : file, < : contetntOfFile, - : readFromInput 
curl -F "web=@index.html;Type=text/html,pass=12345" https://test.net
```
```sh title="🟢 Keepalive connection"
# --keepalive-time --no-keepalive
curl --keepalive-time 100 https://test.net
```
```sh title="🟢 Specify local port"
curl --local-port 5555 https://test.net
```
```sh title="🟢 Pass phrase for the private key"
curl --pass 123456 https://test.net
```
--pubkey
```sh title="🟢 Send PublicKey"
curl --pubkey key.pub https://test.net/react.js
```
```sh title="🟢 Proxy and proxy tunnel"
curl -p -x socks5://1.2.3.4:12345 https://test.net/react.js
```
```sh title="🟢 Custom address for resolving"
curl --resolve <host:port:address> https://test.net/react.js
```
```sh title="🟢 Get a range of bytes"
curl -r 0-499 https://test.net/react.js
```
```sh title="🟢 Retry"
curl --retry 5 --retry-delay 5 https://test.net/react.js
```
```sh title="🟢 Redirect stderr to the specified file"
curl --stderr err https://test.net
```
```sh title="🟢 Trace all requests and response"
curl --trace - --trace-ascii - https://test.net
```
```sh title="🟢 Force http and ip version"
# or -0  ,   -4 for ipv4 , -6 for ipv6
curl --http1.0 https://test.net/react.js
```
```sh title="🟢 Parallel download"
curl -Z -O https://test.net/react1.js -O https://test.net/react2.js
```
```sh title="🟢 Force tls version"
# or -1 -2 -3
curl --tlsv1.0 --max-tls 1.0 https://test.net/react.js
```
```sh title="🟢 Force tls version"
# or -1 -2 -3
curl --tlsv1.0 --max-tls 1.0 https://test.net/react.js
```
```sh title="🟢 Force tls version"
# or -1 -2 -3
curl --tlsv1.0 --max-tls 1.0 https://test.net/react.js
```
```sh title="🟢 Insecure request"
# or --insecure
curl -k https://test.net
```
```sh title="🟢 C++ code of request"
curl --libcurl cppCode https://test.net/react.js
```




