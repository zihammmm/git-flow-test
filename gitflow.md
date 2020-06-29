# 正则表达式需要预编译

# 需要Map的主键和取值时，应该迭代entrySet()
不要在方法体内定义：Pattern pattern = Pattern.compile(规则);

# 应该使用Collection.isEmpty()检测空
时间复杂度为$O(1)$，部分Collection.size()时间复杂度可能是$O(n)$

# 字符串拼接使用StringBuilder
拼接单个字符使用char

# 禁止使用构造方法BigDecimal(double)
使用BigDecimal(String)

# 返回空数组和空集合而不是null
+ Collections.emptyList()
+ Collections.emptyMap()

# 优先使用常量或确定值来调用equals方法(java.utils.Objects.equals())

# 所有的相同类型的包装类对象之间值的比较，全部使用equals方法比较
对于Integer var = ? 在-128至127范围内的赋值，Integer对象是在IntegerCache.cache产生，会复用已有对象，可以直接使用==进行判断。在这个区间之外的所有数据，都会在堆上产生，推荐使用equals方法进行判断。

# Map类集合K/V能不能存储null值的情况
|集合类|key|value|super|说明|
|---|---|---|---|---|
|Hashtable|not null|not null|Dictionary|线程安全|
|ConcurrentHashMap|not null|not null|AbstractMap|锁分段技术|
|TreeMap|not null|null|AbstractMap|线程不安全|
|HashMap|null|null|AbstractMap|线程不安全|

# 使用Arrays.asList()或者List.of转化后不可修改
asList返回对象是一个Arrays内部类，并没有实现集合的修改方法。Arrays.asList体现的是适配器模式，只是转换接口，后台的数据仍是数组。

# foreach中不可remove/add元素
remove元素使用iterator方式，并发需要对Iterator对象加锁。