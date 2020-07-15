## 2020/07/15小记

**[ form表单获取默认值name(code)中的code ]**
 - 实际效果图如下:
 
![](/assets/staffName.png)

- 背景:为编辑合同号抽屉的时候,业务员需要把名字和code一起作为默认值显示出来.销售组织默认值也是中文.这个时候通过this.props.form.validateFields()方法去取值的时候,取到的就是中文.给后端的数据肯定只给code,这时候就需要处理一下.


- 实现代码:
```
   // 编辑抽屉点击保存
  handeleUpdate = () => {
    const { updateContractInfo } = this.props;
    const { listItem } = this.state;
    this.props.form.validateFields((err, values) => {
      let code = values.staffCode.indexOf('(') // 判断人员是否有更改
      let str = new RegExp("[\u4E00-\u9FA5]+"); //判断销售组织是否有更改
      if (!err) {
        const json = {
          id: listItem.id,
          contractCode: listItem.contractCode,
          mailingOrderNum: values.mailingOrderNum,
          orgCode: str.test(values.orgCode) ? listItem.orgCode : values.orgCode,
          sealStatus: values.sealStatus=== '是' ? 'sealed' : 'noSealed',
          staffCode: code >= 0 ? listItem.staffCode : values.staffCode,
        };
        updateContractInfo({ ...json }, res => {
          if (res) {
            message.success('修改合同号信息成功!');
            this.getContractList();
            this.onClose();
          }
        });
      }
    });
  };
```

- 实现思路:
- 业务员:因为它的默认值取到的是`name(code)`.这个时候首先要判断输入框的值是否有改变.如果没有改变的话是可以直接取点击单行编辑的时候带过来的当前行数据的.`listItem`里面就是当前行数据,如果取到的值是`name(code)`,就通过`indexOf()`方法判断`'('`是否存在.如果存在就证明数据未被更改.如果数据被更改,直接取form表单返回的`values.staffCode`就好.
- 销售组织:销售组织的默认值是中文汉字,如果不更改直接取值拿到的肯定是中文汉字,这个时候就通过`正则`判断字符串里面是否中文.如果字符串里面是中文就代表没有更改. 直接取当前列表带过来的销售组织`listItem.orgCode`就好,如果字符串里面不是中文,就直接取form表单返回的`values.orgCode`