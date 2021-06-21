## web server理論
### http協定
是一種用於分佈式、協作式和超媒體訊息系統的應用層協定。HTTP是全球資訊網的數據通信的基礎。

* 客戶端----->瀏覽器
* 伺服器端--->伺服器


* 客戶端請求------->請求網頁
```
GET / HTTP/1.1
Host: www.google.com
```
>>結尾為空行

* 伺服器端應答----->回應網頁
```
HTTP/1.1 200 OK
Content-Length: 3059
Server: GWS/2.0
Date: Sat, 11 Jan 2003 02:44:04 GMT
Content-Type: text/html
Cache-control: private
Set-Cookie: PREF=ID=73d4aef52e57bae9:TM=1042253044:LM=1042253044:S=SMCc_HRPCQiqy
X9j; expires=Sun, 17-Jan-2038 19:14:07 GMT; path=/; domain=.google.com
Connection: keep-alive
```
* HTTP連線示意圖
![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/http.jpg)


