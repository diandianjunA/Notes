# ES6模块化

模块化的好处：

大家遵守同样的模块化规范写代码，降低了沟通成本，极大方便了各个模块之间的相互调用，利人利己

ES6模块化规范是浏览器端与服务器端通用的模块化开发规范，它的出现极大地降低了前端开发者的模块化学习成本

ES6模块化规范中定义：

- 每个js文件都是一个独立的模块
- 导入其他模块成员使用import关键字
- 向外共享模块使用export关键字

## 在node.js中体验和使用ES6模块化语法

1. 确保安装了v14.15.1或更高版本的node.js
2. 在package.json中加入type: module

## 导入导出

- 默认导出：export default 默认导出成员
- 默认导入：import 接收名称 from '模块标识符'
- 注意事项：每个模块中只能使用一次默认导出



- 按需导出：export 导出成员
- 按需导入：import {s1,s2,s3} from '模块标识符'
- 注意事项：
  - 每个模块中可以多次按需导出
  - 按需导入的成员名称必须和按需导出的成员名称一致
  - 按需导入时，可以用as关键字进行重命名，如import {s1,s2 as str2,s3} from '模块标识符'
  - 按需导入和默认导入可以一起使用
- 直接导入并执行模块中的代码：import 'xxx'则xxx中的代码会直接在这个位置执行

## Promise

1. 回调地狱

   多层函数被相互嵌套，就形成了回调地狱

   比如：

   ```javascript
   setTimeout(()=>{
       console.log('延时1秒后输出')
       setTimeout(()=>{
     		console.log('延时2秒后输出')
       	setTimeout(()=>{
         		console.log('延时3秒后输出')
       	},3000)
   	},2000)
   },1000)
   ```

   回调地狱的缺点：

   - 代码耦合性太强，牵一发而动全身，难以维护
   - 大量冗余的代码相互嵌套，代码可读性变差

2. Promise的基本概念

   1. Promise是一个构造函数

      - 我们可以创建一个Promise实例:const p=new Promise()
      - new出来的实例对象代表一个异步操作

   2. Promise.prototype上包含一个.then()方法

   3. .then()方法用来预先指定成功和失败的回调函数

      p.then(成功的回调函数，失败的回调函数)

      p.then(result=>{},error=>{})

   4. .catch(err=>{})可捕获到发生的错误

3. Promise.all()方法

   会发起并行的Promise异步操作，等所有的异步操作结束后才会执行下一步的.then()操作
   
   all()内可以放一个对象，里面有很多个Promise对象，.then()中也可以用一个数组来接收多个Promise操作的结果

    例如：

    ```javascript
    const p{
        Promise p1;
        Promise p2;
        Promise p3;
    }
    Promise.all(p).then(([r1,r2,r3])=>{
        console.log(r1,r2,r3)
   })
    ```

4. Promise.race()方法

    会发起并行的Promise异步操作，只要任何一个异步操作完成，就立即执行下一步.then()操作，类似于赛跑机制，获取的结果也是最快的结果

## async/await

用于简化Promise的异步操作

类似于在这个位置阻塞Promise操作，效果类似于在后面代码中加.then()

```javascript
const r1 =await xxxx.fuc()
```

前提是方法返回的是一个Promise对象

await后面的代码全都异步执行，类似于每一段代码都加一个.then()

# EventLoop

1. 问题：由于JavaScript是单线程执行任务的语言，所以如果前一个任务非常耗时，后一个任务必须一直等待，从而导致程序假死问题
2. 同步任务和异步任务的执行过程
    1. 同步任务由主线程次序完成
   2. 异步任务委托给宿主环境执行
   3. 已完成的异步任务对应的回调函数会被加入到任务队列中等待执行
   4. 主线程的执行栈被清空后，会读取任务队列中的回调函数，次序执行
   5. 主线程不断重复上面第四步
3. EventLoop的基本概念：EventLoop就是上述的任务队列

# 宏任务与微任务

1. 宏任务
    - 异步Ajax请求
    - setTimeout,setInterval
    - 文件操作
    - 其他宏任务
2. 微任务
    - Promise.then,.catch,.finally
    - process.nextTick
    - 其他微任务
3. 宏任务和微任务的执行顺序

   每一个宏任务执行完之后，都会检查是否存在待执行的微任务，如果有，则执行完所有微任务后再继续执行下一个宏任务

