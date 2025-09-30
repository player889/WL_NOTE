# Javascript

## Numbers

### Number only

```html
<input type="text" onkeypress='return event.charCode >= 48 && event.charCode <= 57'/>
```

```java
onkeyup="this.value=this.value.replace(/[^\d]/g,'')"
```

```javascript
onkeypress='return isNumberKey(event)'
function isNumberKey(evt){
    var charCode = (evt.which) ? evt.which : event.keyCode
    if (charCode > 31 && (charCode < 48 || charCode > 57))
        return false;
    return true;
}
```

### Thousands Separator

```javascript
function number_format(n) {
    n += "";
    var arr = n.split(".");
    var re = /(\d{1,3})(?=(\d{3})+$)/g;
    return arr[0].replace(re,"$1,") + (arr.length == 2 ? "."+arr[1] : "");
}
```

```javascript
function numberWithCommas(x) {
    return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}
```

### Fix 2  thousands Separator

```javascript
$(this).keyup(function(event) {
    if (event.which >= 37 && event.which <= 40) {
        event.preventDefault();
    }
    var currentVal = $(this).val();
    if ((currentVal.match(/\./g) || 0).length > 1) {
        currentVal = currentVal.slice(0, -1);
    }
    let temp = currentVal.split(".");
    temp[0] = temp[0].replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    if (temp[0].startsWith("0") && temp[0].length > 1) {
        temp[0] = 0;
    }
    if (temp.length == 2 && temp[1].length > 2) {
        temp[1] = temp[1].substring(0, 2).replace(/\D/g, "");
    }
    $(this).val(temp.join("."));
}).blur(function() {
    let value = $(this).val();
    if('' == value){
        $(this).val("0.00");
    }else {
        let temp = value.toString().split(".");
        if (temp.length == 1) {
            $(this).val(temp[0] + '.00');
        }else if(temp.length == 2 && temp[0] == ''){
            $(this).val("0."+temp[1]);
        }
    }
});
```

## Regular Expression

### English or Number /w Length

```javascript
if(!(/^[A-Za-z][A-Za-z0-9]*$/.test(custNo)) && (5 > custNo.length || 11 < custNo.length)){
	validator.define('CUST_NO', '5碼以上10碼以下英文或數字', '帳號', 'false' );
}
if('' != policyNo &&　(!/^[A-Z0-9]{14}$/.test(policyNo))){
	validator.define('POLICY_NO','保單號碼','限輸入14位英文數字','false');	
}
```

### No Chinese inputs

```javascript
//validator　-　中文
var a = "ABC你";
if (!(/^[^\u4E00-\u9FA5\uF900-\uFA2D]+$/.test(a))) { 
	alert("被保人護照英文姓名不能輸入中文");
}else{
	alert("pass");
}
```

```html
<input type="text" style="ime-mode:disabled" />
```

### Cell Phone Validator

```javascript
//手機驗證
function checkPhone(strPhone){
	return /^09[0-9]{8}$/.test(strPhone);
}
```

### Chinese(Simple) validator

```javascript
var str = '3AAA1AA转码飛機';
var reg = /[\u4e00-\u9fa5]/gi;
var found = str.match(reg);
console.log(found);// ["转", "码", "飛", "機"]
```

### English validator

```javascript
var t2 = '12A313C';
var re2 = /[a-zA-Z]+/gi;
var f = t2.match(re2);
console.log(f);//["A", "C"]
```

```javascript
// will replace 'userName[0]' with userName[{i}]
replace(/\[\d*\]$/, "["+ i +"]")
//AAA-0000
^[a-z]{3}\-[0-9]{4}$
//非負數字
"^\d+[.]?\d*$"
//數字
"^\d+(\.)?\d*$"
//非負整數
"^\d+$"
//整數
"^-?\d+$"
//負整數
"^-[0-9]*[1-9][0-9]*$"
//正整數
"^[0-9]*[1-9][0-9]*$"
//非正整數
"^((-\d+)|(0+))$"
```

### Find String between charactors

```js
let url = "https://app.powerbi.com/groups/e3e17c19-c128-477f-b319-09c26b87fe61/reports/b95c7076-7b74-4976-8ef9-48b378918ed3?ctid=d6949649-50b9-42f7-b888-36f696121dd7";

let reg = /(?<=\/groups\/).*?(?=\/reports\/)/;
let b = url.match(reg);
console.log(b);
//["e3e17c19-c128-477f-b319-09c26b87fe61"]

//=================================================================
String.prototype.extract = function(prefix, suffix) {
	s = this;
	var i = s.indexOf(prefix);
	if (i >= 0) {
		s = s.substring(i + prefix.length);
	}
	else {
		return '';
	}
	if (suffix) {
		i = s.indexOf(suffix);
		if (i >= 0) {
			s = s.substring(0, i);
		}
		else {
		  return '';
		}
	}
	return s;
};
```



## ShortHand

### Object Json

```javascript
const x = 1920, y = 1080;
const obj = { x:x, y:y };(X)
const obj = { x, y };(o)
```

### ParseInt & parseFloat

```javascript
const num1 = parseInt("100");
const num2 =  parseFloat("100.01");
const num1 = +"100"; // converts to int data type
const num2 =  +"100.01"; // converts to float data type
```

### IndexOf

```javascript
if(arr.indexOf(item) > -1) {} // Confirm item IS found
if(arr.indexOf(item) === -1) {}// Confirm item IS NOT found
if(~arr.indexOf(item)) {} // Confirm item IS found
if(!~arr.indexOf(item)) {} // Confirm item IS NOT found
```

## Array

### Split Array into group of Array

```javascript
function groupArr(data, n) {
    var group = [];
    for (var i = 0, j = 0; i < data.length; i++) {
        if (i >= n && i % n === 0)
            j++;
        group[j] = group[j] || [];
        group[j].push(data[i])
    }
    return group;
}
groupArr($.map(e.params.data.items,function(item){return'<td>'+item+'</td>'}),3)

const nameList = Object.values(list).map(item => item.name);
```

## Sort array expect specific item

[Reference](https://stackoverflow.com/questions/51557177/sort-an-array-except-one-element-in-javascript)

```js
data
          .sort(function(a, b) {
            var aa = +((a[sortName] + '').replace(/[^\d]/g, ''));
            var bb = +((b[sortName] + '').replace(/[^\d]/g, ''));

            if(a.loadType == b.loadType == 'FOLDER') return 0;
            if (a.loadType == 'FOLDER') return -1;
            if (b.loadType == 'FOLDER') return 1;

            if (aa < bb) {
              return order * -1;
            }
            if (aa > bb) {
              return order;
            }
            return 0;
          });
```

## Prototype

### Get key from value

```javascript
Object.prototype.getKeyByValue = function( value ) {
	for( var prop in this ) {
		if( this.hasOwnProperty( prop ) )
			 if( this[ prop ] === value )
				 return prop;
	}
}
var test = { key1: 42, key2: 'foo' };
test.getKeyByValue( 42 );  // returns 'key1'
```

## DOM

### ResetForm

```javascript
document.getElementById("reset").addEventListener('click', function(e){
    var inputList = document.getElementsByTagName('input');
    for (var i = 0; i < inputList.length; i++) { 
        if( inputList[i].type == 'text' ){
            inputList[i].value = '';
        }
    };
});
```

### Pop-up new Window

```javascript
function myFunction() { //pop up new window
    urls = ["http://www.google.com.tw"];
    var url = urls[Math.floor(Math.random() * urls.length)];
    params = 'width=' + screen.width;
    params += ', height=' + screen.height;
    params += ', top=0, left=0,scrollbars=yes';
    params += ', fullscreen=yes';
    pop = window.open(url, 'window', params).blur();
    window.focus();
}
```

### Inser element to tr

```javascript
tr.insert(new Element('td').insert(inputElement('EFCTVE_DATE_'+index, vo.EFCTVE_DATE)).insert(dateImg('EFCTVE_DATE_'+index)));
function inputElement(id, value){
	var obj = new Element('input', {className:'textBox2', type:'text', value:value, size:'11', id: id, name:id});
		obj.observe('change', function(event){
			var element = event.element();
			$(id).value = DateUtils.toY2K(element.value);
		});
	return obj;
}
function dateImg(id){
	return '<img src="<%=request.getContextPath()%>/images/CM/calendar.gif" width="20" height="20" onclick="getCalendarFor($(\''+id+'\'))"';
}
```

### Default value

```javascript
HTML<select onfocus="defaultValue=this.value" onchange="this.value=defaultValue">
```

```javascript
$("#accountId").on('focus',function(){
    $(this).prop('defaultIndex',$(this).prop('selectedIndex'));    
});
$("#accountId").on('change',function(){
    $(this).prop('selectedIndex',$(this).prop('defaultIndex'));    
});
```

### Form

```javascript
var someForm = $('#Modal1form');
$.each(someForm[0].elements, function(index, elem){console.log(elem); });
```

### Datepicker

```javascript
//get DatePiker date
$('#startRemitDate').datepicker({ format : 'yyyy/mm/dd', }).on('changeDate', function(ev){ $(this).datepicker('hide'); console.log($(this).val()); });
```

```javascript
//Set startDate with temp.
var temp = new Date();
$('#endRemitDate').datepicker({ format : 'yyyy/mm/dd', startDate : 'temp' }); 	
```

```javascript
//datepicker without date only year and month
$('#datepicker').datepicker({dateFormate:'yymm', changeMonth: true, changeYear: true,
	onClose: function(dateText, inst){
		var month = $('#ui-datepicker-div .ui-datepicker-month :seleccted').val();
		var year = $('#ui-datepicker-div .ui-datepicker-year :seleccted').val();
		var selected  = $.datepicker.formateDate('yymm', new Date(year, month, 1));
	},beforeShow: function(input, inst){
		$(this).datepicker('option', 'defaultDate', new Date(year, momth, 0));
		$(this).datepicker('setDate', new Date(year, month, 0 )));
	};
}).focus(function(){
	$('.ui-datepicker-calender').hide();
})
```

```javascript
//date range
$(".from_date").datepicker({
	format: 'yyyy-mm-dd',
	autoclose: true,
}).on('changeDate', function (selected) {
	var startDate = new Date(selected.date.valueOf());
	$('.to_date').datepicker('setStartDate', startDate);
}).on('clearDate', function (selected) {
	$('.to_date').datepicker('setStartDate', null);
});

$(".to_date").datepicker({
	format: 'yyyy-mm-dd',
	autoclose: true,
}).on('changeDate', function (selected) {
	var endDate = new Date(selected.date.valueOf());
	$('.from_date').datepicker('setEndDate', endDate);
}).on('clearDate', function (selected) {
	$('.from_date').datepicker('setEndDate', null);
});
```

```javascript
$("#endDatePicker").datepicker({
    minDate: '+1m',
    beforeShow: function() {
        //get date startDate is set to
        var startDate = $("#startDatePicker").datepicker('getDate');
        //if a date was selected else do nothing
        if (startDate != null) {
            startDate.setMonth(startDate.getMonth()+1);
            $(this).datepicker('option', 'minDate',startDate);
        }
    }
});
```

### Get Dom From HTML String

```javascript
let a= "<span style='padding-left:0px;'><i class='fas fa-folder'></i> 2019IQ0001-飛出太陽系計畫</span>";
let parser = new DOMParser();
let parsedHtml = parser.parseFromString(a, 'text/html');
console.log(parsedHtml.getElementsByTagName('span')[0].innerText);
```

### DOM Table

| Type            | Description                                                  | Return  |
| --------------- | :----------------------------------------------------------- | ------- |
| firstChild      | 傳回第一個子節點childNodes物件集合，包含此節點下所有的子節點 | Node    |
| lastChild       | 傳回最後一個子節點的childNodes物件集合，包含此節點下所有的子節點 | Node    |
| parentNode      | 傳回父節點的物件，如已是根節點，傳回null                     | Node    |
| nextSibling     | 傳回下一個兄弟節點物件，如已是最後一個節點，傳回null         | Node    |
| previousSibling | 傳回上一個兄弟節點物件，如已是第一個節點，傳回null           | Node    |
| nodeName        | 傳回節點的HTML標籤(英文大寫)名稱                             | String  |
| nodeType        | 傳回節點種類，1是標籤(element node)、2是屬性(attribute node)、3為文字(text node) | Number  |
| specified       | 布林值，傳回在HTML標籤是否有設定指定的屬性值                 | Boolean |
| nodeValue       | 存取文字節點(text node)，其他種類的節點傳回null              |         |
| attributes      | 節點屬性的物件集合，可以直接使用名稱來存取，僅用於元素節點   |         |
| childNodes      | 所有子節點的物件集合(Array)，方法item(i)可訪問第i+1個節點<br />在obj(父)節點新增子節點obj(子)，傳回新增的節點物件 |         |
| appendChild     | obj(父).appendChild(obj(子))<br />複製objDup節點的新節點物件，deep為false僅複製此節點，true連子節點的整個節點樹都複製 |         |
| cloneNode       | objNew = objDup.cloneNode(deep)                              |         |
| createElement   | 建立一個HTML節點物件<br />objNew = document.createElement("tagName") |         |
| createTextNode  | 建立文字節點<br />objNew = document.createTextNode(string)   |         |
| hasChildNodes   | 檢查節點是否擁有子節點，有true<br />objNode.hasChildNodes()  |         |
| insertBefore    | 在obj(父)節點前插入一個子節點obj(子)，位置在objBrother節點前<br />obj(父).insertBefore(obj(子),objBrother) |         |
| removeChild     | 先找到要刪除的節點，透過parentNode的removeChild()方法來刪除目標節點<br />obj.parentNode.removeChild(targetNode) |         |
| replaceChild    | 先找到要替換的節點，透過parentNode的replaceChild()方法來替換節點<br />obj.parentNode.replaceChild(newNode,oldNode) |         |
| getAttribute    | 讀取obj的attr屬性值，例如，obj.getAttribute("title");<br />obj.getAttribute("attr") |         |
| setAttribute    | 設定obj的attr屬性的值為value，例obj.setAttribute("src","bruce.png);obj.setAttribute("attr","value") |         |
|                 | Node.ELEMENT_NODE == 1<br/>Node.ATTRIBUTE_NODE == 2<br/>Node.TEXT_NODE == 3<br/>Node.CDATA_SECTION_NODE == 4<br/>Node.ENTITY_REFERENCE_NODE == 5<br/>Node.ENTITY_NODE == 6<br/>Node.PROCESSING_INSTRUCTION_NODE == 7<br/>Node.COMMENT_NODE == 8<br/>Node.DOCUMENT_NODE == 9<br/>Node.DOCUMENT_TYPE_NODE == 10<br/>Node.DOCUMENT_FRAGMENT_NODE == 11<br/>Node.NOTATION_NODE == 12 |         |

### ES6 - Loop ul li

```javascript
$('<ul>', {class: "list-group"}).append(data.map(m =>$("<li>", {class:"list-group-item list-group-item-danger"}).append(m);
$('#messageText').after($ul);
```



## Arguement

```javascript
function t(){ console.log(t.arguments /*arguments*/);}
//{0:"a",1:"b"}
```

## Validate

### set default value

```javascript
Object.is(b.a, undefined) ? 'defaultValue' : b.t
```

### Check values

![[UTBcN.png]]

## Clourse

```javascript
// 這個代碼是錯誤的，因為變量i從來就沒背locked住。相反，當循環執行以後，我們在點擊的時候i才獲得數值，因為這個時候i操真正獲得值。所以說無論點擊那個連接，最終顯示的都是I am link #10（如果有10個a元素的話）
var elems = document.getElementsByTagName('a');
for (var i = 0; i < elems.length; i++) {
	elems[i].addEventListener('click', function (e) {
		e.preventDefault();
		alert('I am link #' + i);
	}, 'false');
}

// 這個是可以用的，因為他在自執行函數表達式閉包內部
// i的值作為locked的索引存在，在循環執行結束以後，盡管最後i的值變成了a元素總數（例如10）
// 但閉包內部的lockedInIndex值是沒有改變，因為他已經執行完畢了
// 所以當點擊連接的時候，結果是正確的
var elems = document.getElementsByTagName('a');
for (var i = 0; i < elems.length; i++) {
	(function (lockedInIndex) {
		elems[i].addEventListener('click', function (e) {
			e.preventDefault();
			alert('I am link #' + lockedInIndex);
		}, 'false');
	})(i);
}
```

## Map

```javascript
[1, 2, 3].map(function (element) { return element * 2; }); // [2, 4, 6]
[1, 2, 3].map(function (element) { return '<td>'+element * 2+'</td>' });
```

## CallBack

```javascript
// http://www.jstips.co/zh_cn/passing-arguments-to-callback-functions/
var alertText = function(text) { alert(text); };
document.getElementById('someelem').addEventListener('click', alertText.bind(this, 'hello'));

function callback(a, b) { return function() { console.log('sum = ', (a+b)); } }
document.getElementById('someelem').addEventListener('click', callback(1, 2));
```

## HTML Mask

```html
<!--只能輸入漢字-->
<input onkeyup = "value=value.replace(/[^\u4E00-\u9FA5]/g,'')" onbeforepaste = "clipboardData.setData('text',clipboardData.getData('text').replace(/[^\u4E00-\u9FA5]/g,''))">
<!--只能輸入數字的-->
<input onkeyup = "value=value.replace(/[^\d]/g,'') "onbeforepaste = "clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))" >
<!--簡易禁止輸入漢字-->
<input type = "text" style = "ime-mode:disabled" >
<!--輸入數字和小數點-->
<onkeyup = "value=value.replace(/[^\d{1,}\.\d{1,}|\d{1,}]/g,'')">
<!--只能輸入數字和 ":"-->
<input type = text id = "aa1" onkeyup = "this.value=this.value.replace(/[^\d&:]/g,'')" onblur = "this.value=this.value.replace(/[^\d&:]/g,'')" onafterpaste = "this.value=this.value.replace(/[^\d&:]/g,'')" />
<!--只能輸入字母和等號， 不能輸入漢字-->
<input type = text id = "aa" onkeyup = "value=value.replace(/[^\w&=]|_/ig,'')" onblur = "value=value.replace(/[^\w&=]|_/ig,'')" />
<!--只能輸入數字-->
<input onkeyup = "this.value=this.value.replace(/\D/g,'')" onafterpaste = "this.value=this.value.replace(/\D/g,'')" >
 <!--只能輸入數字-->  
<input name = txt1 onchange = "if(/\D/.test(this.value)){alert('只能輸入數字');this.value='';}" >
<input onkeyup = "if(isNaN(value))execCommand('undo')" onafterpaste = "if(isNaN(value))execCommand('undo')" >
```

## Template

```javascript
const Item = ({ url, img, title }) => `
  <a href="${url}" class="list-group-item">
    <div class="image">
      <img src="${img}" />
    </div>
    <p class="list-group-item-text">${title}</p>
  </a>
`;

$('.list-items').html([
  { url: '/foo', img: 'foo.png', title: 'Foo item' },
  { url: '/bar', img: 'bar.png', title: 'Bar item' },
].map(Item).join(''));
```

## Get String inside the parentheses

```javascript
var regExp = /\(([^)]+)\)/;
var matches = regExp.exec("I expect five hundred dollars ($500).");

//matches[1] contains the value between the parentheses
console.log(matches[1]);
```



# Bootstrap V.4

## Center a DIV horizontally and vertically

```css
.login-center {
 	height: 100vh;
}
```

> `.align-items-center` Vertical centered
> `.justify-content-center` Horizontally centered

```html
<div class="row align-items-center justify-content-center login-center">
	---
</div>
```

### more Example

```html
<body class="h-100">
  <div class="container h-100">
  <div class="row justify-content-center h-100">
    <div class="my-auto">
      <input type="text" placeholder="Example input">
      <span class="input-group-btn">
        <button class="btn btn-primary">Download</button>
      </span>
    </div>
  </div>
</div>
</body>
```

------

## `<hr>` In Bootstrap

```html
<div class="row"> 
	<div class="col-md-12 col-lg-12">
		<hr style="min-width:85%; background-color:#a1a1a1 !important; height:1px;"/>
	</div>
</div>
```

## Jumbotron center

```html
	<div class="container-fluid">
		<div class="row">
			<div class="col-12 p-0">
				<!-- .bg-info -->
				<div class="jumbotron min-vh-100 text-center m-0 d-flex flex-column justify-content-center">
					<h1 class="display-4">Opportunities</h1>
					<p class="lead">Web Application</p>
					<p class="lead">
						<a class="btn btn-primary" href="#" role="button" th:href="@{/project}">Create Project</a>
					</p>
				</div>
			</div>
		</div>
	</div>
```

## DatePicker HTML

```html
<div class="input-daterange input-group" id="datepicker">
    <input type="text" class="input-sm form-control" name="start" />
    <div class="input-group-prepend input-group-append">
        <span class="input-group-text">to</span>
    </div>
    <input type="text" class="input-sm form-control" name="end" />
</div>
```

## Modal with DatePicker -  close problem with modal 

```javascript
$('.input-daterange').datepicker({
    format: "yyyy/mm/dd",
    todayBtn: "linked"
}).on('changeDate', function(ev) {
    // YOUR CODE
}).on('hide', function(event) {
    event.preventDefault();
    event.stopPropagation();
});
```

## Bootstrap4 with FontAwsome 5 

```html
<div class="form-group col-md-3">
    <label for="account">委託日期</label>
    <div class="input-group ">
        <input type="text" class="form-control datepicker" id="commissionDate" name="commissionDate" required style="text-align: center;" />
        <div class="input-group-append">
            <span class="input-group-text" id="basic-addon2">
                <span class="input-group-addon">
                    <i class="fa fa-envelope"></i>
                </span>
            </span>
        </div>
    </div>
</div>
```

## RowSpan on Bootstrap

```html
<div class="form-row">
    <div class="form-group col-md-6">
        <div class="form-group required">
            <label for="billId" class="text-dark font-weight-bold">請款對象</label>
            <select class="form-control select2Ajax" name="billId" id="billId" required="required"></select>
        </div>
        <div class="form-group required">
            <label for="billId" class="text-dark font-weight-bold">聯絡人</label>
            <select class="form-control select2Ajax" name="contactId" id="contactId" required="required"></select>
        </div>
    </div>
    <div class="form-group col-md-6">
        <textarea class="form-control" id="memo" rows="6" style="resize: none; overflow-y: scroll;" readonly="readonly" />
    </div>
</div>
```

![[Snipaste_2019-11-08_10-47-23.png]]

## Bootstrap validator

![[Snipaste_2019-12-06_23-12-21.png]]
![[Snipaste_2019-12-06_23-11-40.png]]
# OTHER

## form to json

```js
$.fn.getForm2obj = function () {
  return JSON.stringify($(this).serializeArray().reduce((o, {
    name: n,
    value: v
  }) => Object.assign(o, {[n]: v}), {}));
};
```

## pair array

```js
const pairArray = (arr, size = 2) => {
  return arr.map((x, i) => i % size == 0 && arr.slice(i, i + size)).
      filter(x => x);
};
```

---

##### Number Only

- Option 1:

```html
<input type="text" onkeypress='return event.charCode >= 48 && event.charCode <= 57'/>
```

- Option 2:

```html
<input type="text" onkeyup="this.value=this.value.replace(/[^\d]/g,'')" />
```

- Option 3:

```html
<input type="text" onkeypress='return isNumberKey(event)' />
```

```js
function isNumberKey(evt){
    var charCode = (evt.which) ? evt.which : event.keyCode
    if (charCode > 31 && (charCode < 48 || charCode > 57))
        return false;
    return true;
}
```

##### Thousands Separator

- Option 1:

  ```js
  function number_format(n) {
      n += "";
      var arr = n.split(".");
      var re = /(\d{1,3})(?=(\d{3})+$)/g;
      return arr[0].replace(re,"$1,") + (arr.length == 2 ? "."+arr[1] : "");
  }
  ```

- Option 2:

  ```js
  function numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
  }
  ```

###### Fix 2

```js
$(this).keyup(function(event) {
    if (event.which >= 37 && event.which <= 40) {
        event.preventDefault();
    }
    var currentVal = $(this).val();
    if ((currentVal.match(/\./g) || 0).length > 1) {
        currentVal = currentVal.slice(0, -1);
    }
    let temp = currentVal.split(".");
    temp[0] = temp[0].replace(/\D/g, "").replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    if (temp[0].startsWith("0") && temp[0].length > 1) {
        temp[0] = 0;
    }
    if (temp.length == 2 && temp[1].length > 2) {
        temp[1] = temp[1].substring(0, 2).replace(/\D/g, "");
    }
    $(this).val(temp.join("."));
}).blur(function() {
    let value = $(this).val();
    if('' == value){
        $(this).val("0.00");
    }else {
        let temp = value.toString().split(".");
        if (temp.length == 1) {
            $(this).val(temp[0] + '.00');
        }else if(temp.length == 2 && temp[0] == ''){
            $(this).val("0."+temp[1]);
        }
    }
});
```

# Other

```javascript
//make limit input from user
function isCharKey(evt){ var charCode = (evt.which) ? evt.which : event.keyCode return !(charCode < 31 && (charCode > 48 || charCode < 57)); } 

document : all[i],all[name],.tags(tagname) 
// children [] // write() ,writeln() || element: createAttribute(), createEvent(), creatElement(NS)() ,getElementbyId ,getElementsByTagName(NS)() ,removeAttribute()(NS),

js with css : CSSStyleDeclaration.getPropertyCssValue() ,getPropertyCssValue() ,item() ,removeProperty() ,setPorperty() etc ... && CSSStyleSheet.deketeRule() , insertRule()

example: var divisions = document.body.getElementsByTageName("div")
var paragraphs = divisions[0].getElementsByTagName("p")

NamedNodeMap -- getNameItem()(NS) , removeNamedItem()(NS), setNamedItem()(NS),
NOde: appendChild(), cloneNode() , hasAttribute(), hasChildNodes(), insertBefore(), normalize(), removeChild(), replaceChild()
NodeLterator: detach(), nextNode(), previousNode(), item()

如果你调用多个选定元素的.html()方法，那么其读取的只是第一个元素，换句话说：如果选择器匹配多于一个的元素，那么只有第一个匹配元素的 HTML 内容会被获取。

var name = $('#item')[0].name; ==? var name = $('#item')[0]["name"];

If your requirements are simple, publically accessible, and being done by web developers, then use jQuery. If this is a more complex situation, you have more hard core engineers, behind a username/password, then I suggest ExtJS.

//check attr exist
if (typeof attr !== 'undefined' && attr !== false) { ... }

screen.availWidth //get user screen 

toDateString()

Array.slice(1,4);
ArrayONe.concat(arraytwo,arraythree);

document.write("Apple"<"Banana" );	true ASCII code 65 < 66
document.write("apple"<"Banana" );	false ASCII code 97 < 66

style始终先于javascript加载

当onload事件触发时，页面上所有的DOM，样式表，脚本，图片，flash都已经加载完成了。
当DOMContentLoaded事件触发时，仅当DOM加载完成，不包括样式表，图片，flash。
当然DOMContentLoaded机制更加合理，因为我们可以容忍图片，flash延迟加载，却不可以容忍看见内容后页面不可交互。

function(arg){...} 
这就定义了一个匿名函数，参数为arg而调用函数时，是在函数后面写上括号和实参的，由于操作符的优先级，函数本身也需要用括号，即： (function(arg){...})(param)这就相当于定义了一个参数为arg的匿名函数，并且将param作为参数来调用这个匿名函数而(function($){...})(jQuery)则是一样的，之所以只在形参使用$，是为了不与其他库冲突，所以实参用jQuery 
var fn = function($){....};  fn(jQuery); 

~(text == "text") // 1 or 0

the cusomer the tag use prefix data-XXX ( dataset.XXX )
var temp = $('#ABC').attr('data-XXX');
var temp = $('#ABC').data('XXX'); cut the prefix and left the suffix

this and $(this) are different . if this is array object. this represent to the first element of array.  and $(this) is refert the dom object

.bind()  ->> not inheritence
.live()  ->> this will inheritence the function 

animate() function will move object as flash

function different between preventDefault and stopPropagation (){
<div id="p">
    <a href="" id="a" target="_blank">link</a>
</div>

    就以 a.onclick 來說明好了，點了一個超連結，

    正常狀況下超連結會進行並開啟新頁，
    p 的 onclick 會被觸發（因為事件向上傳遞）。

    ---------------------------------

    如果在 a.onclick 裡面下 preventDefault() ，
    a 的超連結就不會打開頁面。

    如果在 a.onclick 裡面下 stopPropagation()，
    p.onclick 就不會觸發。
}

wrap() this the use the select element and return the DOM object and css DOM
for example : return '<div />' this will put div tag inside every DOM object which is seleccted by wrap

If you are going to declare a function and use it only once and straight away, the function operator syntax is more concise than the function declaration. It is ideal for things like single line JQuery event handlers that toggle some CSS class.

end $.each() use return false; in some case return false will cause error, 
if the case happens, use the object replace T or F. ex: var temp = true ,  ....  return temp;

$.isEmptyObject();

Object.defineProperty(person,"Adress",{XXX});
XXX is the contorl of the properties : Configurable , Enumerable , Writable , Value

function hasPrototypeProperty(object, name){ return !object.hasOwnProperty(name) && (name in object); }

在 Ruby on Rails 上也蠻多人會利用 RJS 來設計 server/client 間的互動，此時 dataType 就必須要指定為 'script'，這樣當 ajax 成功後，你的瀏覽器才會知道要把回傳的資料當 JavaScript 來執行。

JSG.ImageUploader.js //ajax 圖檔上傳 js

window.onload() 发生在页面载入完成时
$(document).ready() 发生在页面主体框架载入完成时(或许某个图片还没下载完)

$(function() { //createSelect function will get the value from parent ( var = options )
	var options = 5;
	var createSelect = function() {
		console.log("start");
		for (var j = 0; j < options; j++) {
			console.log(j);
		}
		console.log("end");
	};
	createSelect();
}); 

if(o.constructor === Boolean) || if(o.constructor === String);

.add();
.addBack(); adding the event to the selector ex: $('div').XXX.addBack().css(...);
{
.ajaxComplete();
.ajaxError();
.ajaxSend();
.ajaxStart();
.ajaxStop();
.ajaxSuccess();
}
.andSelf();
{
.animate();
:animated Selector
}

//The user has a lot of code in one function and wants to split it up
//They take that information and send it through the jQuery callback 
//function which then allows them to have split their code into better manageable pieces with which to work with.
{
	callbacks.add();
	callbacks.disable()
	callbacks.disabled()
	callbacks.empty()
	callbacks.fire()
	callbacks.fired()
	callbacks.fireWith()
	callbacks.has()
	callbacks.lock()
	callbacks.locked()
	callbacks.remove()
}

.stop() method is meant to be used only with animations, 
.clearQueue() can also be used to remove any function that has been added
 to a generic jQuery queue with the .queue() method.
 
.closest();
.contain(); //Select all elements that contain the specified text.
.contents() // Get the children of each element in the set of matched elements, including text and comment nodes.
	//the method can also be used to get the content document of an iframe, if the iframe is on the same domain as the main page();
.context();//The DOM node context originally passed to jQuery(); if none was passed then context will likely be the document.

:only-child
.offsetParent();
:only-of-type Selector();
```

```javascript
jquery sort method:
	insTbody.append(insRows.get().sort(function(a, b) {
		var aSerial = jQuery(a).find(‘[name="serial"]').val();
		var bSerial = jQuery(b).find('[name=“serial”]’).val();
		return aSerial-bSerial;
	}));
	

sort {
	var myarray=[25, 8, 7, 41]
	myarray.sort(function(a,b){return a - b}) 
	//Array now becomes [7, 8, 25, 41]

	//Sort numerically and descending:
	var myarray=[25, 8, 7, 41]
	myarray.sort(function(a,b){return b - a}) //Array now becomes [41, 25, 8, 71]
	
	//Randomize the order of the array:
	var myarray=[25, 8, "George", "John"]
	myarray.sort(function() {return 0.5 - Math.random()});
	example = { // random from 1 to 14
			for (var i = 0, ar = []; i < 14; i++) { ar[i] = i; }
			ar.sort(function () { return Math.random() - 0.5; });
		}

}
:contains
.nextUntil()

$('p').not(":first").each(function(index){ console.log(index + " : " + this.innerHTML); }); 	

// repalce the text inside the #target div
var target = $('#target');
target.html(target.html().replace(/cat/g,'god'));

.after() puts the element after the element // example: $('#a').after("B"); ->> A B
.before() puts the element before the element // example: add tr or td
.append() puts data inside an element at last index and
.prepend() puts the prepending elem at first index

.after() //inserts the argument after the selector.
.insertAfter() //inserts the selector after the argument.

var p = $("<p />", { "text" : "Howdy" }); 

var fragment = document.createDocumentFragment();
for (var i in some_array) {
	var node = document.createElement('option');
	node.innerHTML = '....';
	fragment.appendChild(node);
}
targetNode.appendChild(fragment);

father.append(children);
children.appendTo(father); // father ex : #ha , not like $("#ha")

//jquery
show();
hide();

//在每个<tr>上绑定点击事件是个非常危险的想法，因为性能是个大问题
var myTable = document.getElementById('my-table');
myTable.onclick = function () {
// 处理浏览器兼容
e = e || window.event; var targetNode = e.target || e.srcElement;
// 测试如果点击的是TR就触发
if (targetNode.nodeName.toLowerCase() === 'tr') { alert('You clicked a table row!'); } }

//create new element
var newDiv = $("<div></div>", { "text": "Hello", "class": "newDiv", "title": "Hello" });

var temp = $("<div/>");
$("p").wrap(temp) // shortcut of .wrap("<div><p></div>");

(location).append(dom);
(dom).appendTo(location)

//do not need to bind the form of sumbit Button
$("form").on("submit", function() { 	alert("you just submitted the form!"); 	}); 
```
	i++ ->> i = i + 1 or i += 1
	var i = 0 ; for(i = 0 ; i ...){ ... }
	
	// 对象
	var man = { hands: 2, legs: 2, heads: 1 };
	
	// 在代码的某个地方
	// 一个方法添加给了所有对象
	if (typeof Object.prototype.clone === "undefined") { Object.prototype.clone = function () {}; }
	var i, hasOwn = Object.prototype.hasOwnProperty; for (i in man) { if (hasOwn.call(man, i)) { // 过滤
		console.log(i, ":", man[i]);  } // second option for checking the property
		
	var f = function foo(){ return typeof foo; };// foo是在内部作用域内有效
	// foo在外部用于是不可见的
	typeof foo; // "undefined"
	f(); // "function"
	
	//匿名闭包
	(function () {
		// ... 所有的变量和function都在这里声明，并且作用域也只能在这个匿名闭包里
		// ...但是这里的代码依然可以访问外部全局的对象
	}());
	
	var blogModule = (function (my) { my.AddPhoto = function () { ...  }; return my; } (blogModule)); 
	
	//所有函数共享同一个[[Scope]]，其中循环变量为最后一次复赋值。
	var data = [];
	for (var k = 0; k < 3; k++) {   data[k] = function () {     alert(k);   }; }
	data[0](); // 3, but not 0
	data[1](); // 3, but not 1
	data[2](); // 3, but not 2
	//solve
	for (var k = 0; k < 3; k++) {   data[k] = (function (x) {     return function () {       alert(x);     };   })(k); // 将k当做参数传递进去 }
	
	(function functionValued() { return function () { alert('returned function is called'); }; })()();
	var x = 10;
	
	// 10, 而不是20
	// 变量"x"在(lexical)上下文中静态保存的，在该函数创建的时候就保存了s
	function foo() {   alert(x); } (function (funArg) {   var x = 20;   funArg(); })(foo);
	
	$.fn.extend({
		uploadFile : function() {
			var $this = $(this);
			($this.is(":file")) ? $this.after($('<font color="white" name="tempFont"></font>').text("")).on('change', function(event) {
				var fileNameList = [];
				var files = $this.prop(‘files’);
				for (var i = 0; i <= files.length - 1; i++) {
					console.log(files[0]);
					fileNameList.push(files[i].name);
				
				// console.log(" 2:07:41 PM : " + files[i].name);
				// console.log(" 2:07:41 PM : " + files[i].type);// image/png
			}
			// console.log(files.length);
			$this.next().text((fileNameList.join(" , ")));
		}) : console.warn("Element " + $this.selector + " type is not file");
	}});
## Upload Image

```javascript
var uplodImg = function(evt) {
	var thisObj = this;
	$(this).next().empty()
	var fileCount = $(this)[0].files.length;
	var files = evt.target.files;
	var filesLen = files.length;
	console.log(" 11:33:06 AM : " + files.length);
	for (var i = 0, f; f = files[i]; i++) {
		if (!f.type.match('image.*')) {
			continue;
		}
		var reader = new FileReader();
		reader.onload = (function(theFile) {
			return function(e) {
				var span = document.createElement('span');
				span.innerHTML = [ '<img class="thumb" src="', e.target.result, '" title="', escape(theFile.name), '"/>' ].join('');
				// document.getElementById('list').insertBefore(span, null);
				thisObj.nextElementSibling.insertBefore(span, null);
			};
		})(f);
		reader.readAsDataURL(f);
	}
};
$(function() {
	$("input[type='file']").on('change', uplodImg);
});
```

```
Validator:
常用JavaScript驗證全集--限制輸入(中文， 數字， 英文等)
1. 只能輸入漢字的
    <
    input onkeyup = "value=value.replace(/[^\u4E00-\u9FA5]/g,'')"
onbeforepaste = "clipboardData.setData('text',clipboardData.getData('text').replace(/[^\u4E00-\u9FA5]/g,''))" >

    2. 只能輸入數字的： <
    input onkeyup = "value=value.replace(/[^\d]/g,'') "
onbeforepaste = "clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))" >


    簡易禁止輸入漢字 <
    input type = "text"
style = "ime-mode:disabled" >

    輸入數字和小數點：
onkeyup = "value=value.replace(/[^\d{1,}\.\d{1,}|\d{1,}]/g,'')"


javascript 只能輸入數字和 ":"
.2007 - 11 - 24 15: 50 < input type = text id = "aa1"
onkeyup = "this.value=this.value.replace(/[^\d&:]/g,'')"
onblur = "this.value=this.value.replace(/[^\d&:]/g,'')"
onafterpaste = "this.value=this.value.replace(/[^\d&:]/g,'')" / >

    只能數字和 ":", 例如在輸入時間的時候可以用到。

    <
    input type = text id = "aa"
onkeyup = "value=value.replace(/[^\w&=]|_/ig,'')"
onblur = "value=value.replace(/[^\w&=]|_/ig,'')" / >

    只能輸入字母和等號， 不能輸入漢字。

其它的東西：
只能輸入數字的腳本javascript - -
    1. < input onkeyup = "this.value=this.value.replace(/\D/g,'')"

onafterpaste = "this.value=this.value.replace(/\D/g,'')" >

    上半句意思是鍵盤鍵入只能是數字， 下半句是粘貼也只能是數字


2. < input name = txt1 onchange = "if(/\D/.test(this.value)){alert('只能輸入數字');this.value='';}" >


    3. < input onkeyup = "if(isNaN(value))execCommand('undo')"
onafterpaste = "if(isNaN(value))execCommand('undo')" >

    JavaScript限制只能輸入數字和英文 - -

    function isregname(checkobj) {
        var checkOK = "0123456789-_abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
        var checkStr = checkobj;
        var allValid = true;
        var decPoints = 0;

        for (i = 0; i < checkStr.length; i++) {
            ch = checkStr.charAt(i);
            for (j = 0; j < checkOK.length; j++)
                if (ch == checkOK.charAt(j))
                    break;
            if (j == checkOK.length) {
                allValid = false;
                break;
            }
        }
        return (allValid)
    }

-- -- -- -- -- -- -- --
```

```
    input onkeyup = "value=value.replace(/[\W]/g,'') "
onbeforepaste = "clipboardData.setData('text',clipboardData.getData('text').replace(/[^\d]/g,''))" >
```

```
5. 可以用Javascript對文本框進行檢查， 過濾掉非0 - 9 的字元。

if (event.srcElement.name == 'TextBox1') {
    if (!KeyIsNumber(event.keyCode)) {
        return false; //這句話最關鍵
    }
}

function KeyIsNumber(KeyCode) {
    //如果輸入的字元是在0-9之間，或者是backspace、DEL鍵
    if (((KeyCode > 47) && (KeyCode < 58)) || (KeyCode == 8) || (KeyCode == 46)) {
        return true;
    } else {
        return false;
    }
}

```

最大的差別：bind()的事件函式只能針對已經存在的元素進行事件的設定。如果你想對動態建立的元素bind()事件，是沒有辦法達到效果的，但是live(),on(),delegate()均支援未來新新增元素的事件設定。

