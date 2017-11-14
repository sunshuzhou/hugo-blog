+++
date = "2017-05-03T09:39:24+08:00"
description = ""
title = "cipherscan代码阅读——第二部分"

+++

`cipherscan`使用python编写，这里列举主要的功能函数，解释代码的运行逻辑。


- `main`

```python
main()
	...
	# 从Mozilla下载标准
	build_cipher_lists()
	# 调用cipherscan输出json格式
	data = subprocess.check_output('cipherscan', '-j', target)
	# 处理json数据
	process_results(data)
```


- `build_cipher_lists`从[Mozilla](https://statics.tls.security.mozilla.org/server-side-tls-conf.json)下载相应的json格式的[标准](https://wiki.mozilla.org/Security/Server_Side_TLS)；下载失败则从本地读取事先准备好的json文件；构造三个全局变量以供后面判断服务器的ssl/tls级别使用。

```python
def build_cipher_lists():
	...
	raw = urllib2.urlopen(sstlsurl).read()
	conf = json.loads(raw)
	...
	global old, inter, modern
	old = conf["configurations"]["old"]
	inter = conf["configurations"]["inter"]
	modern = conf["configurations"]["modern"]
```

- `process_results`

```python
def process_results(data, level=None, do_json=False, do_nagios=False):
	...
	results = json.loads(data)
	# 调用函数判断所有级别
	evaluate_all(results)
	...
	# 输出要达到某个级别需要做的修改
	if level:
		print(failures[level])
	else:
		print(failures['old'])
		print(failures['intermedia'])
		print(failures['modern'])
```

- `evaluate_all`

```python
def evaluate_all(results):
	is_old(results)
	is_intermediate(results)
	is_modern(results)
	is_fubar
```


