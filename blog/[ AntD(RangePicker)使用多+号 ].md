## 2020/06/21小记

**[ AntD(RangePicker)使用多+号 ]**
 - 实际效果图如下:
 
![](/assets/RangePicker.png)

- 实现代码:
```
    // 组件实现
     <RangePicker
        showTime={{ format: 'HH:mm:ss' }}
        format="YYYY-MM-DD "
        placeholder={['开始日期', '结束日期']}
        style={{ width: '200px', marginRight: 20 }}
        onChange={(time, timeString) => this.handelChange(time, timeString)}
    />

    // 同步数据选择时间
    handelChange = (time, timeString) => {
        this.setState({
        synTime: {
            startTime: timeString[0],
            endTime: timeString[1],
        },
        });
    };
```

- 遇到的问题及解决方法:
- 遇到的问题是RangePicker组件取timeString值的时候后面会有空格.传值的时候会变成加号.
- 解决方法:
```
    // 第一种使用substring截取.

    // 同步数据选择时间
    handelChange = (time, timeString) => {
        this.setState({
        synTime: {
            startTime: timeString[0].substring(0, 10),
            endTime: timeString[1].substring(0, 10),
        },
        });
    };

    // 第二种使用.trim()方法.用来删除字符串前后空格.
    
    // 同步数据选择时间
    handelChange = (time, timeString) => {
        this.setState({
        synTime: {
            startTime: timeString[0].trim(),
            endTime: timeString[1].trim(),
        },
        });
    };

    // 第三种使用replace正则匹配方法清除
    
    去除字符串内所有的空格：str = str.replace(/\s*/g,"");
    去除字符串内两头的空格：str = str.replace(/^\s*|\s*$/g,"");
    去除字符串内左侧的空格：str = str.replace(/^\s*/,"");
    去除字符串内右侧的空格：str = str.replace(/(\s*$)/g,"");
    
```
- 最终效果:
![](/assets/RangePickerEND.png)