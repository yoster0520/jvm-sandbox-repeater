# slogan例子发生了什么？
## 本地standalone工作
默认情况下，standalone会从本地读取配置，默认的存储目录为：`${USER_HOME}/.sandbox-module/cfg/`
### 录制
```shell
curl -s 'http://127.0.0.1:8001/regress/slogan?Repeat-TraceId=127000000001156034386424510000ed'
```

1. `com.alibaba.jvm.sandbox.repater.plugin.http.HttpStandaloneListener`该类是Http类接口的录制的入口代码。
2. 当一个Http请求进入时， repater监控到有方法调用，并且是Http请求，具体的请求会流转到该类的`doBefore`方法，该方法会判断该请求是否是属于*录制*请求，并且*非回放*请求，并标记开始录制。 
3. 在录制结束后，会调用`doReturn`方法，该方法会将该请求所经过的*所有需录制请求*生成invocation对象，并投递到分发队列（`com.alibaba.jvm.sandbox.repeater.plugin.core.impl.standalone.StandaloneBroadcaster`）中。 
4. `StandaloneBroadcaster`会将请求保存到本地的`${USER_HOME}/.sandbox-module/cfg/repeater-data`中
> 文件的序列化方式为 hessian
### 回放

