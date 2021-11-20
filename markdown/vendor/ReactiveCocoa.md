# ReactiveCocoa

函数响应式框架

信号、订阅

ReactiveCocoa 的核心类 RACSignal

信号 RACStream <- RACSignal

RACDynamicSignal、RACReturnSignal、RACEmptySignal、RACErrorSignal

RACStream 抽象

- empty
- return
- bind
- concat
- zipWith

信号代表一连串的状态

1 -> 2 -> 3 -> 4 -> end

在状态改变时，对应的订阅者 RACSubscriber 就会收到通知执行相应的指令

订阅 RACSubscriber

start RACSignal -> subscribeNext: -> RACSubscriber -> sendNext: (Block 调用) -> sendCompleted

initialize RACSubscriber -> invoke didSubscribe with (Block 成员变量持有)
