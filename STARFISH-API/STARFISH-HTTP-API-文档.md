# 介绍
通过了解以下信息，您可以使用STARFISH提供的API来接入交易平台进行操作
# 认证
访问需验证身份的接口需要：

STARFISH使用API key 和 API secret 进行验证，请访问用户中心申请API key 和 API secret

并使用API secret对传递`QUERY_STRING`进行签名。
# 访问限制
每秒10次
# HTTP接口method列表

深色字体method表示接口提供给公网不需API key 与 API secret 签名 TOKEN 验证
> * _order 限价挂单_
> * _orderlist 委托订单列表_
> * _priceorder 市价挂单_
> * _repealorder 撤销挂单_
> * _getbalance 获取用户余额_
> * _ordersuccesslist 成功交易列表_
> * **getservertime 获取服务器时间戳**
> * **pairlist 交易对列表**
> * **coinlist 币种列表**
> * **getcoinprice 获取币种价格 **

# 定义与Token签名方式

`HOST`: `https://api.starfish3.com`

`QUERY_STRING`: `/api/{method}?apikey={apikey}&timestamp={timestamp}&parameters`

`Token `: `MD5(QUERY_STRING + API secret)`

完整请求URI:

`https://api.starfish3.com/api/{method}?apikey={apikey}&timestamp={timestamp}&parameters&token={token}`

# 例子

请求挂单接口

method : `order` 

api key : `u2CBrhpnMBKhfGAsWvCw`

api secret : `TVE63QUT8JJE8YRZHO7TZHPFCZUVVNXZ5JZGEIJJ`

timestamp : `1532424365`

market : `BTC`

coin : `ETH`

price : `0.05873`

count : `1.173`

type : `1`

得到 `QUERY_STRING`

`/api/order?apikey=u2CBrhpnMBKhfGAsWvCw&timestamp=1532424365&market=btc&coin=eth&price=0.05873&count=1.173&type=1`

`MD5(QUERY_STRING + api secret)`得到 `token`

3db176fc607995309ed95fe6562d93da

得到完成`REQUEST_URI`

`https://api.starfish3.com/api/order?apikey=u2CBrhpnMBKhfGAsWvCw&timestamp=1532424365&market=btc&coin=eth&price=0.05873&count=1.173&type=1&token=3db176fc607995309ed95fe6562d93da`


# 说明

method：调用方法

timestamp：时间戳，需要和服务器之间的时间相差小于20秒

所有的参数链接，都为小写字母
# 公开接口
### 查询服务器时间

`https://api.starfish3.com/api/getservertime`

### 查询可用交易对

`https://api.starfish3.com/api/pairlist`

# 非公开接口API
STARFISH的API请求，除公开的API外都需要使用APIkey 以及 使用secret签名生成的Token
### order 普通挂单 

order 接口入参

  `price     单价`

  `count     数量`

  `market    市场`

  `coin      币种`

  `type enum (1,2)1买2卖`

### priceorder 限价挂单

priceorder 接口入参

`market    市场`

`coin      币种`

`type enum (1,2)1买2卖`

`amount    总量`

### orderlist 订单列表

orderlist 接口入参

  `market  :  市场`

  `coin  :  币种`


### ordersuccesslist 成交订单列表
ordersuccesslist 接口入参

`page      页码`

`offset    需要获取的行数`

`market    市场 （非必传）`

`coin      币种（非必传）`

`type enum (1,2)1买2卖（非必传）`


### repealorder 撤销订单

repealorder 接口入参

`m_id   ：  订单id`

### getbalance 获取余额信息

`getbalance 接口入参`
不需要其他参数

### getcoinprice 获取交易对价格
 getcoinprice 接口入参

`market   ：  市场`
`coin     ：  币种`
getcoinprice 返回值 JSON 
status: 200-成功 300-失败
data：交易对 币种价格
msg： 

