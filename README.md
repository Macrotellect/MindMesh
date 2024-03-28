# 基本介绍

# 产品说明

## 开关机操作

## 工作状态指示

## 头环佩戴

## 头环参数

## 注意事项

# 数据说明

## 脑电波

### 名词解释

### 参考文献

## 冥想指标

### 名词解释

### 参考文献

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
  | token   | 验证token |

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