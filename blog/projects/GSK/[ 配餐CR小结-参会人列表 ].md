## 2020/08/26小记

**[ 配餐CR小结-参会人列表 ]**

- 选择进入会议的参会人和参会人的携带人

 - 效果图如下:
 
![](/assets/20200826161744.png)

- 遇到的问题:
(1):进入页面要把之前选中的值赋出来.
(2):刷新列表后,之前操作的未保存的数据要清空,还原到最开始进入的选中状态.

- 实现代码:

```
  //初始化信息
  const getInfoData = async () => {
    const data: any = await getRemoteCallRecycleClientList(activtylID);
     // 把选中的id赋值出来
     const selected:any = [] 
     data && data.remoteCallRecycleClientList.map( (value:any)=>{
       // 父列表被选中的id
       if(value.IsNotRecycle){
         selected.push(value.CarryID)
       }
       // 子列表被选中的id
       value.carryClientList.map( (v :any) =>{
         if(v.IsNotRecycle){
           selected.push(v.CarryID)
         }
       })
     })
    setCarryIDList(selected)
    setClientList(data);
  };

    // 选中的未收餐人
  const handleCheckbox =(item:any,index:number)=>{
    if(index === 1){
      clientList && clientList.remoteCallRecycleClientList.map( (value : any) =>{
        if(item.CarryID == value.CarryID){
          value.IsNotRecycle = !value.IsNotRecycle
        }
      })
    }else if(index === 2){
      clientList && clientList.remoteCallRecycleClientList.map( (value : any) =>{
        value.carryClientList.map( (v :any) =>{
          if(item.CarryID == v.CarryID){
            v.IsNotRecycle = !v.IsNotRecycle
          }
        })
      })
    }
    setClientList(clientList);
     // 把选中的id赋值出来
     const selected:any = [] 
     clientList && clientList.remoteCallRecycleClientList.map( (value:any)=>{
       // 父列表被选中的id
       if(value.IsNotRecycle){
         selected.push(value.CarryID)
       }
       // 子列表被选中的id
       value.carryClientList.map( (v :any) =>{
         if(v.IsNotRecycle){
           selected.push(v.CarryID)
         }
       })
     })
     setCarryIDList(selected)
  }
  // 渲染的多选框
   <Checkbox defaultChecked={item.IsNotRecycle} checked={item.IsNotRecycle} onChange={() =>{ handleCheckbox(item,1)}} />
```

- 实现思路:
多选框赋值为列表数据每一项中的一个初始值:`IsNotRecycle`,每次你选中或者取消选中的时候,把页面上的列表里面的值改变状态.重新赋值到列表里.页面就会刷新.`defaultChecked`是一进页面的初始值.`checked`是后期你取消选中或者选中的值.
我的刷新按钮触发是在父页面调用子页面的方法.利用useEffect方法监听列表`clientList`的变化.传值到父页面.
`  useEffect(() => {
    // 传递到父页面的方法和参数
    props.onRef({
      ClientCount:clientList.ClientCount, // 收餐份数
      getInfoData, // 获取列表的方法
      CarryIDList, // 选中的值
    })
  }, [clientList]);`