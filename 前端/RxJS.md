## 1. 什么是RxJS

RxJS是一个响应式编程库,它提供了一套API,用来处理异步事件流;它是一个基于观察者模式的库,它使用可观察对象来处理异步事件流;
[官方文档](https://rxjs-dev.firebaseapp.com/)

### Observable(可观察对象)

它可以被订阅,并且可以发出值.它是一个类,可以通过new操作符等方式创建;可以是DOM事件,定时器,ajax请求,webSocket连接等等;

### Observer(观察者)

它可以订阅一个可观察对象, 相当于一个回调函数,它有三个回调函数,
分别是next,error,complete;
用来接收可观察对象发出的值,错误,完成的通知; 可以在其中加上相应的处理逻辑;

### Subscription(订阅)

它是一个对象,它表示一个可观察对象的执行,并且可以取消这个执行.

### Operators(操作符)

它是一个函数,它接收一个可观察对象,并且返回一个新的可观察对象.

### Subject(主题)

它是一个可观察对象,同时也是一个观察者；
它可以订阅一个可观察对象,并且可以接收可观察对象发出的信息,并且可以通过广播的方式将信息发送给所有订阅它的观察者.

### Schedulers(调度器)

Schedulers是一个调度器,它是一个对象,它控制着可观察对象的执行.
用来控制可观察对象的执行,比如:控制可观察对象的执行顺序,控制可观察对象的执行时间,控制可观察对象的执行线程.

## 2. RxJS的使用

### 2.1 创建可观察对象

#### 2.1.1 通过new操作符创建

```typescript
import {Observable} from 'rxjs';

const observable = new Observable(observer => {
    observer.next(1); // 发出值
    observer.next(2);
    observer.next(3);
    setTimeout(() => {
        observer.next(4);
        observer.complete(); // 完成
    }, 1000);
});
```

#### 2.1.2 通过of操作符创建

```typescript
import {of} from 'rxjs';

const observable = of(1, 2, 3, 4);
```

#### 2.1.3 通过from操作符创建

```typescript
import {from} from 'rxjs';

const observable = from([1, 2, 3, 4]);
```

#### 2.1.4 通过interval操作符创建

```typescript
import {interval} from 'rxjs';

const observable = interval(1000);
```

#### 2.1.5 通过timer操作符创建

```typescript
import {timer} from 'rxjs';

const observable = timer(1000, 1000);
```

#### 2.1.6 通过ajax操作符创建

```typescript
import {ajax} from 'rxjs/ajax';

const observable = ajax('https://api.github.com/users?per_page=5');
```

### 2.2 订阅可观察对象

```typescript
import {Observable} from 'rxjs';

const observable = new Observable(observer => {
    observer.next(1); // 发出值
    observer.next(2);
    observer.next(3);
    setTimeout(() => {
        observer.next(4);
        observer.complete(); // 完成
    }, 1000);
});


const observer = {
    next: x => console.log('got value ' + x),
    error: err => console.error('something wrong occurred: ' + err),
    complete: () => console.log('done'),
};

observable.subscribe(observer);

```

### 2.3 取消订阅

```typescript
import {Observable} from 'rxjs';

const observable = new Observable(observer => {
    //...
});

const subscription = observable.subscribe({
    //...
});

setTimeout(() => {
    subscription.unsubscribe();
}, 1500);
```

### 2.4 操作符

所有操作符都可以由rxjs/operators模块导入,并且可以通过pipe方法来使用.
动画演示网站:
[Rx Visualizer](https://rxviz.com/) /
[Launchpad for RxJS](https://reactive.how/rxjs/)

```typescript
import {Observable} from 'rxjs';
import {map, filter, take} from 'rxjs/operators';

const observable = new Observable(observer => {
    observer.next(1); // 发出值
    observer.next(2);
    observer.next(3);
    setTimeout(() => {
        observer.next(4);
        observer.complete(); // 完成
    }, 1000);
});

// map 将值映射为另一个值
observable.pipe(map(x => x * x)).subscribe(x => console.log(x));
// filter 过滤
observable.pipe(filter(x => x % 2 === 0)).subscribe(x => console.log(x));
// take 用来限制可观察对象的发出值的个数
observable.pipe(take(2)).subscribe(x => console.log(x));
// 多个操作符可以通过pipe方法来连接，执行顺序是从左到右
observable.pipe(map(x => x * x), filter(x => x % 2 === 0)).subscribe(x => console.log(x));
//...
```

### 2.5 操作符分类

当不知道要使用哪个操作符的时候,可以通过操作符分类来查找.
或者可以通过[operator-decision-tree](https://rxjs-dev.firebaseapp.com/operator-decision-tree)来查找.

#### 2.5.1 创建操作符(Creation Operators)

创建操作符用来创建一个新的可观察对象.

| Operators        | 说明                                                                |
|------------------|-------------------------------------------------------------------|
| from             | 从一个Promise,数组,可迭代对象,类数组对象,可观察对象,甚至是其他的可转换为可观察对象的对象中创建一个可观察对象.     |
| fromEvent        | 从一个DOM事件中创建一个可观察对象.                                               |
| fromEventPattern | 从一个DOM事件中创建一个可观察对象. 与fromEvent不同的是,fromEventPattern可以自定义事件的添加和移除. |
| interval         | 会在每个指定的时间间隔发出一个自增的数字.                                             |
| of               | 会发出你所指定的任意数量的参数.                                                  |
| range            | 会发出一个指定范围内的自增的数字.                                                 |
| throwError       | 会立即发出一个错误.                                                        |
| timer            | 会在一个指定的时间间隔后发出一个数字.                                               |
| ajax             | 会发出一个AJAX请求的结果.                                                   |
| binCallback      | 会发出一个Node.js风格的回调函数的结果.                                           |
| bindNodeCallback | 会发出一个Node.js风格的回调函数的结果.                                           |
| defer            | 会在被订阅的时候创建一个新的可观察对象.                                              |

#### 2.5.2 转换操作符(Transformation Operators)

转换操作符用来将可观察对象的值进行转换.

**常用的转换操作符:**

| Operators    | 说明                                                |
|--------------|---------------------------------------------------|
| buffer       | 将可观察对象的值收集到一个数组中.                                 |
| bufferCount  | 将Observable发出的值收集到一个长度固定的数组中.                     |
| bufferTime   | 将可观察对象的值收集到一个数组中,并且每个数组的时间间隔是由第二个参数决定的.           |
| bufferToggle | 将可观察对象的值收集到一个数组中,并且每个数组的时间间隔是由另一个可观察对象决定的.        |
| bufferWhen   | 将Observable发出的值收集到一个数组中,并且每个数组的时间间隔是由另一个可观察对象决定的. |
| concatMap    | 将可观察对象的值转换为另一个可观察对象,并且将这些可观察对象发出的值进行合并.           |
| concatMapTo  | 将可观察对象的值转换为另一个可观察对象,并且将这些可观察对象发出的值进行合并.           |
| exhaustMap   | 将可观察对象的值转换为另一个可观察对象,并且只发出这些可观察对象的最新值.             |
| expand       | 将可观察对象的值转换为另一个可观察对象,并且将这些可观察对象发出的值进行合并.           |
| groupBy      | 将可观察对象的值进行分组.                                     |
| map          | 将可观察对象的值进行转换.                                     |
| mapTo        | 将可观察对象的值进行转换.                                     |
| mergeMap     | 将可观察对象的值转换为另一个可观察对象,并且将这些可观察对象发出的值进行合并.           |

#### 2.5.3 过滤操作符(Filtering Operators)

过滤操作符用来过滤可观察对象发出的值.

| Operators      | 说明                     |
|----------------|------------------------|
| audit          | 会在指定的时间间隔内发出可观察对象的最新值. |
| auditTime      | 会在指定的时间间隔内发出可观察对象的最新值. |
| debounce       | 会在指定的时间间隔内发出可观察对象的最新值. |
| debounceTime   | 会在指定的时间间隔内发出可观察对象的最新值. |
| distinct       | 会过滤掉可观察对象发出的重复值.       |
| distinctKey    | 会过滤掉可观察对象发出的重复值.       |
| elementAt      | 只发出可观察对象发出的第N个值.       |
| filter         | 只发出可观察对象发出的满足条件的值.     |
| first          | 只发出可观察对象发出的第一个值.       |
| ignoreElements | 会忽略掉可观察对象发出的所有值.       |
| last           | 只发出可观察对象发出的最后一个值.      |
| sample         | 会在指定的时间间隔内发出可观察对象的最新值. |

#### 2.5.4 合并操作符(Combination Operators)

合并操作符用来将多个可观察对象的值进行合并.

| Operators     | 说明                                               |
|---------------|--------------------------------------------------|
| combineLatest | 会将多个可观察对象的最新值进行合并.                               |
| concat        | 会将多个可观察对象的值进行合并,并且只有前一个可观察对象发出完成信号,才会订阅下一个可观察对象. |

#### 2.5.5 错误处理操作符(Error Handling Operators)

错误处理操作符用来处理可观察对象发出的错误.

| Operators   | 说明                                      |
|-------------|-----------------------------------------|
| catchError  | 如果可观察对象发出错误,则发出一个新的可观察对象.               |
| retry       | 如果可观察对象发出错误,则重新订阅这个可观察对象.               |
| retryWhen   | 如果可观察对象发出错误,则重新订阅这个可观察对象.               |
| throw       | 会立即发出一个错误.                              |
| timeout     | 如果可观察对象在指定的时间内没有发出值,则发出一个错误.            |
| timeoutWith | 如果可观察对象在指定的时间内没有发出值,则发出一个错误.            |
| using       | 会在可观察对象被订阅的时候创建一个资源,并在可观察对象完成的时候释放这个资源. |

#### 2.5.6 辅助操作符(Auxiliary Operators)

辅助操作符用来辅助可观察对象的操作.

| Operators     | 说明                                      |
|---------------|-----------------------------------------|
| delay         | 会在指定的时间间隔后发出可观察对象的值.                    |
| delayWhen     | 会在指定的时间间隔后发出可观察对象的值.                    |
| dematerialize | 会将可观察对象发出的通知转换为值.                       |
| materialize   | 会将可观察对象发出的值转换为通知.                       |
| observeOn     | 会在指定的调度器上发出可观察对象的值.                     |
| subscribeOn   | 会在指定的调度器上订阅可观察对象.                       |
| timeInterval  | 会将可观察对象发出的值转换为一个包含值和时间间隔的对象.            |
| timestamp     | 会将可观察对象发出的值转换为一个包含值和时间戳的对象.             |
| timeout       | 如果可观察对象在指定的时间内没有发出值,则发出一个错误.            |
| timeoutWith   | 如果可观察对象在指定的时间内没有发出值,则发出一个错误.            |
| using         | 会在可观察对象被订阅的时候创建一个资源,并在可观察对象完成的时候释放这个资源. |

#### 2.5.7 条件和布尔操作符(Conditional and Boolean Operators)

条件和布尔操作符用来对可观察对象的值进行条件判断.

| Operators      | 说明                         |
|----------------|----------------------------|
| defaultIfEmpty | 如果可观察对象没有发出值,则发出一个默认值.     |
| every          | 如果可观察对象的所有值都满足条件,则发出true.  |
| find           | 如果可观察对象发出的值满足条件,则发出这个值.    |
| findIndex      | 如果可观察对象发出的值满足条件,则发出这个值的索引. |
| isEmpty        | 如果可观察对象没有发出值,则发出true.      |

#### 2.5.8 算术和聚合操作符(Mathematical and Aggregate Operators)

算术和聚合操作符用来对可观察对象的值进行算术运算和聚合运算.

| Operators | 说明                    |
|-----------|-----------------------|
| count     | 计算Observable发出的值的个数.  |
| max       | 计算Observable发出的值的最大值. |
| min       | 计算Observable发出的值的最小值. |
| reduce    | 对Observable发出的值进行聚合.  |

### 2.6 Subject

```typescript
import {Subject} from 'rxjs';

// 创建一个Subject,之后可以通过它来订阅可观察对象
const subject = new Subject();
// 创建一个可观察的Observable对象
const observable = new Observable(observer => {
    observer.next(1);
    observer.next(2);
    observer.next(3);
    setTimeout(() => {
        observer.next(4);
        observer.complete();
    }, 1000);
});
// 订阅可观察对象，通过Subject来接收可观察对象发出的值,将信息广播给所有订阅者；
observable.subscribe(subject);
// 订阅Subject，通过Observer来接收Subject发出的值
var subscribe1 = subject.subscribe({
    next: (v) => console.log('observerA: ' + v)
});
var subscribe2 = subject.subscribe({
    next: (v) => console.log('observerB: ' + v)
});
// 取消订阅
subscribe1.unsubscribe();
subscribe2.unsubscribe();
```











