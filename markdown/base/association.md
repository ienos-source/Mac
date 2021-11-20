# Association

关联对象如何进行内存管理，关联对象如何实现 weak 属性?

关联对象的 ObjectAssociation 中有两个属性 (uintprt_t _policy, id value)，_policy 包含 retain，assign copy，会对应的对象进行和普通对象一样的内存管理操作
