# 无连接无线数据交换

这是一个 p2p 底层通讯函数库，用以在一定区域内的所有设备广播信息，无论这些设备是已经连接还是没有任何连接。
该库有两个子库，他们有类似的API，第一个是最简单的，它只能单向广播数据或者侦听广播的数据，第二个是复杂点的可以双向数据交换。

* wireless-simple
  * broadcast
  * listen
* wireless-advance

## wireless-simple

* `broadcast(char* msg)`: broadcast message once.
  * `msg`: the message to broadcast.
  * returns
    * Integer: 0 means successful.
* `listen(integer timeout)`: listen a message arrived until timeout.
  * `timeout`: the timout ms.
  * returns:
    * String: message string if successful.

对广播方没有要求，但接收方必须能够:

* 进入 Wifi 监控模式[Monitor Mode] or Promiscuous 模式.
* 切换信道的能力
* 至少能提供精度为 100ms 的定时器中断

注:

* 在某些操作系统中，如FreeBSD,你可以通过选择适当的(802.11 link-layer)头类型捕获 non-data packets。
* 某些无线网卡进入Promiscuous模式，可能只会转发相同SSID的packet.

### 实现原理

根据IEEE802.11无线网络协议，无线网络数据IP包是以802.2格式进行封装的，其中除数据本身外的，其它字段
都是明文发送的，因此发送方可以利用MAC地址，数据长度等进行编码传输数据，不过变更MAC地址在大多数操作系统
中都需要ROOT权限，所以最方便发送方利用的是数据长度字段。由于MTU的限制(不同路由器默认MTU不一样)，
可以利用的数据长度最好限制在8-9bit.

由于空间可能存在很多wifi设备，而接收方同一时间只能监听某一信道，所以，发送方必须在发送数据前，发送一段
固定的引导数据，供接收方识别是否在该信道上存在广播数据。另外发送方的UDP数据包长度是经过了(WPA2, WEP)不同
模式的封装发出的，所以实际的Length值会存在差异。由于引导部分的值是事先约定好的，可以通过引导部分来差出
这个差异值。

*注意*： 这个已经被腾讯申请了专利, 在实际使用中应该避开腾讯的[专利][https://www.google.com/patents/CN104144086A?cl=zh]:
  * 避开专利要求：在Promiscuous 模式下进行监听。
  * 或者避开权利要求10: 改变控制字段的识别字段的字节数。

* Promiscuous Mode:
  Capture packets on a network that you have connected to. This is likely what you need to be in if you want to analyze packets (Wireshark, tcpdump, etc.)
* Monitor Mode:
  Capture packets regardless of connected network. No association to AP needed (and no authentication). Because it is not connected to a network, you can't process the Ethernet info.

## wireless-advance

实现原理：利用IEEE802.11的 beacon frame 和 Probe request/response frame 进行数据交换。
详细可参阅: https://books.google.com.hk/books?id=qzygBQAAQBAJ
该方法如可进一步延伸可以用于室内定位处理，用途更广泛。


