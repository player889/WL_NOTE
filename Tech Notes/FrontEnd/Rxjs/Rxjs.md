- ==Observable== 就是「可被觀察的」，比起 Observable 這個冷冰冰的說法，我更喜歡的一個說法是 stream，資料流。其實每一個 Observable 就是一個資料流，但什麼是資料流？你就想像成是會一直增加元素的陣列就好了，有新的事件發生就 push 進去。
- ==Observer== 就是所謂的「觀察者」，Observer 是 Observable 傳遞的各個值的消費者。 Observer 只是一組回呼(Callback)，對應於 Observable 傳遞的每種型別的通知：`next`、`error`  和  `complete`。下面是一個典型的 Observer 物件的例子：
    ```js
    const observer = {
    	next: (x) =>
    		console.log(
    			"[Observer](https://rxjs.angular.tw/api/index/interface/Observer) got a next value: " +
    				x
    		),
    	error: (err) =>
    		console.error(
    			"[Observer](https://rxjs.angular.tw/api/index/interface/Observer) got an error: " +
    				err
    		),
    	complete: () =>
    		console.log(
    			"[Observer](https://rxjs.angular.tw/api/index/interface/Observer) got a complete notification"
    		),
    };
    ```
    訂閱  `[Observable](https://rxjs.angular.tw/api/index/class/Observable)`  時，你也可以只提供下一個回呼(Callback)作為引數，而不用附屬於  `[Observer](https://rxjs.angular.tw/api/index/interface/Observer)`  物件，例如：
    ```js
    observable.subscribe((x) =>
    	console.log(
    		"[Observer](https://rxjs.angular.tw/api/index/interface/Observer) got a next value: " +
    			x
    	)
    );
    ```
- **Rx.Observable.prototype.pluck('target', 'value')**
    将输入的 event，输出成 event.target.value。
- **Rx.Observable.prototype.mergeMap()**
    将请求搜索结果输出回给 Observer 上进行渲染。
- **Rx.Observable.prototype.debounceTime(TIMES)**  
    表示经过 TIMES 毫秒后，没有流入新值，那么才将值转入下一个环节。这个与前面使用 setTimeout 来实现函数节流的方式有一致效果。
- **Rx.Observable.prototype.switchMap()**
    使用 switchMap 替换 mergeMap，将能取消上一个已无用的请求，只保留最后的请求结果流，这样就确保处理展示的是最后的搜索的结果。

---

- flatMap vs switchMap
    - 其實除了`flatMap`以外，還有另外一種方式叫做`switchMap`，他們的差別在於要怎麼把 Observable 給壓平。前者我們之前介紹過了，就是會把每一個二維的 Observable 都壓平，並且「每一個都執行」。
    - 而`switchMap`的差別在於，他永遠只會處理最後一個 Observable。拿我們的例子來說，假設第一個 request 還沒回來的時候，第二個 request 就發出去了，那我們的 Observable 就只會處理第二個 request，而不管第一個。
    - 第一個還是會發送，還是會接收到資料，只是接收到資料以後不會再把這個資料 emit 到 Observable 上面，意思就是根本沒人理這個資料了。

# exhaustMap

```ts
import { fromEvent, interval } from 'rxjs';
import { exhaustMap, take } from 'rxjs/operators';

const click$ = fromEvent(document, 'click');
const interval$ = interval(1000).pipe(take(5));

click$.pipe(
  exhaustMap(() => interval$)
).subscribe(console.log);
```

In this example, every time a click event happens (`click$`), it triggers a new interval observable (`interval$`). However, if multiple click events occur before an interval completes, the new events are ignored until the current interval completes.

==This is particularly useful in scenarios where you want to avoid concurrent requests or actions and ensure that the system handles one at a time.==

# Merge Map & SwitchMap & exhaustMap

```ts
Observable .fromEvent(DOM, 'click') .margeMap(() => ajax('url...')) );
```

switchMap => 當第一個 Observable 尚未處理完，又送出第二個 Observable 時，取消舊的訂閱，只想保留新的結果

```ts
Observable .fromEvent(DOM, 'click') .switchMap(() => ajax('url...')) );
```

exhaustMap => 當第一個 Observable 尚未處理完，又送出第二個 Observable 時，不管新的，只想保留舊的結果

```ts
Observable .fromEvent(DOM, 'click') .exhaustMap(() => ajax('url...')) );
```

Example:

```ts
Observable.fromEvent(scrollView, 'scroll')  // scroll 事件
  .map(event => event.target)        // 轉成被 scroll 的 DOM 物件
  .map(hasScrolled)                  // DOM 物件被 scroll 了幾 %
  .filter(percent => percent > 0.9)  // scroll 超過 90% 就繼續後面動作
  .exhaustMap(() => fetch('url...')) // 在第一個 Observable 處理完之前，不會處理訂閱的 Observable
  .take(3)    // 當接收到 3 次 response，這個 Observable 就直接 complete，不會再送出元素
  .subscribe(res => {
    // Do sometheing to change view
  })
);
```


# ForkJoin
Function to trigger multiple observables in parallel.

# Refactor - readable


* Let

![[Pasted image 20240105165146.png]]

# Example
## API-1 -> API-2

1. api1Call simulates the first API call, which returns an object with the name value.
2. api2Call simulates the second API call, using the name value obtained from the first call. The switchMap operator is used to switch to the second observable (api2Call) based on the result of the first observable (api1Call).


```ts
import { from, of } from 'rxjs';
import { switchMap } from 'rxjs/operators';

// Simulating API-1 call
const api1Call = () => {
  // Replace this with your actual API-1 call
  return of({ name: 'exampleName' });
};

// Simulating API-2 call
const api2Call = (name: string) => {
  // Replace this with your actual API-2 call using the name value
  return of({ tempData: `Temp data for ${name}` });
};

// Call API-1 and switch to API-2 with the obtained name value
api1Call().pipe(
  switchMap(resultFromAPI1 => {
    const nameValue = resultFromAPI1.name;
    return api2Call(nameValue);
  })
).subscribe(resultFromAPI2 => {
  console.log(resultFromAPI2.tempData);
});
```