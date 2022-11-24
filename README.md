[TOC]

```mindmap
# 标准模块FAQ
## 硬件设计FAQ
### 电源
### 开机时序
## 软件设计FAQ
### AT流程
### PPP拨号
```

[硬件设计FAQ](#0)
[软件设计FAQ](#1)

<span id='0'></span>
# 1 硬件设计FAQ
1.1 电源设计注意事项
------------

1) 模块在工作时，需要供电电源最大可提供2A的电流，并建议加上大电容续流；

2) 模块在工作时，要求工作电压纹波不能超过30mA，建议加上滤波电容和TVS稳压管；

3) PCB布局时，建议滤波电容、TVS管和续流电容靠近模块，更好的发挥元器件效果；

4) PCB走线时，电源走线建议宽度达到2mm，走线平滑，无宽窄急剧变化和尖角；电源的主地导通性要好，否则容易导致电流不足、EMC问题。主电源走线尽量远离射频走线和SIM卡走线；电源走线较长时，应在电源走线中间加0.1UF的滤波电容。

5) 供电为5V时，建议使用LDO芯片做电平变换，且需要保证LDO芯片的输出电流可以达到2A，不建议使用二极管降压，LDO有如29302系列芯片；

6) 供电在12V时，建议使用DCDC芯片做电平变换，且需要保证DCDC芯片的输出电流可以达到2A，DC-DC有如54231芯片。

7) 禁止用二极管降压，否则瞬间2A的大电流导致的压降比较大，易引起模块异常重启。

1.2 串口设计注意事项
------------

模块的串口电平为2.8V或1.8V，当外部MCU的串口电平与模块串口电平不匹配时，需要设计增加电平变换电路进行连接，详细请参考模块对应的硬件设计指南串口电路设计部分。

1.3 模块串口问题
----------

### 1.3.1 串口正常工作电平范围是多少？超过这个范围的通信应该怎么处理？

模块串口是2.8V或1.8V的CMOS电平接口，允许输入的最大电压是3.1V或2.1V。如果输入电压超过3.1V或2.1V，请客户增加电平变换电路。具体可以参考我司各型号模块对应的硬件设计指南中串口设计部分。

### 1.3.2 串口通信出现乱码是什么原因？应该如何解决？

首先确认波特率是否正确，我司模块在客户没有定制波特率的情况下默认出厂波特率是自适应模式，开机后向模块发送AT指令进行适配；其次确认下主、从板的串口共地是否良好。

### 1.3.3 串口通信回码只有一部分是什么原因？如何解决？

通过以下几个方面查找问题：

1) 模块主电源是否有跌落，电源跌落会造成模块异常关机；

2) 确认串口是否有串联电阻和滤波电容；

3) 如果有，将串联电阻改为100~200R范围内的电阻，滤波电容去掉后调试一下。因为串口的串联电阻和并联电容取值越大，对串口信号波形的畸变越严重，串口通信延时越长，可能会导致串口通信异常；

4) 查看串口是否共地，如果串口连接没有共地，可能会导致串口通信异常；

5) 确认模块串口通信是否有兼容设计。如果有，将其他兼容部分完全断开，保证只连通目前调试的一对串口。因为串口的兼容部分如果使用直连方式设计，对信号完整性会有影响，可能会导致串口通信存在异常。

### 1.3.4 模块上电后，串口不通怎么处理？

 首先确认模块上电后是否有正常开机，如果模块已正常开机， 再检测串口电路是否正常，如果串口电路连接也正常，再检查串口工具配置是否正确。通过以上的方式还无法定位问题原因的情况下，请联系有方技术支持人员。（串口发送AT指令需在末尾加回车换行，注意先回车后换行\\r\\n，例如：AT+CSQ\\r\\n）

  

1.4 模块使用寿命问题
------------

### 1.4.1 影响模块使用寿命的因素有哪些？

影响模块使用寿命的因素有很多，主要因素包括以下几个：

1) 模块供电电压不稳、超标

2) IO口电平不匹配，超过电平最高值

3) 模块工作温湿度

4) 静电冲击

5) 异常掉电，存在小概率的程序丢失问题

6) 频繁重启

### 1.4.2 频繁的重启、频繁的上电掉电或频繁的拨号是否会导致模块寿命的降低？

频繁拨号不会影响产品使用寿命，但是频繁重启会对模块的寿命有一定的影响。所以尽量避免频繁对模块进行重启，一般建议24小时内重启10次为佳。

### 1.4.3 如何防止模块因过压冲击导致模块损坏？

模块的最高工作电压为4.3V，过压会导致模块损坏，因此需要对模块电源做过压保护处理。普通的应用场合，建议增加齐纳二极管进行保护；环境恶劣的应用场合（比如车载电子），建议增加TVS管保护。

  

1.5 模块开关机和复位问题
--------------

### 1.5.1 模块设计时是否需要周期性的关机重启操作？

理论上讲，定期（例如间隔2天）关机重启是有好处的。但是从实际使用角度讲，用户安装模块后，一般是不希望模块出现复位操作的，这样会造成掉线现象，有用户会通过服务器考核掉线指标。如果出现TCP假连接现象或者长时间网络阻塞，推荐启动TCP重连过程，正常情况下都能解决问题，若不能解决问题才使用关机重启解决。

另外注意，针对异常状态的重启，要注意控制每天模块的重启次数，应控制在10次以内为佳。比如在不插SIM的情况下，不能对模块不断的重启，应设计一套机制，老保证模块24小时重启不要超过10次，详见《重启机制应用指南》。

### 1.5.2 如何避免模块因电源供电不足导致模块重启或死机的问题？

模块在通讯时**瞬间电流**可以达到**2A**以上，在系统设计给模块供电时应保证电源最少有2A的输出负载能力；另外，为了提高电源的瞬间大电流的续流能力，平滑供电电源的电压波动，必须在电源上增加一个大容量的铝电解电容（470uF或1000uF）或钽电解电容（220uF或470uF）。

### 1.5.3 模块异常Reset，原来配置的信息是否存在？

2G模块复位后原来设置的APN、用户名和密码等信息是不会保存的，所有的连接也不存在，需要重新进行初始化和拨号的流程。

4G模块最新版本会保存APN、用户名和密码信息，以便下次开机时进行网络接入。

### 1.5.4 模块正常开机时序是什么？要注意哪些问题？

模块的ON/OFF管脚控制模块的开机和关机，低电平有效。ON/OFF管脚内部有上拉，外部采用OC电路控制即可。模块上电后，拉低ON/OFF管脚并保持Ton秒（T根据模块的硬件设计指南中的要求来设置），模块开机，开机后串口输出开机回码（波特率自适应的情况下没有开机回码）。在开机状态下，拉低模块ON/OFF管脚T秒（T根据模块的硬件设计指南中的要求来设置）以上，模块进入关机流程，注销网络，最后关闭模块主电源。开关机时序图如下所示。（注意：要保证开机电平足够低）

![](https://www.showdoc.com.cn/server/api/attachment/visitFile?sign=3e957e439fa6fd3f84cc721c3dc24c67)

### 1.5.5 在拨号初始化过程中是否有一项不通过就得关机重连？

在模块初始化和拨号过程中，有五个点的状态如果不通过则需要关机重连：

1) 模块初始化中查询CCID，建议间隔1秒查询几次后得不到CCID号，则需要复位重连。可能原因：未检测到卡或卡座坏等。

2) 查询信号强度CSQ，建议间隔1秒查询30次，如果信号强度一直为99，则需要复位重连。可能原因：天线接触不良、废卡或PA坏等。

3) 查询网络注册状态CREG，建议间隔1秒查询30次，如果回码不是(0,1)或(0,5)，则需要复位重连。可能原因：停机、信号弱或卡信息丢失等。

4) 建立TCP连接，如果5次建立不了TCP连接，则需要复位重连。可能原因：卡欠费、服务器未打开、IP地址或端口错误等。

注意：要注意控制每天模块的重启次数，应控制在10次以内为佳。

### 1.5.6 模块插SIM卡工作时总是重启怎么解决？

问题表现为模块不插卡时工作正常，插卡后总是会出现异常重启现象。此问题可能是模块电源供电不足或者电源上的蓄能电容太小导致的。因为GSM的burst突发原理，模块工作瞬间电流可以达到2A以上，要求电源有大电流续流能力，因此模块主电源要有不小于2A的额定电流输出能力，同时模块主电源输入端要有220uF钽电容（或470uF的铝电解电容）保证电源的续流能力。

### 1.5.7 整机设备重启后模块未正常工作，整机断电重启也不行

1) 确认模块复位是否正常，检查reset引脚电平电压。

2) 整机断电重启不行的可能原因是针对模块主电源断电时间较短导致模块主电源还未跌落到安全关机的电压以下，所以导致模块未真正复位。

  

1.6 SIM卡相关问题
------------

### 1.6.1 模块读取SIM卡失败，应该从哪些步骤着手处理查找问题？

出现模块不能读取SIM卡问题时，可以从以下几个步骤着手分析查找问题：

1) 用手机或者更换一个模块测试，确认SIM卡本身是否良好；

2) 检测SIM卡座接触性是否良好，SIM卡座管脚有没有虚焊或者管脚氧化；

3) 个别SIM卡对负载电容（分布电容+滤波电容）比较敏感，检查一下SIM卡管脚是否有滤波电容，如果有，将管脚滤波电容全部去掉，然后测试能否读卡；

4) 检查原理图和PCB，确认SIM卡封装的正确性，保证SIM卡管脚没有搞错顺序或者贴反SIM卡；

5) 使用示波器，查看SIM卡各管脚信号波形是否正常；

6) 辐射掉卡：去掉天线或者使用吸盘天线并将天线尽量远离放置，查看是否读卡，确认是否是天线辐射干扰问题。

7) SIM\_DATA引脚加10K上拉电阻到SIM\_VCC，确认上拉电阻是否正确，是否假焊、损坏等。

### 1.6.2 SIM卡部分滤波电容加多大比较合适？

目前市场上SIM卡制造商很多，且各制造商制作的SIM卡质量参差不齐，造成市场上SIM卡质量良莠不齐，但是通常情况下我司模块SIM卡管脚的滤波电容通常进行如下配置使用：SIMVCC管脚滤波电容使用0.1uF，SIM卡其他管脚滤波电容使用33pF。

### 1.6.3 SIM卡识别正常，但是注册网络失败

硬件上可从以下几方面着手分析：

1) 检查天线和PCB板焊接是否出现问题；

2) 更换天线或连接线进行对比测试；

3) 确认使用的天线类型是否匹配；

4) 检查天线的安装位置，是否接在GPS天线或辅路天线位置上。

5) 确认安装现场是否有运营商的网络信号，可更换其他运营商SIM卡或用手机进行对比测试。

确认安装现场附近是否有信号干扰源。

  

<span id='1'></span>

2 软件设计FAQ
=========

2.1 SIM卡相关问题
------------

### 2.1.1 是否可通过查询IMSI获取SIM卡号？是否有其他办法获取到SIM卡号？

模块自身可以获取到的SIM卡相关的信息如下：

1) 可以通过AT+CIMI获取IMSI号；

2) 通过AT+CCID获取ICCID号；

3) SIM卡号（即本机号码）需要提前先设置，然后才可以通过AT+CNUM可以获取本机号码，具体流程如下：

AT+CPBS="ON" //选择电话本，此电话本允许保存两个本机号码

OK

AT+CSCS="GSM"  //设置字符集

OK

AT+CPBW=1,"13808801234",129,"neoway" //写入电话号码和机主姓名

OK

AT+CNUM

+CNUM: "neoway","13808801234",129 //查询到的电话号码

OK

### 2.1.2 如何在程序内部确认SIM卡运营商？

1) 在注册上网络的情况下，GPRS模块可以通过AT+COPS？查询；

2) 4G模块可通过AT$MYSYSINFO及AT+COPS？查询

### 2.1.3 专网卡和公网卡有什么区别？

专网卡也称为私网卡，属于运营商为某集团客户搭建的用来内部通讯的网络，一般通过私有的APN来进行接入，所以公网卡和专网卡的主要区别可以从以下几个方面去总结：

1) 专网卡一般是从运营商那里定制的，接入APN，用户名、密码都是私有的，不对外开放的；

2) 专网卡一般不支持漫游；

3) 专网卡获取到的IP一般不能连接外部服务器，除非专网卡支持公网通信；

4) 专网卡的IP地址是固定的或在分配的IP段内随机获取，具体细节由运营商决定。

5) 公网卡的APN为CMNET(移动)和UNINET(联通)，一般情况下拨号时也要使用运营商对应的APN；

### 2.1.4 在获取SIM卡标识时，是否只检查到+CCID即可？

可以不用检查后面的数字。如果返回正确值可以只检查是否返回OK；如果没有SIM卡或SIM卡异常时，AT+CCID的返回值是ERROR。

读取到CCID只能表明产品已经识别到SIM卡了，但是如果SIM卡带有PIN码则需要输入PIN码才能正常使用，所以需要添加AT+CPIN？指令来进行PIN码的确认。

### 2.1.5 SIM卡没有欠费，信号也很好，AT+CREG? 返回为0，1，网络附着失败是什么原因？

AT+CREG？是判断语音业务的注册，要判断数据业务是否注册上需要用指令AT+CGREG？或者AT+CGATT?来查询。如果语音业务能注册的情况下，数据业务附着失败，需要检查SIM卡是否开通了数据业务，可以打电话给运营商客户中心咨询。

### 2.1.6 如何判断SIM卡是欠费还是无法注册网络？

如果SIM卡欠费，会导致注册网络失败，通过AT+CREG？ 查询返回值不是（0,1）or（0,5），但是注册网络失败不能作为SIM卡欠费的唯一判断依据。实际使用过程中，应该将SIM卡欠费作为分析SIM卡注册网络失败的一个原因来考虑。

### 2.1.7 SIM卡注册网络失败

注册网络失败首先确认模块识别Sim卡状态是否正常，卡的PIN码是否被打开（AT+CPIN?是否返回READY），SIM卡是否被锁定；再确认SIM卡是否欠费/激活，然后查看当地是否有运营商的网络信号。（注意：目前电信、联通已关停2G网络，注册电信、联通2G网络会失败）

1) SIM卡注网的等待时间是否足够长。查询网络注册状态指令AT+CREG?，建议间隔1秒查询1次，最少查询60次。

2) SIM卡是否和其他模块的IMEI编号绑定

3) 检查SIM卡的配置是否出现问题：通过运营商将好的卡和不良的卡的后台数据进行对调。

4) 检查SIM卡类型是否和模块匹配。

5) 用AT指令查询现场信号信噪比值、确认是否在正常范围内。

### 2.1.8 开机后发指令AT+CPIN?查询SIM卡PIN 状态，模块返回BUSY？

在刚开机后马上发AT+CPIN? 查询状态会返回BUSY，此时需等到模块输出开机回码“+PBREADY”后，再查询SIM卡PIN状态。

模块电话本准备完毕会输出开机回码“+PBREADY”

2.2 短信问题
--------

### 2.2.1 短信中心号码是怎么回事？

短信发送时使用存储转发模式，即短信先提交到短信中心，再由短信中心负责发送到目的手机。每张卡在发卡时，SIM卡中已经配置好短信中心地址(也是唯一正确地址)，一般情况不需要设置短信中心号码，使用时只通过 “AT+CSCA?” 指令来查寻短信中心地址，只有在查询到的短信中心号码错误时才启用中心号码设置。

### 2.2.2 短信中心号码是固定的吗？可以随意设置吗？

短信中心号码是固定的，不可以随意设置。

### 2.2.3 为何模块只能收短信，不能发短信？

这种情况应该是短信中心号码被更改，导致了模块只能收短信，不能发短信，重新更改为正常的短信中心号码后问题可以解决，通过AT+CSCA指令来修改。

### 2.2.4 模块收发短信的最大长度是多少？超过这个长度的短信怎么处理？

如果是**文本**模式发送短信，每条短信内容最多为**140**个字符。如果要发送内容超过这个长度，需要分成两条或者更多来发送。

如果是**PDU**模式发短信，7bit编码可以发送**160**个字符；UCS2编码可以发送70个汉字。如果短消息内容超过70个汉字，则可以采用长短信方式发送。

### 2.2.5 如果短信内容中含有1A，模块会在检测到1A就发送短信吗？

在PDU模式时模块发送短信会检测到短信长度的，只有检测到事先设置的长度后模块才会发送此短信。在发送PDU短信时，设置长度和实际输入的短信长度不一致，会导致短信发送失败。

![](file:///C:\Users\m1341\AppData\Local\Temp\ksohtml\wps9E76.tmp.jpg) 

1A是Ctrl+Z的编码。

2.3 数据业务相关问题
------------

### 2.3.1 模块内部协议栈与外部协议栈有何区别？

外部协议栈就是客户终端自己的协议栈，客户终端需要处理PPP/IP/TCP/UDP等协议中的各类数据帧和协商流程，常见的使用方式为：CMUX+PPPd。

内部协议栈是模块自己的协议栈，即模块内置协议栈，模块内部已经将PPP/IP/TCP/UDP/FTP等协议封装成AT命令，客户终端只要给模块发送对应的AT命令就可使用对应的协议，而不需要处理各类协议中的数据帧和协商流程。

### 2.3.2 PPP拨号失败的原因有那些？该如何排查确认？

模块进行PPP拨号的硬件条件主要有：天线、模块、SIM卡。在具体执行拨号的过程中，造成拨号失败的主要原因有：模块未读到SIM卡、信号强度较弱、网络没有注册、该卡没有开通数据业务、该卡数据业务欠费、网络没有数据附着、参数设置（APN、用户名、密码、PPP协议选项配置）错误等。结合上述影响PPP拨号的因素，我们可以按照如下流程进行PPP拨号失败问题分析：

确认SIM卡数据业务是否正常，以4G模块为例，需要确保SIM卡允许注册的网络制式，都开通数据业务。（举例：SIM卡可能2G/3G业务是正常的，但是未开通4G业务；SIM卡只开通了公网业务，未开通专网业务；SIM卡的数据业务被限制只能连接固定服务器）

1) 确认SIM卡是否可用，可以通过AT+CPIN?查询，若返回+CPIN：READY，则SIM卡可用；反之，则SIM卡不可用（SIM卡检测失败，SIM卡PIN码激活）。

2) 确认信号强度是否正常，建议信号强度达到15 以上（通过AT+CSQ确定）再进行拨号。

3) 确认网络注册是否正常（通过AT+CREG? 查询）

4) 确认网络数据业务是否附着（通过AT+CGATT? 查询）

5) 确认卡是否欠费（拨打运营商服务电话查询）

6) 确认APN是否正确（确认大小写正确、检查是否有多余的空格出现）

7) 确认用户名和密码设置是否正确（确认大小写正确、检查是否有多余的空格出现）

8) 确认认证方式是否合法（通过AT+XGAUTH指令设定鉴权类型：NONE 、PAP、CHAP）

9) 确认拨叫号码是否正确；

10) 确认SIM卡是否与模块绑定（部分新SIM卡会跟模块IMEI编号绑定，更换其它模块使用就会造成拨号会失败，需要运营商后台解绑）

11) 用AT指令查询现场信号信噪比值、确认是否在正常范围内。

### 2.3.3 模块信号正常，网络注册正常，但是通讯一段时间后，就会突然收到“$MYURCACT：0,0”或者“GPRS DISCONNECT”，是什么原因？ 

部分地区网络，会在固定时间间隔内释放IP，IP被释放后模块会有回码提示“$MYURCACT：0,0”。在模块应用上的表现就是模块突然掉线，而且掉线的时间间隔很有规律。针对该问题，可以按如下方式处理：

1) 咨询当地电信运营商，在使用正确APN的前提下，请运营商去除强制释放IP的机制，或者使用专网卡测试。

2) 为确保终端能正常运行，终端需要有重新拨号机制，即：在检测到PPP连接断开之后， 能重新建立PPP连接。

3) 如果使用电信NB SIM卡，电信限制必须连接电信云平台使用，如果直接连接其他服务器测试，发送几包数据后就会被电信强制断开连接，模块上报“GPRS DISCONNECT”

### 2.3.4 国网指令PPP拨号的时候总是返回ERROR：981，是什么原因？

在进行PPP拨号之前需要确保模块已经读到SIM卡，并且注册上网络，可以通过At+CREG?/AT+CGREG？确定模块是否已经注册上网络。只要在注册上网络的情况下，才能提高拨号成功率。

### 2.3.5 模块获取到IP后，无法建立TCP连接的原因有那些？

1）服务器侦听未打开。

2）对于公网卡来说，服务器IP不是外网IP，也会造成TCP连接建立失败。

3）对于专网卡来说，模块获取到的IP是公网IP也会造成TCP连接建立失败。

### 2.3.6 如何识别TCP假连接问题？

所谓假连接是指当前的TCP Socket链接是假的，不能正常进行数据通讯，假连接是双向的，有可能是客户端假连接，也可能是服务器端假连接，假连接是一种普遍现象，一直存在，导致假连接的几种情况是：

1) **心跳包时间设置周期长**：终端在建立TCP连接后，如果长时间没有进行数据收发，运营商因为网络容量的原因，会强行释放IP地址资源，运营商释放IP地址资源是不会通知终端的，从而导致出现假连接。为了保持TCP连接不被释放IP地址资源，终端需要定时发送心跳包。如果心跳包时间设置周期过长，依然会导致运营商释放掉IP地址资源。目前国内发达地区的移动终端数量越来越多，导致运营商的网络越来越拥挤，从而导致心跳包时间越来越短，因此容易出现IP地址资源被运营商强制释放的情况，有的地方心跳时间可以长达10分钟，有的地方是1分钟甚至更短；

2) **当地网络质量差**：终端或者服务器在关闭TCP连接时，由于当地网络质量差，导致关闭指令没有下达；

3) **服务器异常断电关机**：终端没有接收到连接关闭的指令，导致终端一直认为连接是正常存在的；

4) 实际应用中假连接判断方法：

a) 09标准指令版本：发送TCP数据后，使用AT+IPSTATUS=<socket ID\>命令查询模块发送缓冲区，如果出现的值不是2047，则说明当前的数据包还没有发送出去，如果连续间隔2秒，查询5次仍然不是2047，说明当前的链接有问题，需要主动断开，重新建立；

b) 13国网指令版本：发送TCP数据后，使用 AT$MYNETACK查询ACK的值，如果当前值不是0，说明还有数据包没有收到ACK应答，如果连续间隔2秒，查询5次仍然不是0，说明当前的链接有问题，需要主动断开，重新建立。

### 2.3.7 如何实现TCP长连接（实时在线）？

长连接是通过终端定时发送数据给主站（心跳包）来保持的，导致无法长连接的原因有运营商基站释放资源和目标主站服务器的定时释放资源导致。

### 2.3.8 终端在使用过程中，TCP出现掉线，软件设计上应注意哪些问题？

如果TCP掉线后，重新连接3次仍然失败，则需要复位模块。软件设计上需要注意的有下面几点：

1) 为了保持终端数据业务长期在线，终端需要定期发送心跳包，并且心跳包的间隔时间尽量不要过长（每个地区的心跳包时间都不一样的，并且跟当地网络的拥挤有很大关系，网络越拥挤，心跳包时间越短）；

2) 建议定时查询CSQ，信号弱也是设备掉线的主要原因：

a) 如果CSQ正常，出现掉线，那么主要是网络异常引起，比如周围有干扰源，或者是这个时间段网络非常拥挤，尤其是打电话的用户过多，一定会影响到数据业务（备注：运营商的网络是优先语音业务的，网络拥挤时，首先会保证语音用户的使用）。

b) 如果CSQ很弱，那么更换增益更好的天线，或者反馈给运营商，请运营商增加基站发射信号或者在周围增加直放站。

3) 出现掉线后，需要关掉TCP连接，重新进行连接PPP连接，然后进行TCP连接。

4) TCP掉线有主动掉线、被动掉线和异常掉线：

a) 主动掉线，就是终端自己发送关闭指令；

b) 被动掉线，一般是服务器端发送了断开指令，因此终端要进行判断；

c) 异常掉线，SIM卡运营商网络或者服务器网络出现异常或者阻塞导致，这时候要根据假连接机制判断；

### 2.3.9 与服务器建立TCP/UDP连接时，为什么使用域名无法建立连接?

如果将域名更换为对应的IP地址后建立链接可以成功，就可以确认为DNS域名服务器解析时出现了问题，使用指令AT+DNSSERVER=<n>,<dns-ip><CR>更换DNS域名服务器后即可以正常工作。

如使用阿里云平台进行开发，平台上有对解析线路（电信、移动、联通）进行选择的选项，注意需与测试的SIM卡匹配，选错会导致域名解析失败。

2.4 模块APN设置问题
-------------

### 2.4.1 模块支持设置多少字节的APN、用户名、密码？

不同的模块会有差异，目前模块支持的APN、用户名、密码最大长度均至少为32字节。

### 2.4.2 在设置APN时，是否要针对移动和联通卡做不同设置？

建议做区分设置，中国移动公网APN为CMNET，中国联通公网APN为UNINET，另外要注意现在的物联网卡的APN信息，与普通SIM卡有区别，比如中国移动物联网卡为CMIOT。

### 2.4.3 APN是什么？

**APN****（****Access Point Name****）**指一种网络接入技术，它决定了设备通过哪种接入方式来访问网络；APN通常作为用户签约数据存储在HSS/HLR中，用户在发起分组业务时向网络侧SGSN/MME提供APN，SGSN/MME根据用户所提供的APN，通过DNS进行域名解析，从而获取到GGSN/PGW的IP地址，将用户接入到APN对应的PDN中。

2.5 RIL库相关问题
------------

### 2.5.1 安卓系统开机后，屏幕上的信号识别等待时间太久。

如果系统里的ttyUSB接口很多，在未指定AT接口的情况下，程序会逐个ttyUSB接口测试导致浪费时间。

根据这个情况，我们在程序中强制指定一个AT口，系统启动时网络信号即已成功识别。

### 2.5.2 N720 RIL库，使用ppp拨号，有信号图标，显示有感叹号，不能正常联网

1) 确认SIM卡是否正常识别，注册网络附着网络有没有成功；

2) 检查在 apns-conf.xml 文件里是否添加了SIM卡对应的 APN；

3) 确认chat文件版本，替换/system/bin目录下的chat文件测试

4) 检查/etc/ppp/目录下ip-up文件是否存在

5) 检查内核里是否配置了ppp协议相关的选项

### 2.5.3 使用ppp拨号网络传输速率较慢

在网络速率要求较高的场合，推荐使用NDIS拨号和GOBINET拨号，在网络环境良好的情况下，上下行速率性能对比ppp会有较大的提升。

2.6 串口波特率是多少？
-------------

如果没有特殊说明，模块默认波特率自适应，模块上电开机后不会输出任何提示信息，需要先发送AT指令给模块，模块会自动切换到与外部一样的波特率。  

3 其他FAQ
=======

3.1 基站饱和是什么原因？
--------------

安装基站时，是有一个网络容量上限。随着移动用户的增长(包括各种物联网设备的安装)，基站容量逐渐趋于饱和，这样就会导致如下情况出现：

1) 很难获取到IP地址

2) 正常设备很容易掉线

3) 假连接频频发生

4) 很难注册网络

3.2 假基站的原理
----------

假基站与真基站的功能十分相近，但假基站是某些人或组织为了实现特殊目的而安装的，它并不被运营商承认，也没办法提供移动业务。它利用移动设备优先选择信号质量好的基站进行注册的机制，盗取移动用户的信息或者批量下发垃圾信息。

3.3 AT指令应用问题
------------

### 3.3.1 EVB板工作正常，但发送AT指令时模块无响应/报错。

AT指令发送需在末尾加回车换行（注意先回车后换行\\r\\n），例如：AT+CSQ\\r\\n

### 3.3.2 语音来电用ATH指令概率性无法挂断？

语音电话挂断建议在ring上报后延迟2s后再发指令进行挂断。

### 3.3.3 模块注册网络正常，CSD业务工作有问题

使用CSD业务时，需要将网络制式锁定到GSM，否则无法正常使用。（其他网络制式不支持CSD业务）


