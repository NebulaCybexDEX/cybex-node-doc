## 访问Cybex节点
* Cybex作为公链交易所，提供了丰富的API，在无需任何授权的情况下，供用户访问节点上的相关数据。用户可以通过Cybex提供的公共接入点访问，也可以访问自己运行的全节点。
* 数据分为核心账本数据和插件账本数据。核心账本数据包含如账户数据、资产数据、活跃订单数据、账本余额数据等，这些数据会影响交易执行时的行为。插件账本数据包含行情数据、账号历史操作数据、历史订单数据等，这些数据不影响交易执行时的行为。
* API分为若干大类，如Database API, History API, Network Broadcast API等，每一类API包含若干RPC方法，用户通过调用API下的方法获取节点的数据。其中，Database API包含所有核心账本数据及少量插件账本数据的访问。Database API可通过http和websocket的方式访问，其他API均只能通过websocket的方式访问。

## 公共接入点
* hongkong.cybex.io
* apihk.cybex.io
* normal-hongkong.cybex.io

## 索引
* 账号、资产、订单簿、币龄 [Database API]()
* K线、账号操作历史、成交历史 [History API]()
* 订单历史 [Limit Order Status API]()
* 资产持仓排名、持仓明细 [Asset API]()

## API调用方式举例
### Http方式
Http方式仅能访问Database API。
```Bash
curl --data '{"jsonrpc":"2.0","method":"$FUNCTION","params":[$PARAMS], "id":1}' $ACCESS_POINT
```
以通过用户名查询账户为例，调用Database API的get_account_by_name方法，该函数共有一个参数，为以字符串表示的用户名。  
调用请求:
```Base
curl --data '{"jsonrpc":"2.0","method":"get_account_by_name","params":["harley"], "id":1}' $ACCESS_POINT
```
返回信息为json字符串，"id"为请求中的"id"的值，该值仅用于供客户端识别请求和返回的对应关系，节点并不检查id的唯一性或递增性，请求的返回中的"id"与请求中的"id"一致。
```json
{"id": 1, "jsonrpc": "2.0", "result": {}}
```

### Websocket方式
Websocket方式可以访问节点所有开启的API。访问RPC方法前，需要向节点请求API ID，在websocket连接维持期间，可以使用该API ID调用API下的所有方法。
```Bash
wscat -c wss://hongkong.cybex.io
> {"method": "call", "params": [1, "$APINAME", []], "id": 1} # send to node
< {"id":1,"jsonrpc":"2.0","result":$APIID} # receive from node, extract APIID from "result"
> {"method": "call", "params": [$APIID, "$FUNCTION", [$PARAMS]], "id": 2} # send to node, call api function
< {"id":2,"jsonrpc":"2.0","result": $RESULT} # receive result of rpc call
```

以查询K线为例，调用history api的get_market_history方法，该方法需要5个参数
```Bash
wscat -c wss://hongkong.cybex.io
> {"method": "call", "params": [1, "history", []], "id": 1}
< {"id":1,"jsonrpc":"2.0","result":2}
> {"method": "call", "params": [2, "get_market_history", ["1.3.2", "1.3.0", 86400, "2019-07-29T00:00:00", "2019-08-01T00:01:00"]], "id": 2}
< {"id":2,"jsonrpc":"2.0","result":[...]}
```

## 索引
* Database API
  + [通用](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#通用)
    + [get_objects](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#get_objects):使用对象id查询对象
  + [账户](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#账户)
    + [get_accounts](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#get_accounts):通过账号id批量查询账号
    + [get_full_accounts](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询多个账号的全量信息
    + [get_account_by_name](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):通过账号名查询账号基础信息
    + [get_account_references](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询账号参与的其他账号权限管理
    + [lookup_account_names](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):通过账号名批量查询账号
    + [lookup_accounts](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):列出账号名
    + [get_account_count](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询全网账号总数
  + [资产](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#资产)
    + [get_assets](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):使用资产id批量查询资产
    + [list_assets](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):根据字典序列出资产
    + [lookup_asset_symbols](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):使用资产名批量查询资产
  + [行情](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#行情)
    + [get_limit_orders](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询订单簿
    + [get_ticker](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询最新行情
    + [get_24_volume](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询24小时成交量
    + [get_order_book](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询订单簿，以用户可读形式返回
    + [get_trade_history](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询成交历史
    + [get_trade_history_by_sequence](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):遍历成交历史
  + [权限和验证](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#权限和验证)
    + [get_transaction_hex](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):序列化交易
    + [get_required_signatures](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取交易所需签名
    + [get_potential_signatures](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):查询可以签名交易的公钥
    + [verify_authority](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):验证交易签名
    + [validate_transaction](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):验证交易
    + [get_required_fees](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取操作最低上链手续费
  + [公钥](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#公钥)
    + [get_key_references](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取公钥的账号绑定
    + [is_public_key_registered](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):检查公钥是否被绑定了账号
  + [拟议交易](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#拟议交易)
    + [get_proposed_transactions](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取账号的拟议交易
  + [区块和交易](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#区块和交易)
    + [get_block_header](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取区块头
    + [get_block_header_batch](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):批量获取区块头
    + [get_block](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取区块
    + [get_transaction](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取交易
  + [全局参数](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#全局参数)
    + [get_chain_properties](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取链的不可变参数
    + [get_global_properties](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取链的可变参数
    + [get_config](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取静态配置参数
    + [get_chain_id](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取链id
    + [get_dynamic_global_properties](https://github.com/CybexDex/cybex-node-doc/blob/master/api/database-api.md#):获取链的动态参数
* History API
#### 祝您交易愉快