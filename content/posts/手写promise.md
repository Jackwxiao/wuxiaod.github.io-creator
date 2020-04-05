---
title: "手写Promise"
date: 2020-03-31T21:31:29+08:00
draft: false
---

## 自定义实现 Promise

### promise 构造函数的实现：

```

/* 
Promise 构造函数
executor: 内部同步执行的函数
(resolve, reject) => {}
*/

function Promise(executor) {
    const self = this
    self.status = 'pending' 
        <!-- 状态值, 初始状态为 pending, 成功了变为 resolved, 失败了变为 rejected -->
    self.data = undefined
        <!-- 用来保存成功 value 或失败 reason 的属性 -->
    self.callbacks = [] 
        <!-- 用来保存所有待调用的包含 onResolved 和 onRejected 回 调函数的对象的数组  -->

        /* 异步处理成功后应该调用的函数 value: 将交给 onResolve()的成功数据 */

    function resolve(value) {
        if (self.status !== 'pending') { 
            <!-- 如果当前不是 pending, 直接结束 -->
            return
        } 
            <!-- 立即更新状态, 保存数据 -->
        self.status = 'resolved'
        self.data = value 
            <!-- 异步调用所有待处理的 onResolved 成功回调函数  -->
        if (self.callbacks.length > 0) {
            setTimeout(() => {
                self.callbacks.forEach(obj => {
                    obj.onResolved(value)
                })
            })
        }
    }

    /*异步处理失败后应该调用的函数 reason: 将交给 onRejected()的失败数据 */

    function reject(reason) {
        if (self.status !== 'pending') {
                <!-- 如果当前不是 pending, 直接结束 -->
            return
        }
                <!-- 立即更新状态, 保存数据 -->
        self.status = 'rejected'
        self.data = reason
                <!-- 异步调用所有待处理的 onRejected 回调函数 -->
        setTimeout(() => {
            self.callbacks.forEach(obj => {
                obj.onRejected(reason)
            })
        })
    } try {
                <!-- 立即同步调用 executor()处理 -->
        executor(resolve, reject)
    } catch (error) {
                <!-- 如果出了异常, 直接失败 -->
        reject(error)
    }
}

```

### promise.then() / catch() 的实现：

```

/*
为 promise 指定成功/失败的回调函数
函数的返回值是一个新的 promise 对象
*/

Promise.prototype.then = function (onResolved, onRejected) {
    const self = this
        <!-- 如果 onResolved/onRejected 不是函数, 可它指定一个默认的函数 -->
    onResolved = typeof onResolved === 'function' ? onResolved : value => value
        <!-- 指定返回的 promise 为一个成功状态, 结果值为 value -->
    onRejected = typeof onRejected === 'function' ? onRejected : reason => {
        throw reason
    } 
        <!-- 指定返回的 promise 为一个失败状态, 结果值为 reason -->
        <!-- 返回一个新的 promise 对象 -->
    return new Promise((resolve, reject) => {
        /*
        专门抽取的用来处理 promise 成功/失败结果的函数
        callback: 成功/失败的回调函数
        */
        
        function handle(callback) {
                <!-- 1. 抛出异常 ===> 返回的 promise 变为 rejected -->
            try {
                const x = callback(self.data)
                    <!-- 2. 返回一个新的 promise ===> 得到新的 promise 的结果值作为返回的 -->
                promise 的结果值
                if (x instanceof Promise) {
                    x.then(resolve, reject)
                    <!-- 一旦 x 成功了, resolve(value), 一旦 x 失败了: reject(reason) -->
                } else {
                    <!-- 3. 返回一个一般值(undefined) ===> 将这个值作为返回的 promise 的成功值 -->
                    resolve(x)
                }
            } catch (error) {
                reject(error)
            }
        }
        if (self.status === 'resolved') {
                 <!-- 当前 promise 已经成功了 -->
            setTimeout(() => {
                handle(onResolved)
            })
        } else if (self.status === 'rejected') {
                 <!-- 当前 promise 已经失败了 -->
            setTimeout(() => {
                handle(onRejected)
            })
        } else { 
                <!-- 当前 promise 还未确定 pending -->
                <!-- 将 onResolved 和 onRejected 保存起来 -->
            self.callbacks.push({
                onResolved(value) {
                    handle(onResolved)
                },
                onRejected(reason) {
                    handle(onRejected)
                }
            })
        }
    })
}

/*
为 promise 指定失败的回调函数
是 then(null, onRejected)的语法糖
*/

Promise.prototype.catch = function (onRejected) {
    return this.then(null, onRejected)
}

```

### promise.resolve()/reject() 的实现：

```

/*
返回一个指定了成功 value 的 promise 对象
value: 一般数据或 promise
*/

Promise.resolve = function (value) {
    return new Promise((resolve, reject) => {
        if (value instanceof Promise) {
            value.then(resolve, reject)
        } else {
            resolve(value)
        }
    })
}

/*
返回一个指定了失败 reason 的 promise 对象
reason: 一般数据/error
*/

Promise.reject = function (reason) {
    return new Promise((resolve, reject) => {
        reject(reason)
    })
}

```

### Promise.all/race()的实现:

```

/*
返回一个新的 promise 对象, 只有 promises 中所有 promise 都产生成功 value 时, 才最
终成功, 只要有一个失败就直接失败
*/

Promise.all = function (promises) {
        <!-- 返回一个新的 promise -->
    return new Promise((resolve, reject) => {
            <!-- 已成功的数量 -->
        let resolvedCount = 0
            <!-- 待处理的 promises 数组的长度 -->
        const promisesLength = promises.length
            <!-- 准备一个保存成功值的数组 -->
        const values = new Array(promisesLength)
            <!--遍历每个待处理的 promise -->
        for (let i = 0; i < promisesLength; i++) {
                <!-- promises 中元素可能不是一个数组, 需要用 resolve 包装一下 -->
            Promise.resolve(promises[i]).then(
                value => {
                        <!-- 成功当前 promise 成功的值到对应的下标 -->
                    values[i] = value
                        <!-- 成功的数量加 1 -->
                    resolvedCount++
                        <!-- 一旦全部成功 -->
                    if (resolvedCount === promisesLength) {
                        <!--  将所有成功值的数组作为返回 promise 对象的成功结果值 -->
                        resolve(values)
                    }
                },
                reason => {
                    <!-- 一旦有一个promise产生了失败结果值, 将其作为返回promise对象的失败结果值 -->
                    reject(reason)
                }
            )
        }
    })
}
/*
返回一个 promise，一旦某个 promise 解决或拒绝， 返回的 promise 就会解决或拒绝。
*/
Promise.race = function (promises) {
    <!-- 返回新的 promise 对象 -->
    return new Promise((resolve, reject) => {
        <!-- 遍历所有 promise -->
        for (var i = 0; i < promises.length; i++) {
            Promise.resolve(promises[i]).then(
                <!-- (value) => { 只要有一个成功了, 返回的 promise 就成功了 -->
                    resolve(value)
                },
                <!-- (reason) => { 只要有一个失败了, 返回的结果就失败了 -->
                    reject(reason)
                }

            )
        }
    })
}

```

### Promise.resolveDelay()/rejectDelay()的实现:


```

/*
返回一个延迟指定时间才确定结果的 promise 对象
*/
Promise.resolveDelay = function (value, time) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (value instanceof Promise) { 
            <!-- 如果 value 是一个 promise, 取这个promise 的结果值作为返回的 promise 的结果值 -->
            value.then(resolve, reject)
            <!-- 如果 value 成功, 调用resolve(val), 如果value 失败了, 调用 reject(reason) -->
            } else {
                resolve(value)
            }
        }, time);
    })
}
/*
返回一个延迟指定时间才失败的 Promise 对象。
*/
Promise.rejectDelay = function (reason, time) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            reject(reason)
        }, time)

    })
}

```
### async 函数：

1. 函数的返回值为 promise 对象
2. promise 对象的结果由 async 函数执行的返回值决定

### await 表达式：

1. await 右侧的表达式一般为 promise 对象, 但也可以是其它的值
2. 如果表达式是 promise 对象, await 返回的是 promise 成功的值
3. 如果表达式是其它值, 直接将此值作为 await 的返回值

注意： 
1. await 必须写在 async 函数中, 但 async 函数中可以没有 await
2. 如果 await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理

