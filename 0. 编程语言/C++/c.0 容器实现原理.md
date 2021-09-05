vector是线性容器，因此在扩容的时候，它会申请一块更大的内存，然后将当前的数据复制到新的内存地址，释放之前的内存，一旦内容重新分配，vector的所有迭代器都会失效。

`size`是vector中当前元素数量，`capacity` 是vector的最大容量，初始capacity是0，放入一个元素后capacity是1，在gcc中以2倍扩容。

resize不会减小capacity，reserve不会影响size