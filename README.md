# experience-in-angular
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
```
    var data = null;
    if (data.name) {
      console.log('OK');
    }
```



