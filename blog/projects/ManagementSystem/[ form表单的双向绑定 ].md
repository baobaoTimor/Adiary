## 2020/07/28小记

**[ form表单的双向绑定 ]**

**前言:**
在AntDesignPro`3.23.6`版本中,form表单绑定值是使用的`getFieldDecorator`方法赋值配合`validateFields`方法取值达到双向绑定的效果.那在AntDesignPro `4.0`版本中如何赋值取值呢?

AntDesignPro`3.23.6`版本示例:
```
// 赋值
 <Form  
    labelCol={{ span: 6 }}
    wrapperCol={{ span: 14 }}
    onSubmit={this.handleSubmit}
  >
    <Form.Item>
      {getFieldDecorator('money',{
          rules=[{ 
              required: true,
              message: '请输入金额',
            }],
      })(
          <InputNumber
            min={0}
            onChange={value => console.log(value) })}
            placeholder="请输入金额"
           />
        )}
    </Form.Item>
  </Form>

// 取值
handleSubmit () =>{
    this.props.form.validateFields((err, values) => {
          if (!err) {
            // 这样就拿到了输入的金额
            const money = values.money
          }
    })
}

```
AntDesignPro`4.0`版本示例:
```
// 赋值
 <Form  
    labelCol={{ span: 6 }}
    wrapperCol={{ span: 14 }}
    onSubmit={this.handleSubmit}
  >
    <Form.Item
      label="金额"
      name="money"
      rules={[
        {
          required: true,
          message: '请输入金额',
        },
      ]}
    >
      <InputNumber
        min={0}
        onChange={value => console.log(value) })}
        placeholder="请输入金额"
       />
    </Form.Item>
  </Form>

// 取值
handleSubmit () =>{
  const { validateFields } = form;
  const onValidateForm = async () => {
        const values = await validateFields();
         // 这样就拿到了输入的金额
        const money = values.money
  }
}

```

**总结:**

`3.23.6`版本:

赋值:`getFieldDecorator`方法.
取值:`validateFields`方法.
校验:`rules`在`getFieldDecorator`方法内.

`4.0`版本:

赋值:`Form.Item`的`name`属性直接赋值.
取值:`validateFields`方法.
校验:`Form.Item`的`rules`属性直接校验.
