---
title: javascript操作date的使用须知
date: 2019-01-18 12:18:54
tags:
- date
- 运算符
categories: 
- JavaScript
---
# Date
`Date `对象用于处理日期与时间。
## 构造方法
````
new Date();
new Date(value);
new Date(dateString);
new Date(year, month[, day[, hour[, minutes[, seconds[, milliseconds]]]]]);
````
参数：
````
value
代表自1970年1月1日00:00:00 (世界标准时间) 起经过的毫秒数。
dateString
表示日期的字符串值。该字符串应该能被 Date.parse() 方法识别（符合 IETF-compliant RFC 2822 timestamps 或 version of ISO8601）。
year
代表年份的整数值。为了避免2000年问题最好指定4位数的年份; 使用 1998, 而不要用 98.
month
代表月份的整数值从0（1月）到11（12月）。
day
代表一个月中的第几天的整数值，从1开始。
hour
代表一天中的小时数的整数值 (24小时制)。
minute
分钟数。
second
秒数。
millisecond
表示时间的毫秒部分的整数值。
````
<!-- more -->

>需要注意的是只能通过调用 Date 构造函数来实例化日期对象：以常规函数调用它（即不加 new 操作符）将会返回一个字符串，而不是一个日期对象。另外，不像其他JavaScript 类型，Date 对象没有字面量格式。
>当Date作为构造函数调用并传入多个参数时，如果数值大于合理范围时（如月份为13或者分钟数为70），相邻的数值会被调整。比如 new Date(2013, 13, 1)等于new Date(2014, 1, 1)，它们都表示日期2014-02-01（注意月份是从0开始的）。其他数值也是类似，new Date(2013, 2, 1, 0, 70)等于new Date(2013, 2, 1, 1, 10)，都表示时间2013-03-01T01:10:00。
* 如果没有输入任何参数，则Date的构造器会依据系统设置的当前时间来创建一个Date对象。
* 如果提供了至少两个参数，其余的参数均会默认设置为1（如果没有提供day参数）或者0。
* JavaScript的时间是由世界标准时间（UTC）1970年1月1日开始，用毫秒计时，一天由86,400,000毫秒组成。Date对象的范围是-100,000,000天至100,000,000天（等效的毫秒值）。
* JavaScript的Date对象为跨平台提供了统一的行为。时间属性可以在不同的系统中表示相同的时刻，而如果使用了本地时间对象，则反映当地的时间。
* JavaScript 的Date对象提供了数个UTC时间的方法，也相应提供了当地时间的方法。UTC，也就是我们所说的格林威治时间，指的是time中的世界时间标准。而当地时间则是指执行JavaScript的客户端电脑所设置的时间。
* 以一个函数的形式来调用JavaScript的Date对象（i.e., 不使用 new 操作符）会返回一个代表当前日期和时间的字符串。

### dateString
>表示日期的字符串值。该字符串应该能被 `Date.parse() `方法识别（符合` IETF-compliant RFC 2822 timestamps `或 `version of ISO8601`）。所以需要确认`datestring`的类型。
#### IETF-compliant RFC 2822 timestamps
最初的`datestring`的只支持：[时间字符串](https://tools.ietf.org/html/rfc2822#page-14)
````
Date and Time Specification

   Date and time occur in several header fields.  This section specifies
   the syntax for a full date and time specification.  Though folding
   white space is permitted throughout the date-time specification, it
   is RECOMMENDED that a single space be used in each place that FWS
   appears (whether it is required or optional); some older
   implementations may not interpret other occurrences of folding white
   space correctly.

date-time       =       [ day-of-week "," ] date FWS time [CFWS]

day-of-week     =       ([FWS] day-name) / obs-day-of-week

day-name        =       "Mon" / "Tue" / "Wed" / "Thu" /
                        "Fri" / "Sat" / "Sun"

date            =       day month year

year            =       4*DIGIT / obs-year

month           =       (FWS month-name FWS) / obs-month

month-name      =       "Jan" / "Feb" / "Mar" / "Apr" /
                        "May" / "Jun" / "Jul" / "Aug" /
                        "Sep" / "Oct" / "Nov" / "Dec"

day             =       ([FWS] 1*2DIGIT) / obs-day

time            =       time-of-day FWS zone

time-of-day     =       hour ":" minute [ ":" second ]

hour            =       2DIGIT / obs-hour

minute          =       2DIGIT / obs-minute

second          =       2DIGIT / obs-second

zone            =       (( "+" / "-" ) 4DIGIT) / obs-zone

The day is the numeric day of the month.  The year is any numeric
   year 1900 or later.

   The time-of-day specifies the number of hours, minutes, and
   optionally seconds since midnight of the date indicated.

   The date and time-of-day SHOULD express local time.

   The zone specifies the offset from Coordinated Universal Time (UTC,
   formerly referred to as "Greenwich Mean Time") that the date and
   time-of-day represent.  The "+" or "-" indicates whether the
   time-of-day is ahead of (i.e., east of) or behind (i.e., west of)
   Universal Time.  The first two digits indicate the number of hours
   difference from Universal Time, and the last two digits indicate the
   number of minutes difference from Universal Time.  (Hence, +hhmm
   means +(hh * 60 + mm) minutes, and -hhmm means -(hh * 60 + mm)
   minutes).  The form "+0000" SHOULD be used to indicate a time zone at
   Universal Time.  Though "-0000" also indicates Universal Time, it is
   used to indicate that the time was generated on a system that may be
   in a local time zone other than Universal Time and therefore
   indicates that the date-time contains no information about the local
   time zone.

   A date-time specification MUST be semantically valid.  That is, the
   day-of-the-week (if included) MUST be the day implied by the date,
   the numeric day-of-month MUST be between 1 and the number of days
   allowed for the specified month (in the specified year), the
   time-of-day MUST be in the range 00:00:00 through 23:59:60 (the
   number of seconds allowing for a leap second; see [STD12]), and the
   zone MUST be within the range -9959 through +9959.
````
所以可以得出结论：最开始的js仅支持以下格式的datestring【需要注意的是各个浏览器实现不一致，所以可能会支持其他的格式的`datestring`】
    Sun 30 Dec 2018 19:42:44 +0800
看起来和`console.log`里面的输出有一点不一致；
    Sun Dec 30 2018 19:42:44 GMT+0800 (中国标准时间)

#### version of ISO8601
在es5开始，[添加了ISO8601格式](http://www.ecma-international.org/ecma-262/5.1/#sec-15.9.1.15)的支持；
````
Date Time String Format
ECMAScript defines a string interchange format for date-times based upon a simplification of the ISO 8601 Extended Format. The format is as follows: YYYY-MM-DDTHH:mm:ss.sssZ

Where the fields are as follows:

YYYY	is the decimal digits of the year 0000 to 9999 in the Gregorian calendar.

-	“-” (hyphen) appears literally twice in the string.

MM	is the month of the year from 01 (January) to 12 (December).

DD	is the day of the month from 01 to 31.

T	“T” appears literally in the string, to indicate the beginning of the time element.

HH	is the number of complete hours that have passed since midnight as two decimal digits from 00 to 24.

:	“:” (colon) appears literally twice in the string.

mm	is the number of complete minutes since the start of the hour as two decimal digits from 00 to 59.

ss	is the number of complete seconds since the start of the minute as two decimal digits from 00 to 59.

.	“.” (dot) appears literally in the string.

sss	is the number of complete milliseconds since the start of the second as three decimal digits.

Z	is the time zone offset specified as “Z” (for UTC) or either “+” or “-” followed by a time expression HH:mm

This format includes date-only forms:

YYYY
YYYY-MM
YYYY-MM-DD
It also includes “date-time” forms that consist of one of the above date-only forms immediately followed by one of the following time forms with an optional time zone offset appended:

THH:mm
THH:mm:ss
THH:mm:ss.sss
All numbers must be base 10. If the MM or DD fields are absent “01” is used as the value. If the HH, mm, or ss fields are absent “00” is used as the value and the value of an absent sss field is “000”. The value of an absent time zone offset is “Z”.

Illegal values (out-of-bounds as well as syntax errors) in a format string means that the format string is not a valid instance of this format.

NOTE 1 As every day both starts and ends with midnight, the two notations 00:00 and 24:00 are available to distinguish the two midnights that can be associated with one date. This means that the following two notations refer to exactly the same point in time: 1995-02-04T24:00 and 1995-02-05T00:00

NOTE 2 There exists no international standard that specifies abbreviations for civil time zones like CET, EST, etc. and sometimes the same abbreviation is even used for two very different time zones. For this reason, ISO 8601 and this format specifies numeric representations of date and time.
````
`datestring`的格式可以是如下：
* [date]
* [date]T[time]
* [date]T[time]Z
* [date]T[time][+|-][0800]
其中[date]可以是：
* YYYY
* YYYY-MM
* YYYY-MM-DD
而[time]可以是一下：
* THH:mm
* THH:mm:ss
* THH:mm:ss.sss

可以说[date]是必须有的，其他的都是可以没有，会取默认值，当然各个浏览器内部实现的不一致，所以可能还会出现不一致的问题；

## API列表
````
Date.UTC()
Date.now()
Date.parse()
Date.prototype.getDate()
Date.prototype.getDay()
Date.prototype.getFullYear()
Date.prototype.getHours()
Date.prototype.getMilliseconds()
Date.prototype.getMinutes()
Date.prototype.getMonth()
Date.prototype.getSeconds()
Date.prototype.getTime()
Date.prototype.getTimezoneOffset()
Date.prototype.getUTCDate()
Date.prototype.getUTCDay()
Date.prototype.getUTCFullYear()
Date.prototype.getUTCHours()
Date.prototype.getUTCMilliseconds()
Date.prototype.getUTCMinutes()
Date.prototype.getUTCMonth()
Date.prototype.getUTCSeconds()
Date.prototype.getYear()
Date.prototype.setDate()
Date.prototype.setFullYear()
Date.prototype.setHours()
Date.prototype.setMilliseconds()
Date.prototype.setMinutes()
Date.prototype.setMonth()
Date.prototype.setSeconds()
Date.prototype.setTime()
Date.prototype.setUTCDate()
Date.prototype.setUTCFullYear()
Date.prototype.setUTCHours()
Date.prototype.setUTCMilliseconds()
Date.prototype.setUTCMinutes()
Date.prototype.setUTCMonth()
Date.prototype.setUTCSeconds()
Date.prototype.setYear()
Date.prototype.toDateString()
Date.prototype.toGMTString()
Date.prototype.toISOString()
Date.prototype.toJSON()
Date.prototype.toLocaleDateString()
Date.prototype.toLocaleFormat()
Date.prototype.toLocaleString()
Date.prototype.toLocaleTimeString()
Date.prototype.toSource()
Date.prototype.toString()
Date.prototype.toTimeString()
Date.prototype.toUTCString()
Date.prototype.valueOf()
Date.prototype[@@toPrimitive]
````
### 一元加法|加法
````
+new Date()
// 1546335448125
0+new Date()
// "0Tue Jan 01 2019 17:53:17 GMT+0800 (中国标准时间)"
```
#### 二元加法获取date的值；

[加法运算](https://www.ecma-international.org/ecma-262/5.1/#sec-11.6.1)
![clipboard.png](http://huananimg.zanmeizhuen.com/blog/img/js_date.png)

上图中的NOTE 1说，在步骤5和6中的ToPrimitive调用没有提供hint，除了Date对象之外所有ECMAScript对象将缺少hint的情况当做Number处理；

[js各个类型的toPrimitive](https://javascript.info/object-toprimitive)

而本文需要看的是date[@@toPrimitive]() 方法可以转换一个 Date 对象到一个原始值。
    Date()[Symbol.toPrimitive](hint)
 ==>[date.toPrimitive](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/@@toPrimitive);

给出的` Date `的原始值。根据传入参数的不同，可以返回 `string 或 number `类型。

Date 对象的 [@@toPrimitive]() 方法可以返回一个原始值，或是 `string，或是number`。

如果 `hint` 是` "string"` 或 `"default"`，`[@@toPrimitive]()` 将会调用 `toString`。如果 `toString` 属性不存在，则调用 `valueOf`。如果 `valueOf `也不存在，则抛出一个TypeError。

如果` hint `是 `"number"`，`[@@toPrimitive]() `会首先尝试 `valueOf`，若失败再尝试 `toString`。

当期望一个原始值却收到一个对象时，JavaScript 可以自动的调用 `[@@toPrimitive]()` 方法来将一个对象转化成原始值，所以你很少会需要自己调用这个方法。

date的`toPrimitive`调用，不传递hint的时候，默认是`string`；

所以：
    0+new Date()
    // "0Tue Jan 01 2019 17:53:17 GMT+0800 (中国标准时间)"

#### 一元加法获取date的值
'+'号运算符作为一元运算符时，Expression将进行[ToNumber](http://www.ecma-international.org/ecma-262/6.0/#sec-tonumber)操作。


![clipboard.png](http://huananimg.zanmeizhuen.com/blog/img/js_date2.png)

可以看到，此时date的toPrimitive调用，传递hint是number，返回毫秒数；

参考： [JavaScript 加号运算符详解](http://www.cnblogs.com/polk6/p/js-adv-addopr.html)

