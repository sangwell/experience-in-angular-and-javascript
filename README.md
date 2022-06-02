# Experience In Angular&Javascript
介绍一些Angular开发中遇到的问题及解决办法

## 1. 同时处理多个http请求
业务场景：页面中包含多个独立的下拉框，需要同时加载下拉列表。
```javascript
import {Observable} from 'rxjs/Observable';
import 'rxjs/add/observable/forkJoin';
import 'rxjs/add/observable/merge';
import 'rxjs/add/observable/zip';

let http1 = this.http.get('url');
let http2 = this.http.get('url');
let http3 = this.http.get('url');

Observable.forkJoin(http1, http2, http3).subscribe((res) => {
      console.log(res)
    })

Observable.zip(http1, http2, http3).subscribe(([res1, res2, res3]) => {
      console.log(res1)
      console.log(res2)
      console.log(res3)
    })

Observable.zip(http1, http2, http3).subscribe((res) => {
      console.log(res)
    })

Observable.merge(http1, http2, http3).subscribe((res) => {
      console.log(res)
    })
```

## 2. 在Promise中返回Observable
业务场景：这个问题是在Ionic开发中遇到的，当时在做一个demo，要求api地址是在界面上配置，做法是将api地址通过Storage库保存在app缓存，客户端发http请求的时候先去缓存中取api地址
```javascript
import {Observable} from 'rxjs';
import 'rxjs/add/observable/fromPromise';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/mergeMap';

post(url, parametersModel): Observable<any> {
    //this.storage.get(...)返回一个Promise，this.http.post(...)返回一个Observable
    return Observable.fromPromise(this.storage.get('apiBaseUrl')).mergeMap(apiBaseUrl => {
      return this.http.post(apiBaseUrl + url, parametersModel, {
        headers: this.headerWithToken()
      });
    });
  }
```

## 3. Cannot read property 'xxx' of null解决办法
[Bad]
```javascript
var data = null;
if (data.name) { // TypeError: Cannot read property 'name' of null
    console.log('OK');
}
```
[Good]
```javascript
var data = null;
if (data && data.name) {
    console.log('OK');
}
```

## 4. 同时处理多个Promise
```javascript
getStorage(names: Array<string>): Promise<Array<string>> {
    return Promise.all([this.storage.get(names[0]), this.storage.get(names[1])]); // this.storage.get返回Promise
}
```

## 5. 数组去重
可以用es6新增的Set来实现，Set是一种数据结构，它和数组非常相似，但是成员的值都是唯一的。我们可以先把它转化成Set，再配合...解构运算符重新转化为数组，达到去重的目的。
```javascript
const arr = [1, 1, 2, 2, 3, 4, 5, 5];
const new_arr = [...new Set(arr)];
console.log(new_arr); // 输出[1, 2, 3, 4, 5]
```

## 6. 变量作用域提升
变量作用域提升，也就是通过var申明的变量，会拿到该作用于的最顶层，此时变量只是申明，并没有赋值，所以打印该变量为undefined。
let就不存在变量作用域提升
```javascript
console.log(str); // 输出undefined
var str = '123456';
```
[等价于]
```javascript
var str; // str为undefined
console.log(str); // 输出undefined
str = '123456';
```

## 7. 用Promise对象实现的 Ajax 操作
```javascript
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('出错了', error);
});
```

## 8. 通过改变数组length清空、截取数组
```javascript
const arr = ['A','B','C','D','E','F'];
arr.length = 3;
console.log(arr); //=> ['A','B','C']

arr.length = 0;
console.log(arr); //=> []
```

## 9. 创建纯对象
```javascript
const pureObject = Object.create(null);
console.log(pureObject); //=> {}
```

## 10.链式调用
类的方法最后返回 this
```javascript
    class Person {
        constructor(name, age, tall) {
            this.name = name;
            this.age = age;
            this.tall = tall;
        }

        setName(name) {
            this.name = name;
            return this;
        }

        setAge(age) {
            this.age = age;
            return this;
        }

        setTall(tall) {
            this.tall = tall;
            return this;
        }

        save() {
            console.log(this.name, this.age, this.tall);
            return this;
        }
    }

    const myself = new Person();
    console.log(myself);
    myself.setAge(20).setName('Jack').setTall(3).save();
```

## 11.DIV垂直均分
```javascript
.father{
      display:flex;
      flex-direction: column;
      div{
            flex:1;
      }
}
```


## 12.数组根据某个字段排序
```javascript
arr.sort((a,b)=>{
	return b.LayoutPosition - a.LayoutPosition
});
```

## 13.滑动条控制
```javascript
function scrollToBottom() {
        console.log(document.getElementById('content').scrollHeight);
        const height = document.getElementById('content').scrollHeight;
        document.getElementById('content').scrollTo(0, height);
    }
```

## 14.对0值的判断
```javascript
const flag = 0;
console.log(flag === true);// false
console.log(flag || flag === 0);// true
```

## 15.div内部垂直居中
```javascript
div{
	display: flex;
	justify-content: center;
	align-items: Center;
}
```

## 16.自定义滑动条
```javascript
<input type="range" min="0" max="100"/>

input[type=range] {
    -webkit-appearance: none;
    width: 100%;
    border-radius: 10px; /*这个属性设置使填充进度条时的图形为圆角*/
  }
  /*滑块*/
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
  }
  /*滑动轨道*/
  input[type=range]::-webkit-slider-runnable-track {
    height: 12px;
    border-radius: 10px; /*将轨道设为圆角的*/
    background: #bdbdbd;
    //box-shadow: 0 1px 1px #def3f8, inset 0 .125em .125em #0d1112; /*轨道内置阴影效果*/
  }
  input[type=range]:focus {
    outline: none;
  }
  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    height: 24px;
    width: 24px;
    margin-top: -5px; /*使滑块超出轨道部分的偏移量相等*/
    background: #007aff;
    border-radius: 50%; /*外观设置为圆形*/
    //border: solid 0.125em rgba(205, 224, 230, 0.5); /*设置边框*/
    //box-shadow: 0 .125em .125em #3b4547; /*添加底部阴影*/
    border: 6px solid #c8e0fe;
  }
```

## 17.溢出，省略号
```javascript
    max-width: 100px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
```

## 18.模板字符串
```javascript
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
```

## 19.async-promise
```javascript
async test() {
    await this.getThree().then(data => {
      console.log(data);
    });
    await this.getTwo().then(data => {
      console.log(data);
    });
    await this.getOne().then(data => {
      console.log(data);
    });
  }

  getOne() {
    return this.http.get('http://localhost:50177/api/Test/GetOne').toPromise();
  }

  getTwo() {
    return this.http.get('http://localhost:50177/api/Test/GetTwo').toPromise();
  }

  getThree() {
    return this.http.get('http://localhost:50177/api/Test/GetThree').toPromise();
  }
```

## 20.subject-subscribe
```javascript
private subject: any;

this.subject = this.subjectService.getTreeDevice().subscribe(deviceSNs => {
      this.getDevicesAndShowMarker(deviceSNs);
    });

this.subject.unsubscribe();
```

## 21.数组删除项
```javascript
const index = this.list.findIndex(item => {
          return item.Id === id;
        });
this.list.splice(index, 1);
this.list = [...this.list];
```

## 22.使用 Promise 实现异步 Either
```javascript
const exists = x => x != null;
const ifExists = value => exists(value) ?
  Promise.resolve(value) :
  Promise.reject(`Invalid value: ${ value }`);
ifExists(null).then(log).catch(log); // Invalid value: null
ifExists('hello').then(log).catch(log); // hello
```
## 23.ExpressionChangedAfterItHasBeenCheckedError: Expression has changed after it was checked. Previous value: 'ngIf: false'. Current value: 'ngIf: true'.
### 解决办法
```javascript
  constructor(public cdRef: ChangeDetectorRef) {
  }
  
  ngAfterViewChecked() {
    this.cdRef.detectChanges();
    this.isLoading = true
  }
```

## 24.async-继发和并发的写法
```javascript
async function aaa(){
  let a = await some1()
  let b = await some2()
//这样的写法是继发的 相当于A执行完毕后再执行B
}
async function aaa(){
  let a = some1()
  let b = some2()
  let aDone = await a
  let bDone = await b
//这样的写法是并发的 无先后顺序
}

// 并发的两种写法
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

## 25.在angular中监测window窗口变化
```javascript
import {fromEvent} from 'rxjs';

fromEvent(window, 'resize').subscribe(data => {
      console.log(data);
    });
    
```

## 26.angular组件的input参数如果绑定一个方法，这个方法会不断执行
## 27.*ngFor="let item of functionA()"，这种情况functionA方法也会不断执行

## 28.Angular中如何脏值检测
Angular 无法检测 property 的添加或移除。由于 Angular 会在初始化实例时对 property 执行 getter/setter 转化，所以 property 必须在 data 对象上存在才能让 Vue 将它转换为响应式的。
有时你可能需要为已有对象赋值多个新 property，比如使用 Object.assign() 或 _.extend()。但是，这样添加到对象上的新 property 不会触发更新。在这种情况下，你应该用原对象与要混合进去的对象的 property 一起创建一个新的对象。
```javascript
// 代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```
Angular 不能检测以下数组的变动：

当你利用索引直接设置一个数组项时，例如：this.items[indexOfItem] = newValue
当你修改数组的长度时，例如：this.items.length = newLength

```javascript
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

## 29.Promise使用AsyncPipe
### html
```javascript
<div>{{promise | async}}</div>
```
### typescript
```javascript
  promise: Promise<string>;

  constructor() {
    this.promise = this.getPromise();
  }
  
  getPromise(): Promise<string> {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve('This is promise data');
      }, 2000);
    });
  }
```

## 30.Observable使用AsyncPipe
### html
```javascript
<div>{{observable | async}}</div>
```
### typescript
```javascript
  observable: Observable<number>;

  constructor() {
    this.observable = this.getObservable();
  }

  getObservable(): Observable<number> {
    return of(123);
  }
```

## 31.AsyncPipe例子(1)
### html
```javascript
<div>
  <h4>Heroes:</h4>
  <ul>
    <li *ngFor="let hero of heroes$ | async">
      {{hero.name}}
    </li>
  </ul>
  <h4>Hero:</h4>
  <p>
    <span *ngIf="hero$">{{(hero$ | async).id}}</span>
    <span>{{(hero$ | async)?.name}}</span>
  </p>
</div>
```
### typescript
```javascript
  heroes$: Observable<Hero[]>;
  hero$: Observable<Hero>;
  
  ngOnInit() {
    setTimeout(() => {
      this.heroes$ = this.getHeroes();
      this.hero$ = this.getHero();
    }, 2000);
  }

  getHeroes(): Observable<Hero[]> {
    return of([
      {id: 1, name: 'AAAA'},
      {id: 2, name: 'BBBB'},
      {id: 3, name: 'CCCC'},
      {id: 4, name: 'DDDD'},
    ]);
  }

  getHero(): Observable<Hero> {
    return of({id: 2, name: 'BBBB'});
  }
```

## 32.AsyncPipe例子(2)
### html
```javascript
<div>
  <p>{{ (person$ | async)?.id }}</p>
  <p>{{ (person$ | async)?.title }}</p>
  <p>{{ (person$ | async)?.body }}</p>
</div>
```
### typescript
```javascript
  person$: Observable<{ id: number; title: string; body: string }>;

  ngOnInit() {
    // 调用3次
    this.person$ = this.http.get('https://jsonplaceholder.typicode.com/posts/1');
    
    // 改进 ,只调用1次 ,使用 RxJS 中的共享操作符，它在内部调用 publish().refCount()
    this.person$ = this.http.get('https://jsonplaceholder.typicode.com/posts/1').pipe(share());
  }

```

## 33.Promise vs Observable

### Promise

返回单个值

不可取消的

### Observable

随着时间的推移发出多个值

可以取消的

支持 map、filter、reduce 等操作符

延迟执行，当订阅的时候才会开始执行


## 34.Angular订阅销毁
```javascript
export class AbcComponent implements OnDestroy {
	onDestroy$ = new Subject<boolean>();
	
	ngOnDestroy() {
		this.onDestroy$.next(true);
		this.onDestroy$.complete();
	    }
	    
	subscribeAlarmShielding() {
		this.subjectService.alarmShieldingSubject.pipe(
		    tap(res => {
			// ...
		    }),
		    takeUntil(this.onDestroy$)
		).subscribe();
	    }


}
```

## 35.定义一个比较器
```javascript
    function compare() {
        return (object1, object2) => {
            const value1 = object1['(\'age\')'];
            const value2 = object2['(\'age\')'];
            if (value2 < value1) {
                return -1;
            }
            else if (value2 > value1) {
                return 1;
            }
            else {
                return 0;
            }
        }
    }
```

## 36.数组group
```javascript
const sorted = this.groupBy(this.list, (item: any) => {
      return [item.group];//按照group进行分组
    });

  groupBy(array: any[], f: any) {
    const groups: any = {};
    array.forEach(function (o) { //注意这里必须是forEach 大写
      const group = JSON.stringify(f(o));
      groups[group] = groups[group] || [];
      groups[group].push(o);
    });
    return Object.keys(groups).map(function (group) {
      return groups[group];
    });
  }
```

## 37.Promise同步请求
```
[service]
getSth() {
        return this.http.get(url).toPromise();
    }
[调用]
async run() {
        const result = await this.getSth();
        this.search();
    }

```
