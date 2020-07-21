## 2020/06/30小记

**AntD(Table_columns删减)**

实现思路:
``通过splice方法.找到需要删减的位置和数量``
```
    render(
        const {  contractStatus } = this.props;
        const columns =[...]
        // 打印columns,找到需要删除的下标
        console.log('columns---',columns)
        // 根据表单选择的值判断是否删除
        if(contractStatus && contractStatus === '商务'){
            // splice用法(下标,需要删除的个数,替换的值(可选))
            columns.splice(7,2)
        }
    )
   
```

- 结果如下图:

`商务/代理未选择的时候`
![](/assets/agent.png)

`选择商务后的columns`
![](/assets/agent1.png)



