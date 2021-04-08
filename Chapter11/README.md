# Chapter11

## 11.1

|  关联容器|类型  |
|  :----| :----   |
| map{key, value}          |          不可重复且有序       | 
| set{key}                |           不可重复且有序       |
| multimap{key, value} |          可重复且有序      |
| multiset{key}       |    可重复且有序         |
| unordered_map{key, value} |    不可重复且无序|
| unordered_set{key}         |  不可重复且无序|
| unordered_multimap{key, value}  |     可重复且无序|
| unordered_multiset{key}       |    可重复且无序|


## 11.3

|  关联容器额外的类型别名|  |
|  :----| :----  |
| key_type | 此容器为关键字的类型 |
| mapped_type | 每个关键字关联的类型，只适用于map |
| value_type | 对于set，与key_type相同。对于map, 与`pair<const key_type, mapped_type>` 相同｜

```C++
set<string>::value_type v1; // v1 string
set<string>::key_type v2; // v2 string
map<string, int>::value_type v3; // v3 pair<const string, int>
map<string, int>::key_type v4; // v4 string
map<string, int>::mapped_type v5; // v5 int
```

set的迭代器是const的