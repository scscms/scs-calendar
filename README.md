# ES2015　日历控件<sup>shine</sup>

很久以前就想自己写个日历控件，可以应付设计师的特殊界面。
网上也很多日期选择控件，而且功能强大丰富。原代码也及其复杂难懂，其实日历可以更加简单实现，虽然在此使用的是ES2015，直接转成ES5也是可以的。

### 界面分析

如图：

![image](https://github.com/scscms/scs-calendar/raw/master/calendar.jpg)

可看到，每个月的日历界面由上个月末尾几天，和本月所有天数加上下个月的月初几天组成，共7 X 6 = 42个格子。
主要难题其实就只有一个：获取某个月最后一天的号数是多少？网上大部分做法就是判断是否闰年是否是闰月等去取。
其实我们有一个更简单的方法：把某个日期对象的天数设置成0，它会自动转换成该日期的上个月最后一天的日期。
```javascript
var ymd = new Date(2016,2,0);//自动获取到2016年2月最后一天日期
console.log(ymd);// Date {Mon Feb 29 2016 00:00:00 GMT+0800}
```

### 1.初级界面
首先我们只显示当前月份的日期来实现一个简单界面：
```javascript
	"use strict";
    const nowDate = new Date();//当前日期
    const nowYear = nowDate.getFullYear();//当前日期的年份
    const nowMonth = nowDate.getMonth() + 1;//当前日期的月分
    const firstDay = new Date(nowYear,nowMonth - 1,1);//当前日期的第一天日期
    const lastDay = new Date(nowYear,nowMonth,0).getDate();//获取本月最后一天的号数
    const arrayDay = [];//当前日历的日期数组;
    for(let i = 1;i <= lastDay;i ++){
        arrayDay.push(nowYear + "-"+ nowMonth + "-"+ i);
    }

    let firstWeek = firstDay.getDay();//当前日期的第一天的星期
    if(firstWeek > 0){
        //假如第一天不是从星期天开始，需要补充日期数组
        for(let i = firstWeek;i --;)arrayDay.unshift("");
    }
    //日期数组大部分都不够长，再加下个月的14天空位
    for(let i = 14;i--;)arrayDay.push("");

    let table = `<table border="0" cellpadding="5" cellspacing="0"><tr><td>日</td>
    <td>一</td><td>二</td><td>三</td><td>四</td><td>五</td><td>六</td></tr><tbody>`;
    //遍历组成一个7 X 6的日历界面
    for(let l = 0;l <= 42;l ++){
        if(l % 7 == 0){
            table += `<tr>`;
        }
        //截取相应日期显示
        table += `<td>${arrayDay[l].replace(/\d+\-/g,"")}</td>`;
        if(l + 1 % 7 == 0){
            table += `</tr>`;
        }
    }
    table += `</tbody></table>`;

    document.querySelector(".scs_calendar").innerHTML = table;
```
[查看完整代码](index.html)

### 2.添加年月select选择

[查看完整代码](index2.html)

当然这是凭直觉直接写出来的代码。其中我们仍有优化的空间：比如年月select可只执行一次innerHTML；甚至table td也可一次性生成，后继修改其文本或样式即可；
最后还没增加点击日期事件哟。

### 3.经过优化的代码

思路是先使用js生成整体html然后根据传入的年份和月份修改相应的日期即可。同时使用事件委托监听点击事件。
这样改变年月时调用的函数就很简洁了：
```javascript
    function scsCalendar(year,month){
        const firstDay = new Date(year,month - 1,1);//当前日期的第一天日期
        const lastDay = new Date(year,month,0).getDate();//获取本月最后一天的号数
        let firstWeek = firstDay.getDay();//当前日期的第一天的星期
        for(let i = 0;i < 42;i ++){
            let td = dateObj.tds[i],day = i >= firstWeek && i < lastDay + firstWeek ? i - firstWeek + 1 : "";
            td.innerHTML = day;//dateObj.tds.textContent
            td.setAttribute("data-ymd",day ? year + "-" + month + "-" + ("0" + day).slice(-2) :"");
        }
        dateObj.selectDom[0].value = year;
        dateObj.selectDom[1].value = month;
    }
```
[查看完整代码](index3.html)

### 4.编写成jQuery插件
下一站...
### 5.非select选择年月
下二站...
### 5.切换选择年月窗口
下三站...
### 6.日历的简单限制
下四站...

终点站...