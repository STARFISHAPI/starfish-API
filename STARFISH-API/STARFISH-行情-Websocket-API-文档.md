
# 行情

行情是一个完全公开的api，当前只提供了WebSocket的api。WebSocket协议是基于TCP的一种新的网络协议。它实现了客户端与服务器之间在单个 tcp 连接上的全双工通信，由服务器主动发送信息给客户端，减少了频繁的身份验证等不必要的开销。服务器不再是被动的接到客户端的请求后才返回数据，而是有了新数据后主动推送给客户端。WebSocket 只支持行情查询，交易接口将在后续提供.

# 请求与订阅说明
## 1.访问地址

行情请求地址为：wss://marketinfo.starfish.com

## 2.数据包描述:

	数据会以2进制流方式传输, 每次上行及下行数据都包含一个特定包头格式:
		4字节 uint32 包体总长度;		
		4字节 uint32 请求的命令ID;		
		2字节 uint16 预留2字节;

### 命令ID列表   
 
    用户主动发送的上行命令ID列表	
>  {
>
>      心跳请求命令ID		          : 6553601,
> 	
>      请求订阅某市场的某币种命令ID         : 6553602,
> 	
>      请求k线命令ID                       : 6553606,
> 	
>      请求订阅的市场币种的24小时数据命令ID  : 6553610, 
>
>      请求24小时币种变化命令ID             : 6553609,
> 	
>  };


    服务端 主动下发的下行命令ID列表
> {
>
>      下发的成交信息命令ID             : 6553603,	
>
>      下发的挂单数据命令ID             : 6553604,	
>
>      下发的24小时币种变化命令ID       : 6553605,	
>
>      返回k线数据命令ID                : 6553607,	
>
>      当前分钟k线变化命令ID            : 6553608,
>
>  }

## 3.交互命令

> **所有发送的字符串都需要在字符串流之前以16位(2字节)字符存储字符串长度.**

### 1.	心跳. 客户端需要每隔30秒向服务器发送一次心跳,如果距离上一次心跳60秒没有收到新的心跳包,服务器将断开连接

    uint32 `client_ts`; 客户端时间戳

### 2.	订阅特定币种信息. 订阅成功后,下发该币种的挂单数据,成交数据,当前币种24小时市场统计数据

    string `market`; 市场名称
    string `coin`; 币种名称

### 3.	订阅特定职场所有币种24小时信息, 订阅成功后,下发特定市场中所有币种的24小时市场统计数

    string `market`; 市场名称

### 4.	获取K线信息

    string `market`; 市场名称
    string `coin`; 币种名称
    uint16 `type`; k线类型 1:分时图, 2:分钟线, 3:小时线, 4:日线 , 5:周线
    uint16 `offset`; k线跨度 分钟线(1,5,15,30), 小时线(1,2,4,6,12),日线和周线(1)
    uint32 `time`; k线结束时间戳 0:当前时间


### 5.	服务器下发挂单数据

    String: {
			"n":"市场名:币种名",
			"b":[[价格,数量]],		//买单数组
			"s":[[价格,数量]		//卖单数组
			]
			}

### 6.	服务器下发成交数据
    String: [
			[
				价格,
				数量,
				时间戳,
				买卖类型(1买2卖)
			]
		]

### 7.	服务器下发当前币种24小时市场统计

    String: [最高价,最低价,成交量,涨跌百分比,显示名称,最新价, 人民币价值,美金价格,比特币价值]

### 8.	服务器下发当前分钟k线

    string  市场币种名称
    string: [最高价,最低价,成交量,涨跌百分比,显示名称,最新价, 人民币价值,美金价格,比特币价值]

### 9.	服务器下发K线信息

    string `market`; 市场名称
    string `coin`; 币种名称
    uint16 `type`; k线类型 1:分时图, 2:分钟线, 3:小时线, 4:日线 , 5:周线
    uint16 `offset`; k线跨度 分钟线(1,5,15,30), 小时线(1,2,4,6,12),日线和周线(1)
    uint32 `time`; k线结束时间戳
    String: [[时间戳,开始价格,最后价格,最低价格,最高价格,总额]]

# 代码示例:

    在使用前请引用"Int64.js"和starfishSDK.js"文件
    下方例子提供以下API
###  订阅特定币种信息

    <script type="text/JavaScript" src="Int64.js"></script>
    <script type="text/javascript" src="starfishSDK.js"></script>
    <script *ype  ="text/javascript">
    sdk = new threeExSDK({ 
    host : "wss://marketinfo.starfish.com/", 
    });  
    sdk.connect();  
    sdk.subInfo('USDT', 'BTC'); 	//订阅特定币种信息
					//参数
 					//market: 市场名称
					//coin:	币种名称
					//
  					//订阅成功后,下发该币种的挂单数据,成交数据,当前币种24小时市场统计数据

     //获取到挂单数据
     fn.MarketOrder=function(res){
		console.info("挂单数据:",res);
		/*{
			"n":"市场名:币种名",
			"b":[[价格,数量]],	//买单数组
			"s":[[价格,数量]		//卖单数组
			]
			}*/
	};

     //获取到成交数据
     fn.MatchSuccessInfo=function(res){
		console.info("成交数据:",res);
		/*
		[
			[
				价格,
				数量,
				时间戳,
				买卖类型(1买2卖)
			]
		]
		*/
	};


    //当前币种24小时市场统计
    fn.MarketWatch24HoursData=function(res){
		console.log("当前币种24小时市场统计:",res);
		/*
		[最高价,最低价,成交量,涨跌百分比,显示名称,最新价, 人民币价值,美金价格,比特币价值]
		*/ 
	};
    </script>

### 订阅特定职场所有币种24小时信息

    <script type="text/JavaScript" src="Int64.js"></script>
    <script type="text/javascript" src="starfishSDK.js"></script>
    <script *ype  ="text/javascript">
    sdk = new threeExSDK({ 
    host : "wss://marketinfo.starfish.com/", 
    });  
    sdk.connect();    
    sdk.subMarketInfo('USDT');	//订阅特定市场的所有币种24小时统计信息
				//参数
				//market: 市场名称
				//
				//订阅成功后,下发特定市场中所有币种的24小时市场统计数据

    //获取的24小时市场各币种统计
    fn.Market24HoursData=function(res){
		console.log("24小时市场各币种统计:",res);
		/*
		{"市场名:币种名":[最高价,最低价,成交量,涨跌百分比,显示名称,最新价, 人民币价值,美金价格,比特币价值]}
		*/
	};
    </script>

### 获取K线信息

    <script type="text/JavaScript" src="Int64.js"></script>
    <script type="text/javascript" src="starfishSDK.js"></script>
    <script *ype  ="text/javascript">
    sdk = new threeExSDK({ 
    host : "wss://marketinfo.starfish.com/", 
    });  
    sdk.connect();    
	sdk.klineInfo('USDT', 'BTC', 2, 1, 0); 	//获取k线信息
						//参数
						//market: 市场名称
						//market: 市场名称
						//coin:	币种名称
						//type: k线类型 1:分时图, 2:分钟线, 3:小时线, 4:日线 , 5:周线
						//offset: k线跨度 分钟线(1,5,15,30), 小时线(1,2,4,6,12), 日线和周线(1)
						//time: k线结束时间戳 0:当前时间
						//
						//返回k线信息
  
    //当前分钟K线
    fn.MarketKLineCurrent=function(res){
		console.log("当前分钟k线:",res);
		/*
		["U市场名:币种名","[[时间戳,开始价格,最后价格,最低价格,最高价格,总额]]"]
		*/
		
	};
    </script>	
