# Boxing and UnBoxing

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

