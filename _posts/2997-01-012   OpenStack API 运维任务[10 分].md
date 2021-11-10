# **OpenStack API运维任务[10分]**

## **1.1，安装python**

在自行搭建的 OpenStack 私有云平台或提供的 all-in-one 平台上，根据 http 服务中提供 的 Python-api.tar.gz 软件包，完成 python3.6 软件和依赖库的安装

#### 1.1 Linux上

```shell
# 拉取包
[root@controller ~]# curl -O http://172.21.48.11/Python-api.zip
# 解压包
[root@controller ~]# unzip Python-api.zip 

[root@controller ~]# cd Python-api/
[root@controller Python-api]# ll

#解压安装包
[root@controller Python-api]# tar xf python-3.6.8.tar.gz 
# 安装软件包
[root@controller python-3.6.8]# rpm -ivh packages/python3-*
# 安装模块
[root@controller Python-api]# pip3 install certifi-2019.11.28-py2.py3-none-any.whl  && \
pip3 install urllib3-1.25.11-py3-none-any.whl && \
pip3 install idna-2.8-py2.py3-none-any.whl && \
pip3 install chardet-3.0.4-py2.py3-none-any.whl && \
pip3 install requests-2.24.0-py2.py3-none-any.whl
```

#### 1.2  win 上

```shell
1 ，安装 python
2， 安装 pycharm
3， 将python 压缩包放到 win 桌面
4， 安装模块 （pip3 install ...）
```

![](C:\Users\Tao\Desktop\云计算笔记\open stack 考题笔记\openstack 图\Snipaste_2021-10-30_09-16-43.jpg)

```shell
5, 打开pycharm 编写代码 
6，vim  进入 .py 文件   用 which python3   找到 /bin/...

:! which python3
/usr/bin/python3

7， python3  .py     执行 .py 文件
```

 

## **1.2，使用 python 调用 api 实现创建 user[2 分]**

**题目要求**

---

1.检查 python 版本是否为 3.6.8 计 0.3 分 

python3 -V

2.检查 pip 版本计 0.3 分 

pip3 -V

3.执行脚本有正确的返回计 0.2 分 



4.查看用户是否被正确创建计 0.2 分 

5.查看用户是否有正确的描述计 0.2 分 

6.查看是否有正确的获取 token 的方式、调用 user 接口、使用 requests.post、requests.get 等方法 

计 0.8 分

---

#### 1.2.1  获取一个token

注：

---

:! which python3    # 查出执行路径

/usr/bin/python3

---



```python
[root@controller ~]# vim auth.py

#!/usr/bin/python3
import json, requests

# 定义全局路径
headers = {}
headers["Content-Type"] = "application/json"
os_auth_url = 'http://172.21.48.21:35357/v3/auth/tokens'  # 填入controller节点IP  
#  openstack endpoint list | grep keystone  //查看

# 多步骤身份验证（2-Factor密码和TOTP）定义请求头
body = {
    "auth": {                                              #授权
        "identity": {                                      #身份
            "methods": ["password"],                       #验证方法：密码
            "password": {
                "user": {
                    "id": "5ebdfd5c8ac8435f8389245c0e1d89e7",  #根据controller的user_id  
                    "password": "000000"
                }
            }
        },
        "scope": {                                          #范围
            "project": {
                "id": "a8159b3bef3f472b916d3671ec56bac4"    #根据controller的project_id
            }
        }
    }
}

#获取token值
def get_token():
    reponse = requests.post(os_auth_url, data=json.dumps(body), headers=headers).headers["X-Subject-Token"]
    return reponse
print(get_token())    # 可省略

执行
[root@controller ~]# python3 auth.py 
```

#### 1.2.2 调用API 创建user

```python
[root@controller ~]# vim create_user.py  

#!/usr/bin/python3
import json, requests, time
from auth import get_token

headers = {}
headers['X-Auth-Token'] = get_token()
headers['Content-Type'] = 'application/json'
url = 'http://172.21.48.21:35357/v3/users'


# print(requests.get(url, headers=headers).json())    # 数据结构

body = {
    'user': {
        'name': 'chinaskill',
        'domain_id': 'c3bbd3a51c9547a2a44daa1d923db1fc',
        'enabled': True,
        'description': 'API create user!',      # openstack domain list 复制单词 description
    }
}

def create_user():
    re = requests.post(url, data=json.dumps(body), headers=headers).json()
    print(re)

def main_user():
    re = requests.get(url, headers=headers).json()
    name = re['users']
    for su in name:
        if su['name'] == 'chinaskill':
            requests.delete(url + f"/{su['id']}", headers=headers)
            time.sleep(1)
            create_user()
            print('删除同名用户并新建成功')
            break
    else:
        create_user()
        print('创建用户成功')

main_user()

执行
#!/usr/bin/python3
```

#### 1.2.3 使用 python 调用 api 实现创建 flavor[2 分] 

---

创建一个云主机类型，名字为 pvm_flavor、vcpu 为 1 个、内存为 

1024m、硬盘为 20G、ID 为 9999。执行完代码要求输出"**云主机类型创建成功**"

**题目要求**

1.执行脚本有正确的返回计 0.2 分 

2.查看 flavor 是否被正确创建，即 flavor 的详细信息是否正确计 0.2 分 

3.查看是否有调用了 flavor 的接口计 0.3 分 

4.查看是否有正确的获取 token 的方式、调用 user 接口、使用 requests.post、requests.get 等方法 

计 1.3 分

---



```python
[root@controller ~]# vim create_flavor.py

#!/usr/bin/python3
import json, requests
from auth import get_token

headers = {}
headers['X-Auth-Token'] = get_token()
url = "http://1:8774/v2.1/flavors"

body = {
    "flavor": {
        "name": "pvm_flavor",
        "vcpus": 2,
        "ram": 2048,
        "disk": 20
    }
}

def create_flavor():
    re = requests.post(url, data=json.dumps(body), headers=headers).json()   # 提交
    print(re)


def main_flavor():
    re = requests.get(url, headers=headers).json()         # 查看
    name = re['flavors']

    if len(name) == 0:
        create_flavor()
        print('云主机类型创建成功...')
    elif name[0]['name'] == body['flavor']['name']:
        requests.delete(url + f"/{name[0]['id']}", headers=headers)
        create_flavor()
        print('删除同名云主机类型并新建成功...')

main_flavor()

执行
python3 create_flavor.py 
```



#### 1.2.4  使用 python 调用 api 实现创建网络[2 分]

----

为平台创建内部网络 pvm_int，子网名称为 pvm_intsubnet;

设置云主机网 

络子网 IP 网段为 192.168.x.0/24（其中 x 是考位号），网关为 192.168.x.1（如果存在同名内 

网，代码中需先进行删除操作）。执行完代码要求输出“网络创建成功”



**题目要求**

1.执行脚本有正确的返回计 0.3 分 

2.查看网络是否被正确创建，即网络的详细信息是否正确计 0.3 分 

3.查看是否有调用了 net 的接口计 0.3 分 

4.查看是否有正确的获取 token 的方式、调用 user 接口、使用 requests.post、requests.get 等方法 

计 1.1 分

---



```shell
[root@controller ~]# vim create_network.py   

#!/usr/bin/python3
import json, requests      # 导包
from auth import get_token   # 进入 auth  导出  get_token


class networks:                       #创建类
    headers = {}
    headers['X-Auth-Token'] = get_token()
    headers['Content-Type'] = 'application/json'
    url = "http://172.21.48.21:9696/v2.0/"     # openstack endpoint list  <查找 url>
    # print(requests.get(url + "networks", headers=headers).json())     # 获取创建网络时的 数据结构
    # print(requests.get(url + "subnets", headers=headers).json())     # 获取创建子网时的 数据结构


# networks      # 执行类

    body = {                                     # 创建网络的数据结构
        "network": {
            "name": "pvm_int",
            'provider:network_type': 'vxlan',
            'router:external': False,
            'shared': False,
        }
    }

    def pvm_int(self):     # 创建网络
        re = requests.post(self.url + "networks", data=json.dumps(self.body), headers=self.headers).json()
        print(re)   # 获取  创建 成功后的数据结构



    def pvm_intsubnet(self):    #  创建 子网
        net_id = requests.get(self.url + "networks", headers=self.headers).json()["networks"][0]["id"]
        body = {
            "subnet": {
                "name": "pvm_intsubnet",
                'network_id': net_id,
                'cidr': '192.168.100.0/24',
                'gateway_ip': '192.168.100.1',
                'ip_version': 4,
            }
        }
        re = requests.post(self.url + "subnets", data=json.dumps(body), headers=self.headers).json()    # 创建子网
        print(re)    #  获取  创建 成功后的数据结构

    def main_network(self):
        re = requests.get(self.url + "networks", headers=self.headers).json()  # 获取 networks  的  值（创建网络时   产生的数>
据结构）
        name = re["networks"]             # name  只接收  networks  的 值

        if len(name) == 0:                 # 如果 值为 空  则创建
            self.pvm_int()       # 调用函数的格式     self.函数
            self.pvm_intsubnet()
            print('新建网络成功...')
        elif name[0]['name'] == self.body['network']['name']:      # 如果值不为空   则  先删除  后创建
            requests.delete(self.url + f"networks/{name[0]['id']}", headers=self.headers)
            self.pvm_int()
            self.pvm_intsubnet()
            print('删除同名网络并新建网络成功...')


if __name__ == '__main__':     # 优先执行  里面的内容
    net = networks()
    net.main_network()
    
    
  执行
  python3 create_network.py 
```



#### 1.2.5 使用 python 调用 api 实现创建镜像[2 分]

---

要求在 OpenStack 私有云平台中上传镜像 cirros-0.3.4-x86_64-disk.img，名字为 pvm_image， 

disk_format 为 qcow2，container_format 为 bare。执行完代码要求输出“镜像创建成功，id 

为:xxxxxx”

**题目要求**

1.执行脚本有正确的返回计 0.2 分 

2.查看镜像是否被正确创建，即镜像的详细信息是否正确计 0.2 分 

3.查看是否有调用了 image 的接口计 0.3 分 

4.查看是否有正确的获取 token 的方式、调用 user 接口、使用 requests.post、requests.get 等方法 

计 1.3 分

---



```shell
[root@controller ~]# vim create_image.py 

#!/usr/bin/python3
import json, requests, time
from auth import get_token

headers = {}
headers['X-Auth-Token'] = get_token()
headers['Content-Type'] = 'application/octet-stream'
url = "http://172.21.48.21:9292"

body = {
    "name": "pvm_image",
    'container_format': 'bare',
    'disk_format': 'qcow2',
}

def create_image():
    re = requests.post(url + "/v2/images", data=json.dumps(body), headers=headers).json()
    print(re)
    re = requests.put(url + re["file"], data=open("/root/cirros-0.3.4-x86_64-disk.img","rb"), headers=headers).status_code
    print(re)

def main_image():
    re = requests.get(url + "/v2/images", headers=headers).json()
    name = re['images']
    print(name)
    if len(name) == 0:
        create_image()
        print('镜像上传成功')
    elif name[0]['name'] == body['name']:
        requests.delete(url + f"/v2/images/{name[0]['id']}", headers=headers)
        time.sleep(2)
        create_image()
        print('删除同名镜像并新建成功')

main_image()

执行
python3 create_image.py 
```

#### 1.2.6 使用 python 调用 api 实现创建云主机[2 分]

----

要求使用 pvm_image、pvm_flavor、pvm_intsubnet 创建 1 台云主机 pvm1（如果存在同名虚 

拟主机，代码中需先进行删除操作）

**题目要求**

1.执行脚本有正确的返回计 0.2 分 

2.查看云主机是否被正确创建，即云主机的详细信息是否正确计 0.2 分 

3.查看是否有调用了 servers 的接口计 0.2 分 

4.查看是否有调用了 images 的接口计 0.2 分 

5.查看是否有调用了 flavor 的接口计 0.2 分 

4.查看是否有正确的获取 token 的方式、调用 user 接口、使用 requests.post、requests.get 等方法 

计 1 分

---

```shell
[root@controller ~]# vim create_vm.py

#!/usr/bin/python3
import requests, json
from auth import get_token

headers = {}
url = 'http://172.21.48.21:8774/v2.1/servers'
headers["X-Auth-Token"] = get_token()

body = {
    "server": {
        "name": "pvm1",
        "imageRef": "3ee8d3b1-5f81-4717-9138-531bc6f6a52f",                #手动替换一下id
        "flavorRef": "9999",               #手动替换一下id
        "networks": [{ "uuid" : "35b6b7cd-f0f1-403c-87ca-42cbda751215",}]  #对应网络的uuid
    }
}


def create_server():
    re = requests.post(url, headers=headers, data=json.dumps(body)).json()
    print(re)

def main_server():
    re = requests.get(url, headers=headers).json()
    value = re['servers']

    if len(value) == 0:
        create_server()
        print("新建成功")

    elif re['servers'][0]['name']  == body['server']['name']:
        id = re['servers'][0]['id']
        re = requests.delete(url + f"/{id}", headers=headers,)
        create_server()
        print("删除同名并新建成功")

main_server()

执行
python3 create_vm.py 
```



















