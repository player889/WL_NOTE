# Javascript Refactoring

1. 小心使用for ... in

   - 不保證引索
   - 迴圈中修改陣列造成混亂

2. forEach

   1. ```js
      ['a','b','c'].forEach((item,index)=>{
          console.log(index+":"+item);
      })
      ```

   2. ```js
      [2,3,4].map(item=>item +2);
      ```

   3. foreach with map，【value在前】、【key在後】

3. Set vs Array

   - Set的優勢就是value不能是重覆的，所以可以減少記憶體存取重覆值的浪費。尋找跟新增/刪除元素平均的時間複雜度都比Array好。
   - Array 的優勢因為資料是連續的，所以存取非常快速 O(1)

4. Arrow Function

   - ```js
      someFunction(someArg=>({something:"somevalue"}))  //return object
      ```

5. Dynamic Declear Object

   ```js
   song = {
     ['first' + 'Song'] :{},
     ['second' + 'Song'] :{}
   }
   ```

6. 建構函示 vs 工廠函示

   ```js
   //建構函示
   let temp = function(){
     this.msg= '12313';
   }
   console.log(new temp().msg);
   ```

   ```js
   //工廠函示
   let temp2 = (function(){
     return {
       msg2 : 'hello world'
     }
   })();
   ```

   ```js
   //揭示模組模式 - revealing module pattern
   var t = (function(){
     const msg = 'hello';
     const fn = function(){
       return msg +  "world";
     }
     return {
       msg,fn
     }
   })();
   
   const s = Object.create(t);
   console.log(s.msg);
   console.log(s.fn());
   ```

7. 物件導向

   ```js
   //模板方式
   class Person{
     log(number){
       console.log(this.whatIs(number));
     };
   };
   class Binaryknower extends Person{
     whatIs(number){ return Number('0b'+number) };
   }
   class BinaryOblivious extends Person{
     whatIs(number){ return number };
   }
   const personOne = new BinaryKnower();
   const personTwo = new BinaryOblivious();
   [personOne,personTwo].forEach(person=>person.log(10));
   ```

   ```js
   //策略模式
   class Person{
     constructor(knowBinary){
       this.knowBinary = knowBinary;
     }
     log(number){ console.log(this.whatIs(number))};
     whatIs(number){
       if(this.knowBinary){
         return Number('0b'+ number);
       }else{
         return number;
       }
     }
   }
   
   const personOne = new  Person(true);
   const personTwo = new  Person(false);
   [personOne,personTwo].forEach(person=>{person.log(10)});
   
   //====================
   class Person{
     constructor(whatIs){
       this.whatIs = whatIs;
     }
     log(number){ console.log(this.whatIs(number))};
   }
   
   const binary ={
     aware(number){return Number('0b'+number)},
     oblivious(number){return number}
   }
   
   const personOne = new  Person(binary.aware);
   const personTwo = new  Person(binary.oblivious);
   [personOne,personTwo].forEach(person=>{person.log(10)});
   ```

   ```js
   //狀態模式
   ...
   ```

   ```js
   //外觀模式
   ...
   ```

8. pramid of doom ==> async/await

   - promise vs callBack

     ```js
     function addOne(addEnd){
       return Promise.resolve(addEnd+1);
     }
     function four(){
       return new Promise((resolve,_reject)=>{
         setTimeout(()=>resolve(4),500);
       });
     };
     
     four().then(addOne).then(console.log);
     ```

   - ```js
     const foo = async () => {
         return 1;
     }
     
     foo().then((res) => {
         console.log(res);
     });
     ```
   
   - ```js
     const logAsync = (message, time) => {
         return new Promise((resolve, reject) => {
             if (message && time) {
                 setTimeout(() => {
                     console.log(message);
                     resolve()
                 }, time);
             } else {
                 reject();
             }
         });
     };
     
     const demo = async () => {
         await logAsync('1 秒後會出現這句', 1000);
         await logAsync('再 1.5 秒後會出現這句', 1500);
         await logAsync('再 2 秒後會出現這句', 2000);
     };
     
     console.log("!!!");
     
     demo();
     ```
   
   - ```js
     (async () => {
       const res = await fetch('API_URL');
       const data = await res.text();
       
       console.log(data);
     })();
     ```
   
   - ```js
     ((async () => {
         const { data } = await axios.get('API_URL');
         
         console.log(data);
     })();
     ```

