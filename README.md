# 基本介绍
## 系统简介
脑能网MindMesh是由深圳宏智力科技有限公司开发的团体版脑电采集系统. 该系统最多支持50人的脑波数据采集.
![MacDown logo](https://hardware.cn-bj.ufileos.com/guestapk/pp1QQ20240328-1540582x.png)

![MacDown logo](https://hardware.macrotellect.com/guestapk/pp3QQ20240328-154238%402x%20(1).jpg)
开发者通过接入脑能网SDK可以获取到头环采集的数据指标（目前仅Windows系统, 不支持iOS、安卓、Linux的接入），通过脑能网SDK可以快速构建属于您的应用。
## 数据指标
数据指标包含专注度、放松度、 α（阿尔法）波、β（贝塔）波、γ（伽玛）波、θ（西塔）波、δ（德尔塔）波. 详细解释见下方名词解释.

# 产品说明

## 系统连接示意图
![](https://hardware.macrotellect.com/guestapk/pp4QQ20240328-161605%402x.jpg)

为了说明方便, 采集设备在下面的内容里面简称头环.
系统连接电脑的方法, 可以参考下面的视频链接.
**https://www.youtube.com/watch?v=vOP2R1yOYjY&t=2s**

## 头环佩戴
- 每次使用前后，推荐使用无尘布清洁头环的电极；佩戴头环前保持额头清洁、无遮挡

- 佩戴位置：前额、高于眉毛一个手指宽位置

- 佩戴方式：左手握住头环的核心模块, 从上往下佩戴在前额即可

- 注意事项：感觉头环电极紧贴前额即可，佩戴不宜过松，容易导致电极接触不良；也不宜太紧，佩戴后有一定舒适度为宜
- **正确佩戴的参考视频:
https://www.youtube.com/watch?v=UM_S2CRuSk4&t=33s**

## 注意事项
- 头环电极不能被头发遮挡, 否则会影响采集效果.
# 数据说明

## 脑电波
脑电波是一种生物电信号. 大脑在活动时，大量神经元同步发生的突触后电位经总和后形成的。它记录大脑活动时的电波变化，是脑神经细胞的电生理活动在大脑皮层或头皮表面的总体反映.

### 参考文献
文献名: Angelakis_et_al_2004_Peak_alpha_frequency-an_electroencephalographic_measure_of_cognitive_preparedness  

复制下方链接到浏览器即可下载PDF文件: https://hardware.macrotellect.com/guestapk/Angelakis_et_al_2004_Peak_alpha_frequency-an_electroencephalographic_measure_of_cognitive_preparedness.pdf
## 放松度
放松度/Relaxation: 此数值实时反映大脑的放松程度，0为最低，100为最高。试着闭上双眼，深呼吸，放松全身肌肉，你将可以看见该数值的提升。1秒输出1个数值.

### 参考文献
文献名: Klimesch_1999_EEG_alpha_and_theta_oscillations_reflect_cognitive_and_memory_performance  

复制下方链接到浏览器即可下载PDF文件: https://hardware.macrotellect.com/guestapk/Klimesch_1999_EEG_alpha_and_theta_oscillations_reflect_cognitive_and_memory_performance%20(1).pdf
## 专注度

### 名词解释

### 参考文献

## 放松度

### 名词解释

### 参考文献

# SDK

## Windows

### 环境说明

- .NET Standard 2.0

### SDK

- MindMeshParser.dll

### 重要说明

- MindMeshParser接收的是从MindMesh网关串口读取的原始数据
- MindMesh网关串口名称USB Serial Port，波特率115200，停止位1，数据位8，校验位None
- 串口初始化示例代码

  ```
    SerialPort serialPort = new SerialPort("COM");
    serialPort.BaudRate = 115200;
    serialPort.StopBits = StopBits.One;
    serialPort.Parity = Parity.None;
    serialPort.DataBits = 8;
  ```

### 接口列表

- **MindMeshParser(string token)** 构造函数
  
  | 参数     | 说明     |
  | ------- | ------- |
  | token   | 验证token.token需付费,请联系购买渠道获取有效的token,否则会初始化失败 |

- **Parser(byte[] bytes, int length)**
  
  | 参数     | 说明     |
  | ------- | ------- |
  | bytes   | 获取到的MindMesh原始数据 |
  | length  | 数据长度

### 事件列表

- **ParserDataOnReceived(MindMeshData[] datas)**
  
  | 参数     | 说明     |
  | ------- | ------- |
  | datas   | 已连接网关的设备的数据 |

- **ParserDataOnLost(MindMeshData[] datas)**
  
  | 参数     | 说明     |
  | ------- | ------- |
  | datas   | 已连接过网关但是下线或者丢包的设备的数据 |

### MindMeshData类说明

- totalDevice 所有连接设备的数量

- duration 网关刷新时长

- address 设备地址

- deviceID 设备ID

- packageIndex 设备发送数据索引，如果设备下线或丢包不更新

- isLost 设备是否丢包或下线

- att 专注度

- med 放松度

- power 设备电量

- delta

- theta

- lowAlpha

- highAlpha

- lowBeta

- highBeta

- lowGamma

- highGamma

- signal 设备佩戴质量，当值为200设备未佩戴，当值为0表示已经戴好

- ToString() MindMeshData对象转换成字符串的方法


### 代码参考

```

MindMeshParser parser = new MindMeshParser(token);
parser.ParserDataOnLost += OnLost;
parser.ParserDataOnReceived += OnReceive;

SerialPort serialPort = new SerialPort("COM");
serialPort.BaudRate = 115200;
serialPort.StopBits = StopBits.One;
serialPort.Parity = Parity.None;
serialPort.DataBits = 8;
if (!serialPort.IsOpen)
{
    serialPort.Open();
}

Thread thread = new Thread(() =>
{
    while (true)
    {
        Thread.Sleep(500);
        if (!serialPort.IsOpen)
        {
            return;
        }
        int dataLength = serialPort.BytesToRead;
        if (dataLength != 0)
        {
            byte[] ds = new byte[dataLength];
            serialPort.Read(ds, 0, dataLength);
            parser.Parser(ds, dataLength);   
        }
    }
});

thread.Start();

void OnLost(MindMeshData[] datas)
{
    for (int i = 0; i < datas.Length; i++)
    {
        Console.WriteLine(datas[i].ToString());
    }
}

void OnReceive(MindMeshData[] datas)
{
    for (int i = 0; i < datas.Length; i++)
    {
        Console.WriteLine(datas[i].ToString());
    }
}

```