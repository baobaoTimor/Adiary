## 2020/08/26小记

**[ 配餐CR小结-携带参会人 ]**

- 填写携带者的信息

 - 效果图如下:
 
![](/assets/20200826165352.png)

- 遇到的问题:
(1):需要不重复的主键.
(2):微信上传图片.
(3):科室和职称的默认值处理.
(4):第二次进入增加携带者的时候要带出之前的图片和携带者信息

- 实现代码:

```
    // 定义携带者列表
    const [people, setPeople] = useState([{
        id: crypto.guid(),
        CarryDeptName:[], // 科室
        CarryClientName: '', //医生姓名
        InstitutionName: '', //所在医院
        CarryClientTitle:[], //职称
    }]); // 携带参会人
    const [ClientID, setClientID] = useState()//客户ID
    const [deptData, setDeptData] = useState([])//科室列表
    const [positionData, setPositionData] = useState([])//职称列表
    const [doctorData, setDoctorData] = useState()//进入页面医生的信息
    const [files, setFiles] = useState<any>([]) // 所有的图片

    // 页面初始化获取是否有携带人数据
    useEffect(()=>{
        getInfoData()
    },[])

    // 列表数据
    const getInfoData = async () => {
        const data: any = await getRemoteCarryClientList(openId,activtylID)
        const deptData: any = await getDeptAll()
        const positionData: any = await getPositionAll()
        if(deptData || positionData){
          await setDeptData(deptData);setPositionData(positionData)
        }
        setDoctorData(data)
        let newProple = [...people]
        if (data) {
          // 医生首次进入携带参会人页面的时候,赋机构名称和科室的默认值
          newProple[0].InstitutionName = data.InstitutionName
          newProple[0].CarryDeptName = data.ClientCarryList.length > 0  ? data.ClientCarryList : [ data.DeptID,data.DeptName]
          await setPeople(newProple);setClientID(data.ClientID)
          if(data.ClientCarryList && data.ClientCarryList.length > 0 ){
            // 医生再次进入携带参会人页面的时候,携带的医生列表有值的时候,展示出携带的值
            let nextProple:any = []
            data.ClientCarryList.map((value1 : any)=>{
              // 处理携带医生职务
              const CarryClientTitle = positionData && positionData.filter( (v : any) =>{
                if(value1.CarryClientTitle == v.label){
                  return Array(v.value,String(v.label))
                }
              })
              nextProple.push({
                id:crypto.guid(),
                InstitutionName:value1.InstitutionName,
                CarryDeptName:Array(value1.CarryDeptID,value1.CarryDeptName),
                CarryClientName:value1.CarryClientName,
                CarryClientTitle:[CarryClientTitle[0].value,CarryClientTitle[0].label]
              })
            })
            await setPeople(nextProple);
          }
          // 医生第二次进入页面的时候图片进行赋值
          setFiles(data.ClientIamagesList);
        }
      } 
      // 打开相机上传图片
      const chooseImage = () => {
        if (files.length < 3) {
          (window as any).wx.chooseImage({
            count: 1, // 默认9
            sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
            sourceType: ['camera'], // 指定来源是相机
            success: (res: any) => {
              (window as any).wx.getLocalImgData({
                localId: res.localIds[0], // 图片的localID
                success: function(res: { localData: any }) {
                  let localData: any
                  // 判断是ios还是安卓
                  if (res.localData.substr(0, 5) !== 'data:') {
                    localData = 'data:image;base64,' + res.localData;
                  } else {
                    localData = res.localData;
                  }
                  setFiles((files: any) => {
                    return [...files,localData]
                  })
                },
              });
            },
          });
        } else {
          Toast.info('最多只能上传三张照片!');
        }
      };

       // 输入框的值
      const handleChange = (index :number,name: any, value: any) => {
        let newPeople = [...people];
        newPeople[index] = Object.assign({},people[index],{[name]:value});
        setPeople(newPeople);
      }; 

      // 删除照片
      const handleDeleteImg = (imgItem: any, index: number) => {
        console.log(files)
        let newFiles = files.map((item: any, index: number) => {
            if(item === imgItem){
              // 当点击删除图标的时候,图片与页面图片list内容存在相同的时候删除
              return null
            }else{
              return item
            }
        }).filter((text: any)=>text)
        setFiles(newFiles)
      }

      return(
        ...
        // 照片
        <Flex>
          <img
            src={require('@/assets/icons/camera.png')}
            onClick={() => chooseImage()}
            style={{ width: 53, height: 53, marginRight: 10 }}
          />
          {files &&
            files.map((item: any, index: number) => {
              let imgSrc = ''
              if(item.indexOf('data:image/jpg;base64,') !== -1){
                // 当它包含的时候
                imgSrc = item
              }else{
                // 当它不包含的时候(判断是否是安卓手机)
                const { isAndroid } = deviceOs()
                if(!isAndroid){
                  imgSrc = 'data:image/jpg;base64,'+item
                }
                imgSrc = item
              }
              return (
                <div style={{ marginRight: 10 }} key={index}>
                  <img
                    src={imgSrc}
                    alt="img"
                    style={{ width: 53, height: 53, position: 'relative' }}
                  />
                  <img
                    src={require('@/assets/icons/fail.png')}
                    alt="删除图标"
                    style={{
                      width: 14,
                      height: 14,
                      position: 'absolute',
                      margin: '-4px 0px 0px -10px',
                      zIndex:9999999
                    }}
                    onClick={() => handleDeleteImg(item, index)}
                  />
                </div>
              );
            })}
        </Flex>
        ...
        // 点击新增参会人的时候
        <Flex
          className={styles.add}
          onClick={() => {
            let data = [...people];
            data.push({
              CarryClientName: '', //医生姓名
              CarryClientTitle: [], //职称
              id: crypto.guid(),
              CarryDeptName: (doctorData && [doctorData.DeptID, doctorData.DeptName]) || [], // 科室
              InstitutionName: (doctorData && doctorData.InstitutionName) || '', //所在医院
            });
            setPeople(data);
          }}
        >
          + 增加参会人
        </Flex>
      )
```

- 实现思路:
(1):利用UUID生成主键ID.
(2):微信上传图片必须发版才能测试,所以要联调很多次.然后需要注意的就是要判断是安卓还是ios.返回值不一样的.
(3):选择框的默认值是数组.后端返回的数据是汉字.需要去比出对应的数组.再赋值.
例:`<Picker
      data={positionData}
      extra="请选择"
      title="请选择职称"
      value={people[index].CarryClientTitle}
      onOk={v => {
        handleChange(index, 'CarryClientTitle', v);
      }}
      className={styles.picker}
    >
      <List.Item arrow="horizontal">
        <div>
          <span className={styles.red}>{'*'}</span>
          职称
        </div>
      </List.Item>
    </Picker>`
(4):在初始化的时候,先赋值一遍.参考`getInfoData`方法