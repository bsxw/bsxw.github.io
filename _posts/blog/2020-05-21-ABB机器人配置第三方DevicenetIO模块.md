# ABB机器人控制柜IRC5配置第三方DeviceNet IO从站

- 机器人侧软硬件配置：
  - IRC5安装有 DeviceNet PCI Express board, DSQC1006（DeviceNet总线现已非标准配置，新控制器目前标配Ethernet IO DSQC1030）。
  ![无](/images/blog/IRC 控制柜网络接口.bmp)
  - 已添加软件选项 709-1 DeviceNet Master/Slave。
  ![无](/images/blog/IRC 控制柜网络接口.bmp)
- 第三方DeviceNet IO模块侧：
  - 连接好DeviceNet 总线（标准为5芯线 V- CANL CANH Shield CANH V+），选择5芯接口的第三方模块，由ABB控制柜提供总线电源防止干扰。注意前端主站末端从站接上120欧姆电阻。
  ![无](/images/blog/IRC 控制柜网络接口.bmp)
  - 设置DeviceNet从站模块地址，一般为两个旋钮（一个*10，一个*1）。
- 主从站设置：
  作为从站的第三方模块适配器需先组态配置好通讯波特率、输入输出大小等，一般通过第三方指定软件进行设置。
- 主站（机器人控制器）设置：
  - 新建DeviceNet 总线，设置总线名称、站号、波特率。
  ![无](/images/blog/IRC 控制柜网络接口.bmp)
  - 新建DeviceNet 总线设备。
    1. 对于已有EDS描述文件的第三方设备可以直接填入相关参数。
      - Vendor ID                          （EDS文件中对应参数）
      - Product Code                       （EDS文件中对应参数）
      - Device Type                        （EDS文件中对应参数）
      - Connection Type                    （EDS文件中对应参数）
      - Connection Input Size             （实际输入模块字节数）
      - Connection Output Size            （实际输出模块字节数）
    2. 对于缺失EDS文件的第三方模块，设置好模块地址后通过新建DeviceNet Generic Device 模板类型设备重启后自动识别模块参数，根据重启弹出的日志填写参数。
    
