# async/await 语法

#### 作用
> 解决异步嵌套问题，传说中的终极解决方案

#### 异步问题
* async/await 实现读取文件 a.txt、b.txt、c.txt
    ```
    var fs = require('fs');

    function doReadFile(file) {
        return new Promise((resolve,reject)=>{
            fs.readFile(file,function(err,data){
                resolve(data.toString())
            })
        })
    }

    // 定义 async 函数
    async function asyncFn() {
        // 使用 await 执行异步代码
        let data1 = await doReadFile('a.txt')
        console.log(data1)
        let data2 = await doReadFile('b.txt')
        console.log(data2)
        let data3 = await doReadFile('c.txt')
        console.log(data3)
    }

    asyncFn()
    ```

### 特点
1. `await` 只能放在 `async` 函数中
2. `await` 后面跟 `Promise` 对象并且执行并且通过 `reslove` 返回结果，也可以是其他类型，但是如果是其他类型就直接执行
3. `async` 返回值是一个 `Promise` 对象
4. 只要 `await` 后面的 `Promise` 执行引发 `reject`，那么整个 `async` 函数就会终端操作，`async` 返回值 `Promise` 对象引发 `reject`
5. 通过 `try/catch` 或者 `Promise` 的 `catch` 解决 `async` 函数终端操作