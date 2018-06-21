前提条件：
------
使用具有IOT服务权限的用户登陆


### 进入IOT服务

进入IOT Core service 如下图所示

![image](https://raw.githubusercontent.com/zhenyu-aws-lab/aws-iot-labs/develop/images/lab1/pic1.jpg)


### 创建IOT中个的物（thing）

* 创建物

点击左侧列表Manage->Things,进入下述界面. 点击Create后选择 create a single thing.
进入如下列表后, 输入对于本地设备的命名, 比如light, 其他保持默认点击下一步.
 
* 生成证书
此页面点击create certificate

得到如下证书, 分别下载4个证书到本地，准备之后代码使用。

* 设置device相应的权限（Policy）
左侧TAB，选择Secure->Policies，点击Create创建policy. 输入policyName,例如: lightdevie_policy,然后如下图所示，点击add statements中的advanced mode，并复制如下的数据到命令行中
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iot:Publish",
        "iot:Subscribe",
        "iot:Connect",
        "iot:Receive"
      ],
      "Resource": [
        "*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "iot:GetThingShadow",
        "iot:UpdateThingShadow",
        "iot:DeleteThingShadow"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```
* 绑定证书和policy
点击左侧tab，Secure->Certificates，选中刚刚创建的Certificates. 进入Certificate详细界面后，选择attach policy，如下图所示
选择上一步中创建的 lightdevie_policy policy

至次，设备再AWS IOT中的注册已经结束，下面进入模拟设备运行的环节。


### 设备运行代码，连接AWS IOT云端服务

* 上传代码到设备
由于本次试验采用的为nodejs,所以要求树莓派上需要有node的运行环境, 并且将代码包demo1.tar进行上传。另外由于模拟信息发送的因素，建议用户同时打开树莓派以及AWS IOT两个界面

上传完毕后采用下述的指令解压
```shell
$ tar -xvf demo1.tar
```
上传在1.中生成的证书到代码的同级目录，放置后如下所示:

```shell
pi@raspberrypi:~/Application/aws-smarthome-light/aws-smarthome-air-purifier/certs $ ls -l
total 16
-rw-r--r-- 1 pi pi 1758 Jun  7 15:45 ca.pem
-rw-r--r-- 1 pi pi 1220 Jun  7 15:23 certificate.pem.crt
-rw-r--r-- 1 pi pi 1679 Jun  7 15:23 private.pem.key
-rw-r--r-- 1 pi pi  451 Jun  7 15:23 public.pem.key
```
其中aws-smarthome-air-purifier 为代码解压目录
* 修改代码并运行

返回AWS IOT主页面，点击左侧TAB，Manage->Things。选择刚注册的thing 如light, 进入如下界面，红框即位endpoint


返回树莓派命令行，下载node的相关依赖包，输入如下指令
```shell
$ npm install
```
* 验证消息上传

返回AWS Iot 界面，点击左侧 Test Tab，如下图所示订阅lights_online topic


继续返回树莓派命令行
```shell
$node index.js
```
得到如下的输出
同时我们在Test界面看到了树莓派已上线的消息，即设备到云端的发送消息成功。
* 验证消息下发的逻辑
同样的，在Test界面点击publish to topic, 进入如下界面，并修改输出如下图所示

点击publish to topic，发现灯亮（风扇转）
同理，修改message的value为0，点击Publish to topic发现灯灭（风扇停）。

### 停止程序，断开电源
