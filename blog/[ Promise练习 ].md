## 2020/07/13小记

**Promise练习**

请写出以下代码输出结果:
    
    async function async1(){
        console.log('async1 start');
        await async2();
        console.log('async1 end');
    }

    async function async2(){
        console.log('async2');
    }

    console.log('script start')

    setTimeout(()=>{
        console.log('settimeout');
    },0)

    async1();

    new Promise((resolve)=>{
        console.log('promise1');
        resolve();
    })
    .then(()=>{
        console.log('promise2');
    });

    console.log('script end')
  
输出结果:
```
    "script start"
    "async1 start"
    "async2"
    "promise1"
    "script end"
    "async1 end"
    "promise2"
    "settimeout"
```
执行原理分析:
- 先走同步队列，再走异步队列（包含微观任务队列和宏观任务队列）。同步任务结束后，先处理微观任务后处理宏观任务

- promise的构造函数是同步执行，promise.then中的函数是异步执行。
- settimeout最后执行