---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [技术]
created: '2021-08-30 13:58:26'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: '', thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-08-30 13:58:26'
parent: 0
password: ''
slug: QNAP-Docker-Ubuntu-上手使用-附-Cloudflare-DDNS
status: publish
tags: [DDNS, Docker, Linux, NAS, QNAP, Ubuntu]
template: ''
title: QNAP Docker Ubuntu 上手使用 | 附 Cloudflare DDNS
type: post
---
# Preface

之前折腾 `Clash` 的时候，我拿 `py` 写的 `Cloudflare DDNS` 的脚本挂了，于是乎就打算直接挂在 `NAS` 上。虽然现在折腾完毕，但是这次在 `NAS` 上搞得过程还是很值得记录的。

# Ubuntu Docker

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210831232041.png)

安装完毕之后要做几件事情：

1. 换源，但是此时只能把连接改成 `http` 协议，因为还没装 `ssl` 库和 `ca` 证书
2. 更新源：`apt update`
3. 安装 `ssl` 库：`apt install openssl`
4. 安装 `ca` 证书：`apt-get install ca-certificates -y`
5. 讲源改成 `https`
6. 更新源：`apt update`

# 安装环境

* `python3`
* `pip3`

自行搜索，`apt` 一顿乱装即可

# 脚本

从我的 https://github.com/JeffersonQin/CloudflareDDNS 当中抽出一段：

```python
import json
import requests
import time
import traceback
import os

while True:
	try:
		root_dir = os.path.split(os.path.abspath(__file__))[0]
		config_dir = os.path.join(root_dir, 'config.json')
		log_dir = os.path.join(root_dir, 'log.out')
		with open(config_dir, 'r', encoding='utf8') as f:
			config = json.load(f)
			X_AUTH_KEY = config['X_AUTH_KEY']
			ZONE_ID = config['ZONE_ID']
			EMAIL = config['EMAIL']
			DNS_RECORD_NAME = config['DNS_RECORD_NAME']
			WEBSITE_URL = config['WEBSITE_URL']

		headers = {
			"X-Auth-Email": EMAIL,
			"X-Auth-Key": X_AUTH_KEY,
			"Content-Type": "application/json"
		}

		ip = requests.get(url='http://ip.42.pl/raw').text

		res = requests.get(url="https://api.cloudflare.com/client/v4/zones/" + ZONE_ID + "/dns_records", headers=headers).json()
		result_arr = res["result"]
		site_id = ""

		for result in result_arr:
			if result["type"] == 'A':
				if str(result["name"]) == WEBSITE_URL:
					site_id = result["id"]
					break

		response = requests.put(url="https://api.cloudflare.com/client/v4/zones/" + ZONE_ID + "/dns_records/" + str(site_id), headers=headers, data='{"type":"A","name":"' + DNS_RECORD_NAME + '","content":"' + ip + '","ttl":1,"proxied":false}')

		res = json.loads(response.text)

		with open(log_dir, 'a', encoding='utf8') as f:
			f.write(str(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())) + ": " + ip + "\n")
			if res["success"]:
				f.write(str(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())) + ": success\n")
			else:
				f.write(str(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())) + ": failed\n")
				f.write("response: \n")
				f.write(res + "\n")
	except Exception as e:
		with open(log_dir, 'a', encoding='utf8') as f:
			f.write(str(time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())) + ": unexpected error occurred\n")
			f.write("Exception: " + e + '\n')
			f.write("Traceback: \n")
		f.write(traceback.format_exc() + '\n')
	time.sleep(1800)
```

额外需要一个配置文件：`config.json`，需要如下字段：
* `X_AUTH_KEY`
* `ZONE_ID`
* `DNS_RECORD_NAME`
* `WEBSITE_URL`

本来是用 `nohup` 的，然后管道追加到文件，但貌似这样 `log` 不能正确的输出，就直接用代码实现了。

# 添加开机启动

首先是 `NAS` 中这个 `Docker` 的开机启动：

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210831233506.png)

然后是脚本的开机运行。由于默认的启动命令是 `bash`:

![](https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/picgo/20210831233622.png)

所以我在 `/root/.bashrc` 中追加了一句：

```
nohup python3 /path/to/script/ddns.py &
```

记得修改路径。
