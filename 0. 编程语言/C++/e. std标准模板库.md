# std标准模板库

## function

[https://blog.csdn.net/fengbingchun/article/details/52562918](https://blog.csdn.net/fengbingchun/article/details/52562918)

```cpp
function<void(const string&)> preorder = [&](const string& name) {
            if (!dead.count(name)) {
                ans.push_back(name);
            }
            if (edges.count(name)) {
                for (const string& childName: edges[name]) {
                    preorder(childName);
                }
            }
        };
```