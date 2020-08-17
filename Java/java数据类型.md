# 装箱和拆箱

* new Integer(2) == 2?
  * true
  * 自动拆箱了
* new Integer(2 ) == new Integer(2)?
  * false
  * 比较的是两个箱子
* Integer.valueOf(2) == Integer.valueOf(2)?
  * 不确定
  * 在-128-127之间,系统会缓存,此时相等
  * 在其他数字,系统随机缓存,此时不确定
* Integer.valueOf(2).intValue() == 2?
  * true
  * intValue()拆箱
* new Integer(2).equals(new Integer(2))?
  * true
  * equals 判断值是否相等
* 自动装箱
  * 调用 `Integer.valueOf()`
  * 缓冲池大小`-128-127`,上界可通过`-XX:AutoBoxCacheMax=<size>`调整

# 基本数据类型

|      |         |       |
| ---- | ------- | ----- |
| 1    | int     | 4字节 |
| 2    | char    | 2字节 |
| 3    | byte    | 1字节 |
| 4    | short   | 2字节 |
| 5    | long    | 8字节 |
| 6    | double  | 8字节 |
| 7    | float   | 4字节 |
| 8    | boolean | 1字节 |

