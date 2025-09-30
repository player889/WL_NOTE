## Preface

```js
var obj = { name: 'sven'};
var A = function(){};
	A.prototype = obj;
var a = new A();
console.log(a.name); //sven
```

### Function.prototype.call or Function.prototype.apply

Change **this** variable inside the function

```js
var obj = {
    name : 'jay',
    getName : function(){
        return this.name;
    }
};

var obj2 = {
    name : 'John'
};

console.log(obj.getName()); //jay
console.log(obj.getName.call(obj2)); //john
```

### call vs apply

- call

  ```js
  var func = function(a,b,c){
      console.log([a,b,c]); //[1,2,3]
  }
  func.call(null,1,2,3);
  ```

- apply

  ```js
  var func = function(a,b,c){
      console.log([a,b,c]); //[1,2,3]
  }
  func.apply(null,[1,2,3]);
  ```

- Recommend using **apply**

### Missing this

```js
var obj = {
    name : 'jay',
    getName : function(){
        return this.name;
    }
};
console.log(obj.getName()); //jay

vargetName2 = obj.getName;
console.log(getName2); //undefined ==> **(this refer to golbal window obj)
```

```js
document.getElementById = (function(func){
    return function(){
        return func.apply(document, arguments);
    }
})(document.getElementId);

var getId = document.getElementById;
var div = getId('div1');
console.log(div.id); //div1
```

- Before

  ```js
  document.getElementById('div1').onclick = function(){
      console.log(this.id); //div1
      var func = function(){
          console.log(this.id); //undefined *(golbal windows)
      }
  }
  ```

- After

  ```js
  document.getElementById('div1').onclick = function(){
      var func = function(){
          console.log(this.id); //div1
      }
      func.call(this);
  }
  ```

### High Order Function

1. Closure

2. AOP - before, after, around

   ```js
   Function.prototype.before = function(beforeFn){
       var _self = this;
       return function(){
           beforeFn.apply(this,arguments);
           return _self.apply(this,arguments);
       }
   };
   
   Function.prototype.after = function(afterFn){
       var _self = this;
       return function(){
           var ret = _self.apply(this,arguments);
           afterFn.apply(this,arguments);
           return ret;
       }
   }
   
   var func = function(){
       console.log('2');
   }
   
   func = func.before(function(){
      console.log('1');
   }).after(function(){
       console.log('3');
   })
   
   func();
   //1
   //2
   //3
   ```

   ```js
   Function.prototype.around = function(beforeFun, afterFun) {
   	var _orgin = this;
   	return function() {
   		return _orgin.before(beforeFun).after(afterFun).apply(this, arguments);
   	}
   }
   ```

3. Lazy  Function

   ```js
   var addEvent = function(elem,type,handler){
       if(window.addEventListener){
           addEvent = function(elem,type,handler){
               elem.addEventListener(type,handler,fals);
           }
       }else if(window.attachEvent){
           addEvent = function(elem,type,handler){
   			elem.attachEvent(type,handler,fals);
           }
       }
       addEvent(elem,type,handler);
   };
   
   var div = document.getElementById('div1');
   
   addEvent(div, 'click', function(){
   	console.log(1);
   });
   
   addEvent(div, 'click', function(){
   	console.log(2);
   });
   ```

## 單例模式

```js
class MyClass {
  constructor() {
    if (MyClass._instance) {
      throw new Error("Singleton classes can't be instantiated more than once.")
    }
    MyClass._instance = this;

    // ... Your rest of the constructor code goes after this
  }
}

var instanceOne = new MyClass() // Executes succesfully
var instanceTwo = new MyClass() // Throws error
```

```js
class MyClass {
  constructor() {
    if (MyClass._instance) {
      return MyClass._instance
    }
    MyClass._instance = this;

    // ... Your rest of the constructor code goes after this
  }
}

var instanceOne = new MyClass()
var instanceTwo = new MyClass()

console.log(instanceOne === instanceTwo) // Logs "true"
```

```js
var mySingleton = (function () {

  // Instance stores a reference to the singleton
  var instance;
  function init() {
    // Singleton
    // Private methods and variables
    function privateMethod(){
        console.log( "I am private" );
    }
    var privateVariable = "I'm also private";
    var privateRandomNumber = Math.random();
    return {
      // Public methods and variables
      publicMethod: function () {
        console.log( "The public can see me!" );
      },
      publicProperty: "I am also public",
      getRandomNumber: function() {
        return privateRandomNumber;
      }
    };
  };

  return {
    // Get the singleton instance if one exists
    // or create one if it doesn't
    getInstance: function () {
      if ( !instance ) {
        instance = init();
      }
      return instance;
    }
  };
})();
```

## 策略模式

```js
var strategies = {
    "s": function(salary){
        return salary * 4
    },
    "a": function(salary){
		return salary * 3
    },
    "b": function(salary){
		return salary * 2
    }
};

var calculateBonus = function(level, salary){
    return strategies[level](salary);
};
console.log(calculateBonus('s','9999'));
```

Form 驗證功能

```html
<html>
<body>
	<form action="http:// xxx.com/register" id="registerForm" method="post">
		请输入用户名：<input type="text" name="userName"/ >
		请输入密码：<input type="text" name="password"/ >
		请输入手机号码：<input type="text" name="phoneNumber"/ >
		<button>提交</button>
	</form>
	<script>
		/***********************策略对象**************************/
		var strategies = {
			isNonEmpty: function( value, errorMsg ){
				if ( value === '' ){
					return errorMsg;
				}
			},
			minLength: function( value, length, errorMsg ){
				if ( value.length < length ){
					return errorMsg;
				}
			},
			isMobile: function( value, errorMsg ){
				if ( !/(^1[3|5|8][0-9]{9}$)/.test( value ) ){
					return errorMsg;
				}
			}
		};
		/***********************Validator 类**************************/
		var Validator = function(){
			this.cache = [];
		};
		Validator.prototype.add = function( dom, rules ){
			var self = this;
			for ( var i = 0, rule; rule = rules[ i++ ]; ){
				(function( rule ){
					var strategyAry = rule.strategy.split( ':' );
					var errorMsg = rule.errorMsg;
					self.cache.push(function(){
						var strategy = strategyAry.shift();
						strategyAry.unshift( dom.value );
						strategyAry.push( errorMsg );
						return strategies[ strategy ].apply( dom, strategyAry );
					});
				})( rule )
			}
		};
		Validator.prototype.start = function(){
			for ( var i = 0, validatorFunc; validatorFunc = this.cache[ i++ ]; ){
				var errorMsg = validatorFunc();
				if ( errorMsg ){
					return errorMsg;
				}
			}
		};
		/***********************客户调用代码**************************/
		var registerForm = document.getElementById( 'registerForm' );
		var validataFunc = function(){
			var validator = new Validator();
			validator.add( registerForm.userName, [{
				strategy: 'isNonEmpty',
				errorMsg: '用户名不能为空'
			}, {
				strategy: 'minLength:6',
				errorMsg: '用户名长度不能小于10 位'
			}]);
			validator.add( registerForm.password, [{
				strategy: 'minLength:6',
				errorMsg: '密码长度不能小于6 位'
			}]);
			var errorMsg = validator.start();
			return errorMsg;
		}
		registerForm.onsubmit = function(){
			var errorMsg = validataFunc();
			if ( errorMsg ){
				alert ( errorMsg );
				return false;
			}

		};
	</script>
</body>
</html>
```

## 代理模式

- 預設圖片

## 疊代器模式

## 發佈訂閱模式

- Ajax Success Callback Function

  ```js
  /*兔头店*/        
  var shop={
      listenList:[],//缓存列表
      addlisten:function(fn){//增加订阅者
          this.listenList.push(fn);
      },
      trigger:function(){//发布消息
          for(var i=0,fn;fn=this.listenList[i++];){
              fn.apply(this,arguments);
          }
      }
  }
  
  /*小明订阅了商店*/
  shop.addlisten(function(taste){
      console.log("通知小明，"+taste+"味道的好了");
  });
  /*小龙订阅了商店*/
  shop.addlisten(function(taste){
      console.log("通知小龙，"+taste+"味道的好了");
  });
  /*小红订阅了商店*/
  shop.addlisten(function(taste){
      console.log("通知小红，"+taste+"味道的好了");
  });    
  
  // 发布订阅
  shop.trigger("中辣");
  
  ```

  