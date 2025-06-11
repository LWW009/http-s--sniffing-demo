# 🔤
#### ChmodBPF 
Can I understand it as a specific file - or say a tool - bundled with the Wireshark installation package, designed to configure access permissions for the `/dev/bpf*` interface? In this context, what exactly does the term 'script' mean? Is it accurate to say that when I install Wireshark, this script is included as a helper tool to enable access to the packet capture interface?  ChmodBPF 权限脚本 - 这里的‘脚本’是什么意思？可以理解为我下载Wireshark的时候，Wireshark安装包里的某个file用来支持我访问 `/dev/bpf*` 接口的一个工具吗？
> In the Wireshark installation package (as a prerequisite), a script named ChmodBPF is automatically included to configure capture permissions during the initial setup. Specifically, this script is an executable file—typically a shell script—that automates the execution of system commands required to grant Wireshark access to packet capture interfaces such as /dev/bpf*.
#### /dev/bpf*
Can I understand `/dev/bpf*` as a kind of "gateway" for capturing network traffic? In other words, does accessing this interface serve as a prerequisite for tools like Wireshark to capture packets—potentially including sensitive information such as plaintext usernames and passwords of devices on the same network? Is it fair to say that access to this interface is like a "key" that unlocks the ability to observe network data?  `/dev/bpf*` 可以理解为如果要抓流量数据，从这个接口access，才可能抓到流量中的比如正在使用这个流量的某台设备的username和password？
> The `/dev/bpf*` devices can be seen as intermediaries between the network interface (e.g., WLAN) and user-space applications like Wireshark. They act as controlled entry points for packet capture. When an application like Wireshark wants to capture traffic, it must first gain access to these devices. BPF stands for Berkeley Packet Filter, which serves as a kernel-level filtering mechanism that controls which packets are passed to the user-space program.
#### 'GET' & 'POST'








3, request.method的`GET`和`POST`区别是什么？我curl用`GET`可以抓到么？ Any difference between `GET` and `POST`? Can I also get credentials by `GET`?
> parameters are passed and exposed on the URL with `GET`, whilst it is passed and hidden in the body with `POST` (but still in plain text under HTTP). Yes, with `curl -X GET http://xxx.com?username=abc&password=123`.
---
4, 先post到login.php，再post到userinfo.php，跟我直接用curl `POST`到login.php，多了一个什么动作？ What is the extra action with curl if to `POST` to login.php, then to userinfo.php, in comparison with directly `POST` to login.php?
> 'save and carry session/cookie' action.

---
5, 我现在用的是家里的wifi，为啥捕获接口里有那么多接口？Why there are list of interfaces show up in my Wireshark main page, even if only one of them is connected (which is en0 = home WiFi)?
> Each entry represents a different ‘virtual’ or ‘backup’ interface. For example, lo0 = local loopback interface (self-access); en0 = home Wifi main interface; en1, bridge, utun, awdl, vmnet = virtual interfaces used by features like AirDrop, VPN, VirtualBox, Apple Wireless Direct Link, and others.
---
6, 我现在在家，比如我想抓我所在城市机场wifi里的流量里某台设备的password，我需要去机场使用机场wifi吗？I’m home right now, but what if I’d capture something from a mobile device at airport in my city, do I need to physically be there and connect the airport WIFI?
> The answer is yes, moreover, it can’t be 100% certainty to capture what we want. Talking about ‘infrastructure’, on top of using the same network, the network itself must be a non-isolated LAN, which means all devices under the same network can ‘see’ each other. On conditions that the device is using HTTP instead of HTTPS, Wireshark had to be ‘on’ before the data has been transmitting. Only then would the Wireshark successfully do the caption.

## HTTPS
1, How to understand DNS Poisoning?

> Take facebook.com for example. Cache poisoning = IP address poisoning = The IP address is no longer Facebook's, but that of a malicious website. Once the IP address is poisoned, when I try to visit Facebook, the system will redirect me to the poisoned IP address and take me directly to the web page of the malicious site. This means that even if I type "facebook.com" in the browser's address bar, what appears on the screen is actually the interface of a malicious website. 

> 缓存被污染 = ip地址被污染 = 这个ip地址不再是facebook的ip地址而是某个恶意网站的。一旦ip地址被污染，我访问facebook的时候，系统会跳进这个被污染的ip地址而直接给我direct去恶意网站的web界面，也就等于即便我network标签栏里输入的是facebook.com，呈现在眼前的是某个恶意网站的界面，





# Steps (One-Off):
1, open Wireshark, select Wi-Fi: en0, click ‘Start capturing packets’.

2, open Terminal, run `curl -X POST http://testphp.vulnweb.com/login.php \-d "username=testuser&password=123456" `

> this step is to use `curl` command to simulate the wen login request

> http://testphp.vulnweb.com/login.php is the test site

> -X POST：define it's the POST request

> -d: submitted form data (same as submitted by the browser)

3, filter by key words 'http' in Wireshark
will show 2 results under HTTP protocal: POST request + 200 OK

4, check out the captured username and password 


# Case (Bug) during test

用curl在terminal里simulate的login.php和userinfo.php，userinfo里还是可以看到密码，说明用curl的单词请求还是可以抓到userinfo.php数据，即便是在login之后。

Using `curl` in the terminal to simulate login.php and userinfo.php, the password is still visible in userinfo.php, which suggests that a single `curl` request can still retrieve sensitive data (for example, passwords) from userinfo.php, even with login status.

When using `curl` commands in the terminal to simulate a login process (login.php) and access a user information page (userinfo.php), the response from userinfo.php still shows sensitive data like password even after logging in with `curl`. This raises a concern that just using `curl` in a simple request (userinfo.php) can retrieve sensitive information without proper session handling or security checks.
Generally, passwords should not be retrieved to client server (web brower or `curl` userinfo.php) after logging in, it should only be verified with login.php, for example, to cross-check whether the login password is in accordance with the one in data center. The userinfo.php should only retrieve a 'verified proof', for example, session ID or token, instead of the password itself.

It implies:

1, login state might not be properly managed (e.g. no session or token validation)

2， If user data can be retrieved using `curl` without sending any cookies, session ID, or token, it indicates that the endpoint is unintentionally exposed and lacks proper access control.