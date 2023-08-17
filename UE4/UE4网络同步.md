# UE4网络同步

Actor 主要通过两种方式进行更新：

- 属性更新
- RPC （远程过程调用）。

属性更新和 RPC 的主要区别在于，属性可以在发生变化时随时自动更新，而 RPC 只能在被执行时获得调用更新。

其中，最具代表性的一类应当复制的属性就是 Actor 的健康值。每当健康值发生变化时，您通常都希望告知客户端。如果健康值没有变化，则不会发送任何数据。这样做很有效率。但需要记住的是，即使这个属性没有变化（因此不消耗任何带宽），它仍然会消耗 CPU 资源来判断这个值是否发生变化。因此，这适合那些经常变化的属性。

有关属性的更多信息，请参见 [属性复制](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/Networking/Actors/Properties) 文档。

RPC 的例子包括：让所有客户端在某个位置看到同一场爆炸。您可以以位置和半径为参数的 RPC 函数，同时在每次发生爆炸时调用它。您也可以将此存储为一组属性，通过同步的方式将其传达给客户端，但这种做法会损失一些效率，因为爆炸出现的频繁度也许不会高得有必要将它们作为属性。

有关 RPC 的更多信息，请参见 [RPC](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/Networking/Actors/RPCs) 文档。

https://zhuanlan.zhihu.com/p/483485488