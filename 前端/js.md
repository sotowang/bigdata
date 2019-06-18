# JS中内置对象

* Number

  ```javascript
  var myNumber = new Number(numeric value);
  ```

  * 属性
    * MAX_VALUE
    * MIN_VALUE
    * NEGATIVE_INFINITY
    * POSITIVE_INFINITY
  * 方法
    * toExponential
    * toFixed
    * toPrecision
    * toString
    * valueOf

* Boolean

  ```javascript
  var myBoolean = true;
  ```

* String

  ```javascript
  var myString = "My string";
  ```

  * 属性
    * length
  * 方法
    * `charAt`
    * `charCodeAt`
    * `concat`
    * `fromCharCode`
    * `indexOf`
    * `lastIndexOf`
    * `match`
    * `replace`
    * `search`
    * `slice`
    * `split`
    * `substr`
    * `substring`
    * `toLowerCase`
    * `toUpperCase`

* Date

  ```javascript
  var myDate = new Date();
  var myDate = new Date(dateString);
  var myDate = new Date(milliseconds);
  var myDate = new Date(year, month, day, hours, minutes, seconds, milliseconds);
  ```

  * 方法
    * `getDate`
    * `getDay`
    * `getFullYear`
    * `getHours`
    * `getMilliseconds`
    * `getMinutes`
    * `getMonth`
    * `getSeconds`
    * `getTime`
    * `getTimezoneOffset`
    * `setDate`
    * `setFullYear`
    * `setHours`
    * `setMilliseconds`
    * `setMinutes`
    * `setMonth`
    * `setSeconds`
    * `setTime`

* Array

  ```javascript
  var myArray = new Array(1, 2, 3);
  for(var i=0; i<myArray.length; i++) {
     document.write(myArray[i]);
  }
  ```

* Math

  ```javascript
  var pi = Math.PI;
  ```

  