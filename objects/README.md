## 常用节点数据库对象
节点数据保存在对象数据库(object database)中，每个数据库对象具有唯一ID标识，ID以"x.y.z"的形式表示，xyz均为无符号整数。其中x.y表示该对象的类型，z表示该对象是该类型下的第几个对象。例如，1.2表示账户类型，1.2.0表示第一个账户，1.2.9999表示第10000个账户。同类型的对象拥有相同的属性字段。所有对象均可以使用[Database API]()的get_objects方法查询。这里仅介绍常用的节点数据库对象。

[账号](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/account.md)  
[资产](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/asset.md)  
[理事会成员](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/committee_member.md)  
[见证人](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/witness.md)  
[订单](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/limit_order.md)  
[锁定期](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/vesting_balance.md)  
[持仓](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/account_balance.md)  
[锁仓](https://github.com/CybexDex/cybex-node-doc/blob/master/objects/htlc.md)
