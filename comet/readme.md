comet
======

`comet` 的是连接层，主要用来hold住长链接请求，主要作用是给 client push 数据.

首先要理解几个概念之间的联系

* server 
    - bucket (list)
        + channal (map)
        + room (map)
    - round 
    - operator

同时启动tcp server 和 websocket server, 监听，还要启动rpc。

- tcp server 
- websocket server 
- rpc server, 用来接受 job 的 push请求, 可以启动多个


**websocket 连接上来之后怎么存储连接的?**

[websocket.go](./websocket.go#127)

```golang
    if p, err = ch.CliProto.Set(); err == nil {
		// 连接之后获取获取房间等信息
		if key, ch.RoomId, hb, err = server.authWebsocket(conn, p); err == nil {
			b = server.Bucket(key)
			err = b.Put(key, ch)
		}
	}
```

[logic.log](./logic.go#47) , 通过 rpc 到 logic 模块获取房间，key的分配数据

```golang
func connect(p *proto.Proto) (key string, rid int32, heartbeat time.Duration, err error) {
	var (
		arg   = proto.ConnArg{Token: string(p.Body), Server: Conf.ServerId}
		reply = proto.ConnReply{}
	)
	if err = logicRpcClient.Call(logicServiceConnect, &arg, &reply); err != nil {
		log.Error("c.Call(\"%s\", \"%v\", &ret) error(%v)", logicServiceConnect, arg, err)
		return
	}
	key = reply.Key
	rid = reply.RoomId
	heartbeat = 5 * 60 * time.Second
	return
}
``` 


**rpc 怎么接收 job 模块的调用的，找到对应的 channel 然后push 数据给 client?**