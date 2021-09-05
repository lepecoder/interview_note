## 不修改序列的操作

* find：顺序查找 `find(v.begin(), v.end(), value)` 返回迭代器。
* find_end：逆序查找  `find_end(v.begin(), v.end(), value)`
* find_if：查找满足条件的第一个元素  `find_if(v.begin(), v.end(), [](int x) {return x>4;})`
* binary_search：二分查找  `binary_search(v.begin(), v.end(), value)`
* merge：两个有序数列合并  `merge(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(v3))`
* lower_bound：在有序数列中二分查找第一个大于等于value的元素。
* upper_bound：在有序数列中二分查找第一个大于value的元素。
* equal_range：在有序数列上二分查找，返回一个pair，保存给定值的范围。
* copy：复制容易内元素到一个新容器，`copy(v.begin(), v.begin()+k, ans.begin())`

## 修改序列的操作

* reverse：翻转数组、字符串
* unique：去除相邻重复的元素，返回尾迭代器，之后的元素不确定，通常需要 `v.erase(res,v.end()`配合`sort`可以去重。
* sort：排序
* stable_sort：稳定排序
* nth_element：类似快速排序，`nth_element(v.begin(), v.begin()+k-1, v.end())` 使前k个元素小于后k个元素。
* next_permutation：将当前容易更改为全排列的下一个排列

`__gnu_pbds::priority_queue()`优先队列。支持擦除操作。

## C++常用算法

- 排序函数

| 函数名                                                     | 用法                                                                                                                                                                                               |
| ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sort (first, last)                                         | 对容器或普通数组中 [first, last) 范围内的元素进行排序，默认进行升序排序。                                                                                                                          |
| stable_sort (first, last)                                  | 和 sort() 函数功能相似，不同之处在于，对于 [first, last) 范围内值相同的元素，该函数不会改变它们的相对位置。                                                                                        |
| partial_sort (first, middle, last)                         | 从 [first,last) 范围内，筛选出 muddle-first 个最小的元素并排序存放在 [first，middle) 区间中。                                                                                                      |
| partial_sort_copy (first, last, result_first, result_last) | 从 [first, last) 范围内筛选出 result_last-result_first 个元素排序并存储到 [result_first, result_last) 指定的范围中。                                                                               |
| is_sorted (first, last)                                    | 检测 [first, last) 范围内是否已经排好序，默认检测是否按升序排序。                                                                                                                                  |
| is_sorted_until (first, last)                              | 和 is_sorted() 函数功能类似，唯一的区别在于，如果 [first, last) 范围的元素没有排好序，则该函数会返回一个指向首个不遵循排序规则的元素的迭代器。                                                     |
| void nth_element (first, nth, last)                        | 找到 [first, last) 范围内按照排序规则（默认按照升序排序）应该位于第 nth 个位置处的元素，并将其放置到此位置。同时使该位置左侧的所有元素都比其存放的元素小，该位置右侧的所有元素都比其存放的元素大。 |