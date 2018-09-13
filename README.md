# Experience In Angular&Javascript
介绍一些Angular开发中遇到的问题及解决办法

## 1. 同时处理多个http请求
业务场景：页面中包含多个独立的下拉框，需要同事加载下拉列表。
```javascript
import {Observable} from 'rxjs/Observable';
import 'rxjs/add/observable/forkJoin';
import 'rxjs/add/observable/merge';
import 'rxjs/add/observable/zip'

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


