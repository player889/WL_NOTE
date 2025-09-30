```ad-note
title:app.module.ts
collapse: none

 - declarations - 加載根組件
 - provides - 程式共享對象
 - bootstrap - 根組件是型態(類)


```

```js
fromEvent(document.querySelector('input'), 'input').pipe(
  debounceTime(300),
  map(event => (event.target as HTMLInputElement).value),
  switchMap(query => getData(query).pipe(
    switchAll(),
    map(({firstName, lastName}) => firstName + ' ' + lastName),
    toArray(),
  )),
).subscribe(console.log);
```

![[Pasted image 20230726175142.png]]

# InvalidPipeArgument: '[object Object],[object Object]' for pipe 'AsyncPipe'

```javascript
this.studentservice.getStudentList().subscribe((data) => {
	this.students = of(data); // please do import {of} from 'rxjs';
});
```
