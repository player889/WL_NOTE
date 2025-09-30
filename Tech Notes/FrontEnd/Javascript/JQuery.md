# JQuery

[TOC]

## Get Element Length

```javascript
<div id="foo">
    <div id="bar"></div>
    <div id="baz">
    <div id="biz"></div>
    <span><span>
</div>
$("#foo > div").size(); //jQuery code to count child elements
```
## Upper Case

```javascript
(function ($) { //轉大寫
$.fn.uppercase = function (p) {
    var $input = $(this);
    $input.css({'text-transform': 'uppercase'}).on('change',function() {$(this).val( $(this).val().toUpperCase() );});
    return $input;
}; })(jQuery);
```
## Selector

```javascript
#　　	指示　id　
.　　	指示　class　
*　　	全选　
,　　	多选　
空格　所有后代　//ex: $('#option1 :input'') ->> slelect all  inputs(children) from option(div)
>　　	子　
~　　	兄弟　
+　　	下一个　
:　　子(多功能)　
()　　函数式的过滤与查找

$('#target option').filter(':odd').css("background","blue");
$('#target option:odd').css("background","blue");

$("input[name='element12']")
$("input[name='element" +myVar+ "']")

$("div>ul a");
$("div#main p strong");
$("div.main p>li a");

//This will return all strong elements that are direct children of paragraphs, where those paragraphs exist within a div.
$("div").find("p>strong"); 

//Add the previous set of elements on the stack to the current set.
.andSelf();
.add(DOMelements);

// ex : $('#flip ul li:eq(0)').nextAll();
// gets all siblings that are after the current element in the DOM structure
.nextAll(); 

$(".second").siblings() //gives two elements—the first paragraph and the last.
prev() and next(); //one before or after current element.

$("strong").parents().not("html, body");
.parentsUntil(DOM); //get all parents dom elements which after the DOM elements

filter() – search through all the elements.
find() – search through all the child elements only.

var jq = { s1: $('#s1'), s2: $('#s2'), s3: $('#s3') },
    jq.s1

.eq()               		Method   $('li').eq(0)
:eq()               		selector:  $('li:eq(0)')
.first()         			Method   $('li').first()
:first             			selector:  $('li:first')
:first-child selector: 		$('li:first-child')
:lt()              			selector:  $('li:lt(1)')
:nth-child() selector:  	$('li:nth-child(1)')

html(), html(val) 讀取或設定 innerHTML
text(), text(val) 讀取或設定 innerText
$(X).append( Y ) 在 X 內新增 Y 當成最後的子元素
$(X).appendTo( Y ) 將 X 附加到 Y 內作為最後的子元素
$(X).prepend( Y ) 將 Y 插入 X 內當作第一個子元素
$(X).prependTo( Y ) 將 X 插入 Y 內當作第一個子元素
$(X).after( Y ) 將 Y 接在 X 後方
$(X).before( Y ) 在 Y 插入到 X 前方
$(X).insertAfter( Y ) 將 X 接在 Y 的後方
$(X).insertBefore( Y ) 將 X 插入到 Y 的前方
$(X).wrap( Y ) 用 Y 將 X 包起來(與 wrapAll 最大的差別在於 wrap 時每個X都會被一個 Y 包住)
$(X).wrapAll( Y ) 將所有 X 元素集中在一起並用一個Y包起來
$(X).wrapInner( Y ) 將 X 的子元素用 Y 包起來
$(X).replaceWith( Y ) 將 X 置換成 Y
$(X).replaceAll( Y ) 以 X 去置換 Y
$(X).clone() 複製一份 X
empty()
wrap()
unwrap()
hide() sets the matched elements CSS display property to none.
remove() removes the matched elements from the DOM completely.
detach() is like remove(), but keeps the stored data and events associated with the matched elements.

//將陣列元素 (v) 與其排序 (i) 傳給 callback，return true/false 決定該陣列元素留下或捨棄，可以用來過濾陣列。
jQuery.grep(array, function(v, i) { ... }); 

//將陣列元素 (v) 處理過傳回，傳回值若為 null 則移除該元素，傳回值若為陣列則陣列中的元素都會被新增，最後得到一個處理過的新陣列。
jQuery.map(array, function(v, i) { ... }); 
var nameList = $.map(people, function(item, index) {
  return item.name;
});
                                  
//檢查 val 有無存在於 array 中。
jQuery.inArray(val, array); 
                                  
//合併兩個陣列，若要排除重覆元素則可用jQuery.unique(array)事後處理。
jQuery.merge(array1, array2); 
                                  
// jQuery.isFunction(obj) 判別是否為陣列 / 函數。
jQuery.isArray(obj) 
                                  
//去掉字串頭尾的空白
jQuery.trim(s); 
                                  
//將陣列 / 物件 / jQuery 物件序列化成一個字串，格式如: width=1680&height=1050。
jQuery.param(array/object/jQuery) 
                                  
//不要這樣做  
$('#nav li').click(function(){   
    $('#nav li').removeClass('active');
    $(this).addClass('active');
});  
//這樣才帥氣  
$('#nav li').click(function(){  
	$(this).addClass('active').siblings().removeClass('active');
});
//jquery get nodename 
$(this).context.nodeName.toLowerCase();
//insert as previous sibling
element.insertAdjacentHTML(“beforebegin”, “<p>Hello world!</p>”);
//insert as first child
element.insertAdjacentHTML(“afterbegin”, “<p>Hello world!</p>”);
//insert as last child
element.insertAdjacentHTML(“beforeend”, “<p>Hello world!</p>”);
//insert as next sibling         
element.insertAdjacentHTML(“afterend”, “<p>Hello world!</p>”); 
                           
//How to get an Array with jQuery, multiple <input> with the same name
<input type="text" id="task" name="task[]" />
var values = $("input[id='task']").map(function(){return $(this).val();}).get();

```

## Table

```javascript
var mytable = document.getElementById('tableid');
var myrows = mytable.getElementsByTagName("tr");
var lastrow = myrows[myrows.length -1];
var mycells = lastrow.getElementsByTagName("td");
var lastcell = mycells[mycells.length -1];
$('td input:checkbox', "table[name='list']").prop('checked',this.checked);

//trigger checkbox inside td
$("td[name='checkboxArea']").on("click", function(event) {
	if (!$(event.target).is('input')) { $('input:checkbox', this).prop('checked', function(i, value) { return !value; }); }
});
```
## checkbox & radio

```javascript
document.getElementsByName("way")[i].checked = true;
form.radio_name[j] 

$("input:radio[name='"+id+"']'").filter("[value='"+value+"']").prop('checked', true);

$("#a").bind("click", this , function(event) { console.log(this.checked); }

var tmep =   XXX.checked; if(temp){ ... }

if($('#copyForm').is(':checked')){ ... }

$('input[name="genderS"]:checked').val();
var radios = document.getElementsByName('genderS'); //same as below
for (var i = 0, length = radios.length; i < length; i++) {
	if (radios[i].checked) {
		console.log(radios[i].value);
		// only one radio can be logically checked, don't check the rest
		break;
	}
}	 

//To get the value of the selected radioName item from form "myForm"
$('input[name=radioName]:checked', '#myForm').val();

function get checkbox value (){
	<input class="messageCheckbox" type="checkbox" value="3" name="mailId[]">
	<input class="messageCheckbox" type="checkbox" value="1" name="mailId[]">
	var checkedValue = null; 
	var inputElements = document.getElementsByTagName('input');
	for(var i=0; inputElements[i]; ++i){
	  if(inputElements[i].className==="messageCheckbox" &&  inputElements[i].checked){
			checkedValue = inputElements[i].value;
		break;
		}
	}
}

var specialSection = document.getElementById("A"); specialSection.onclick = show;

var temp = document.getElementById("t").getElementsByTagName("td");
console.log(temp[2].children.length);
document.getElementById("t").getElementsByTagName("td")[2].getElementsByTagName("span");

//make radio readonly
$(':radio:not(:checked)').attr('disabled', true);
```
## disabled

	document.getElementById('TetstText').disabled=true;
	$("#age").attr("disabled", false);
	$("#option2_2").children().attr("disabled", true);
	
	var elem = document.getElementById("first_td");
	elem.style.display = "none";
	
	select.option[i] ,select.options.length
	
	remove disabled : part1[i].disabled = false;
## DDl & Select Options

```javascript
//mutiple select without + ctrl
$('#list').on('mousedown','option',function(e) {
e.preventDefault();
$(this).prop('selected',$(this).prop('selected') ? false : true);
return false;
});

//Get every select of option index;
$("option:selected", $(this) ).index()

//get the previous select option elemeent
selecotr.focus(function(){});

//$.map with select options
var options = $('#selectBox option');
var values = $.map(options ,function(option) { return option.value; }); 

var e = document.getElementById("ddlViewBy");
var strUser = e.options[e.selectedIndex].value;
var strUser = e.options[e.selectedIndex].text;
//index with option
var x=document.getElementById("mySelect").selectedIndex;
var y=document.getElementsByTagName("option");
alert("Is " + y[x].text + " selected by default? " + y[x].defaultSelected);

var x=document.getElementById("mySelect").selectedIndex;
document.getElementsByTagName("option")[x].value);

$("#select").append($("<option></option>").attr("value", "值").text("文字"));

$('#mySelect').
append($('<option>', { value: 1, text: 'My option' }));
$.each(items, function (i, item) { $('#mySelect').append($('<option>', { value: item.value, text : item.text })); });

//dynamic create select and options
$('#searchArea')
    .empty()
    .append($('<select/>', {
    'id': 'example_search_input',
    'type': 'select',
    'name': 'deviceType',
    'class':'form-control',
    'style':'width:200px'
	}).append(
        $.map(('stage'===value)?_stage:_billType, function(item, index) {
            return $('<option/>',{value:item.value,text:item.key});
        })
	).on('click',function(){}));


//IE8
$("#selectList").append(new Option("option text", "value"));

$('#citys1 option:eq(0)').before("<option>AAA</option>");

selectElement : selectIndex, tabIndex, mutiple, add(element, HTMLElement before), remove(long index);

//css make select box dissected fixed
.selectDisabled{ pointer-events: none; }

prop() on later version of jquery on select dom.

//jquery get the option from select
$(this).children().map(function() {return $(this).val();}).get()

//說明：使用default select 的 value 來達到不管再怎麼選還是原來選的那一個
<select name="s1" onfocus="defaultValue=this.value" onchange="this.value=defaultValue">
//select readonly - using "data" attribute
$('#intakeDepartment').val(val).trigger('change').on('select2:open', function () {
    $(this).data('previous', $('#parentName').val() ? this.value : '');
}).on('select2:select', function (e) {
    let val = $('#parentName').val() ? $(this).data('previous') : this.value;
    $(this).data('previous', val);
    $(this).val(val).trigger('change');
});

var myOptions = { val1 : 'text1', val2 : 'text2' };
var mySelect = $('#mySelect'); // * cannot be .each loop
$.each(myOptions, function(val, text) { mySelect.append( $('<option></option>').val(val).html(text) ); });

$('#mySelect option').eq(1).prop('selected', true);

var id = 'sel02';
$('#mySelect option').filter(function(){return this.id === id }).prop('selected', true);

ex: $("select[id='"+newId+"']").val($("select[id='"+newId+"'] option:first").val());

for (var i = 0; i < document.getElementById("carType").options.length; i++) {
    if (selectObj.options[i].value == strUser) {
        selectObj.options[i].selected = true;
    }
}

//mutiple select option1
$(document).ready(function(){ function displayVals() { 
var singleValues = $("#single").val(); $("p").html(singleValues.join(",")); } 
$("select").change(displayVals); });

//option2
var realvalues = []; var textvalues = [];
$('#multiplelistbox :selected').each(function(i, selected) {
	realvalues[i] = $(selected).val();
	textvalues[i] = $(selected).text();
});

//option3
var str = "";
$("#list").change(function() { $("#list option:selected").each(function() { str += $(this).text() + " "; }); }).trigger('change');
//  先取得要移除項目的 index
var selectIndex = $("#select").find(":selected").index();
//  移除選擇的項目
$("#select").find(":selected").remove();

//  判斷移除項目後，原先的index是否還有option，有的話就直接將此option設定為選取狀態
//  捲軸就不會往上跑了
if ($('#select option').get(selectIndex) != null) {
	$('#select option').get(selectIndex).selected = true;
}
else {
    //  沒有項目的話，判斷select理是否還有option
    //  有的話，表示移除的項目為最後一個，就設定上一個option為選取狀態
    if ($('#select option').length > 0) {
        $('#select option').get(selectIndex - 1).selected = true;
	}
}

var options = jQuery(carTypes).map(function () {
    return {"value" : this.catpcd, "label" : this.catpcd +' '+ this.catpcdName};
});
$select.append('<option value="'+options[i].value+'">'+options[i].label+'</option>');

//set ddl options with ajax
function initSelect(defaultVal) {
    $("select#countries").change(function() {
        $.getJSON("/ajax/exhibition/getstates.php?idCountry=" + $("select#countries").val(), function(j) {
            var options = '';
            $.each(j, function(key, value) {
                options += '<option value="' + key + '">' + value + '</option>';
            });
		$("select#state").html(options);
        $("select#state option[value=" + defaultVal + "]").attr('selected', 'selected');
        });
    }).change();
}
$(document).ready(function(){
    initSelect($('#statepreset').val());
});
```

## CSS

```css
// Use toggleClass for toggling classes, use addClass for adding classes.
.toggleClass.(“example”); 
```

## map - options / json return str

```javascript
$("p").append($(':checkbox').map(function() {return this.id;}).get().join(‘,’)); 
->>two,four,six,eight

var html = $.map(lcns, function(lcn) {
    return '<option value="' + lcn + '">' + lcn + '</option>'
}).join('');

addNewOption = function (target, data) {
    var _options = $.map(data, function (k, v) {
        return new Option(k, v);
    });
    target.html(_options);
}

jQuery - get all src of images in div and put into field
var images = $('.thumbnailArrows').children('img').map(function(){
	return $(this).attr('src')
}).get();

for(var i = 0; i < 3; i++) {
    arr[i] = function() {
        console.log("function " + i);
    }
}
var arr = [0,1,2].map(function(i){
    return function(){
		console.log("function " + i);
    };
}); // better way 
```
## Ajax

```javascript
var str = jQuery.param( { width:1680, height:1050 } );

Use Google Gson. It really eases converting (collections/maps of) Java objects to JSON strings

//getJSON
jQuery.getJSON( url [, data ] [, success( data, textStatus, jqXHR ) ] );
$.ajax({ dataType: "json", url: url, data: data, success: success }); 	

JSON.stringify(XXX) // console.log(JSON.stringify(data)); ( data from server)
JSON.parse(XXX): Takes a JSON string and parses it to a JavaScript object.
//get all key and value. if value is = object then print !!!!
JSON.parse('{"1": 1, "2": 2,"3": {"4": 4, "5": {"6": 6}}}', function (k, v) { console.log("K :" + k + ", V : " +v); if(typeof v == "object" ) console.log("!!!"); });
//get all key and values as object from nest object.
var a = {"1": 1, "2": 2, "3": {"4": 4, "5": {"6": 6}}}; var b = {}; JSON.parse(JSON.stringify(a), callBack); function callBack(k,v){     if(k != "" && typeof v != "object"){console.log("key :  " +k+ " , value : " + v);         return b[k] = v;     } } console.log(JSON.stringify(b));
//If k === "3", then delete this element from json by return "undefined". NOTE: must return v, otherwise it will cuases undefined error message.
var a = JSON.parse('{"1": 1, "2": 2,"3": 3}', function (k, v) { if(k === "3"){ console.log(k); return undefined; }else{ return v; } }); console.log(a);
ajax the success:function(j) ->> the j object might be presenet as the data object such as data:{json: XXX}	

jQuery 會自動幫你把 dataType 設成 script 好讓 browser 認得要執行，這樣就不會去呼叫 success 或 complete 的 callback ，而是去呼叫你自己定義的 callback function 。
function processData(data) {
  $('#msg').html(data.msg);
  $('#msg').fadeIn();
  setTimeout(function(){
	$('#msg').fadeOut();
	$(e.target).attr('disabled', false);
  }, 3000);
}
function showMsg(e) {
	$(e.target).attr('disabled', true);
	$.ajax({
		url: 'msg.php',
		dataType: 'jsonp',
		jsonp: 'processData',
		data: {gender: $('#gender').val(), name: $('#name').val(), callback: 'processData'},
		error: function(xhr) {
		  alert('Ajax request 發生錯誤');
		  $(e.target).attr('disabled', false);
		}
	}
};	

loadChinese(["addressType"],function(json){ ChineseObj.addressType 	=  json; });
//array : url path to controller to load chinese json object
var loadChinese = function(urlArray,callback){
	var json = {},o = {},o.params = {};
	o.success = function(data) {
		if (data.rc != '0000') {
			twq.alertErrMsg(data.rm);
		} else {
			 json = $.extend({}, json, data.data);
			if(typeof callback === "function") callback(json);
		}
		$.unblockUI();
	};
	$.each(urlArray,function(index){
		o.url = "const/"+urlArray[index];
		twq.ajax(o);			
	});
};
```

## Each

```javascript
//pass this element and continue.
$.each(a,function(index){if(a[index] == "AAA") return true;});

// "this" is the current element in the loop });	
$('#mydiv').children('input').each(function () { alert(this.value); });

forEach(mailArchive[mail].split("\n"), handleParagraph);

obj.each(function(){ if(expr){return false} //跳出循环 }

var sum = 0; $('.add').each(function(){ $(this).on('keyup',function(){ sum = sum + parseInt($(this).val()); $('#sum').val(sum);	 }); }); 	
$.each(json,function(index,elem){ $.each(elem,function(k,v){ mySelect.append( $('<option></option>').val(k).html(v) ); }); });

//get json key and value
var a = { "rc": "0000", "rm": null, "data": { "63": "104夢想搖籃", "312": "104測試商城" } };
console.log( a.data); $.each(a.data,function(index,elem){ console.log(elem); console.log( index); });

//取得list key and value
var arr =  [ { key: 'Adidas', value: 100 }, { key: 'Nike', value: 50 } ];
$.each(arr, function() { var key = Object.keys(this)[0]; var value = this[key]; });
$.each(data, function(index, obj){
if (index == 1) {
    return; // 等於continue
} else {
    return false; // 等於break
}
});
```

## Event

```javascript
mouseleave()
mouseout()
mouseenter()
mouseover()

//use on after jquery 1.7
var p = $("p");
p.on("click",p.textContent,function(){ console.log("helo"); });
p.on("click",this,function(){ console.log("helo"); }); 	

function init() {
	for (var i = 0; i < elm.length; i++) {
		if (window.addEventListener) { //Firefox, Chrome, Safari, IE 10
			elm[i].addEventListener('mouseover', highlight, false);
			elm[i].addEventListener('mouseout', unhighlight, false);
		} else if (window.attachEvent) { //IE < 9
			elm[i].attachEvent('onmouseover', highlight);
			elm[i].attachEvent('onmouseout', unhighlight);
		}
	}
}
if (window.addEventListener) { //when document is loaded initiate init
    document.addEventListener("DOMContentLoaded", init, false);
} else if (window.attachEvent) {
    document.attachEvent("onDOMContentLoaded", init);
}

jQuery("#selector").bind("click", {name: "barney"}, clickHandler);
function clickHandler(e) { console.log(e.data.name); }

$("#a").bind("click", b() , function(event) { ... }

document.getElementById("outside").addEventListener("click", modifyText, false);
productLineSelect.addEventListener('change', function() { foo('bar');}, false);  function foo(message) { alert(message); }formElement.addEventListener("keyup",function(e) {testIt(e, myObject)}, true);
formElement.addEventListener("keyup",ha, true); function ha(e){ e.target.style.visibility = 'hidden'; }
element.addEventList('click',bar=function() { foo(id);},false);
element.removeEventListener('click',bar,false);

window.onload = function() {
	var way = document.getElementsByName("option");
	for (var i = 0; i < way.length; i++) {
		way[i].addEventListener("click",  function(){ show(this); },false);
	}
	function show(obj){
		console.log(obj);
	};
};

var btn = document.getElementById(“myBtn”);
btn.onclick = handler;
btn.onmouseover = handler;
btn.onmouseout = handler;
var handler = function(event){
	switch(event.type){
		case “click”:
			alert(“Clicked”);
		break;
		case “mouseover”:
			event.target.style.backgroundColor = “red”;
		break;
		case “mouseout”:
			event.target.style.backgroundColor = “”;
		break;
	}
};

var message = "Spoon!";
$( "#foo" ).bind( "click", { msg: message }, function( event ) { alert( event.data.msg ); });

inputs[i].onchange = showChecked // call showChecked function

//luve vs bind
$(function(){  // bind
	 //$(".newButton").click(fn) 同等於 $(".newButton").bind("click",fn)
	 $(".newButton").bind("click",geterateButton);
});

$(function(){ //live not in used in jquery 1.7
	$(".newButton").live("click",function(){
		//將".newButton"保留
		$(this).after($("<input type='button' value='New Button' class='newButton' />"));    
	});
});

//.on
As of jQuery 1.7, the .on() method is the preferred method for attaching event handlers to a document.
$(elements).on(events [, selector] [, data], handler);  - http://itseer.blogspot.tw/2012/03/jquery-on.html
events 是一個事件型態，它可以是一個或多個事件字串，例如可以輸入"click"或是"click keyup"，注意多個事件必須使用空格隔開。
selector 指定這個DOM元素，這個元素的後代元素都會接受event的傳導，它是optional的,如果沒有指定，表示開事件每次發生都會觸發
data 可以傳入參數到event的callback函式，格式為{ }
handler  該event 觸發後的處理函式。

listener: function(){ // brower different
	document.getElementById('foo').addEventListener('click', function(){
		this.updateNumber();
	}.bind(this));
}
listener: function(){ // brower different
	var that = this;
	document.getElementById('foo').addEventListener('click', function(){
		that.updateNumber();
	});
}
```
## clourse

```javascript
// local variables are “re-created” every time a function is called
function f(){ var b = "b"; return function(){ return b; } }
var temp = f(); temp(); // call function 
if cosole.log(f());  ->> return function(){ return b; }

//No matter what happens to x in the outer scope, the closure remembers the value of x from the time of the function’s execution.
var x = 42; console.log(x); var message = (function(x) { return function() { console.log("x is " + x); }; })(x); message(); x = 12; console.log(x); message();

function makeAdder(x) { return function(y) { return x + y;};} var add5 = makeAdder(5); var add10 = makeAdder(10); 
print(add5(2));  // 7
print(add10(2)); // 12
```
## JSON

```javascript
$(function(){
	var el = ['node','value'];
	var jsonData = {};
	$.each(el,function(index,object) { jsonData[this] = "A"; });
	viewData.first.push(jsonData);
	
	var myJsonText=JSON.stringify(viewData);
	console.log(myJsonText);
};
->> {"first":[{"node":"A","value":"A"}]}
```

## Stop using gobal ajax setting

```javascript
$.ajax({
    url: longPollUrl,
    global: false,     // this makes sure ajaxStart is not triggered
    dataType: 'json',
    complete: longpoll,
    success: function(data){
        
    }, 
});
```

## Dynamic Load Page and Javascript

```html
<div id="temp"></div>
```

```javascript
$('#temp').load('/contact/pick', function() {
    $.getScript("/js/contact/contactPick.js", function() {
		...
    });
});
```

```java
@GetMapping("/contact/pick")
public String contacPick(Model model) {
    log.info("====contactPick====");
    return "/modal/contactPickModal";
}
```

## JQuery class start with

Using `*` in multiple class 

```javascript
$("[class*='tab']");
```

```html
<div class="module tab121232441"></div>
<div class="module tab123134545"></div>
```

## Jquery Chain fn

```javascript
$.fn.extend({
    vf: function(){
        if(!$(this)[0].checkValidity()){
            $(this)[0].classList.add('was-validated');
            Swal.fire('Check Inputs','' ,'warning');
            this.pass = false;
        }
        return this;
    },
    aa: function(){
        console.log("XXX" + this.pass);
    }
});
//html
$('#invoiceForm').vf().aa();
```

## Jquery Chain fn with mutiple selector

```javascript
<input id="a" value="1"/>
<input id="b" value="2"/>
$.fn.extend({
	show() {
		this.each(function () {
			let v = $(this).val();
			console.log(v);
		});
	}
});
$('#a, #b').show();
//==
1
2
```



## Extend

```javascript
以jQuery.extend(objA, objB)為例，你可以想像成objA與objB各有一些屬性(方法也會比照處理，在此只提屬性)，extend()會將objB有而objA沒有的屬性加到objA裡，如果objB裡的某個屬性，objA裡剛好也有同名的屬性，則會用objB的屬性值去覆寫objA原有的屬性。objA最後就是整合結

jQuery.extend的最常見的用途是用來處理Plugin或函數的傳入參數，比如函數會用到的參數有10個，但大部分情況呼叫時只需要指定其中一兩個，其餘的用預設值即可。於是我們可以在函數中宣告一個預設值物件objDefault，裡面已有10個屬性，呼叫函數時則傳入objOption，裡面只放入一兩個要變更的屬性值，經過var objSetting = jQuery.extend(objDefault, objOption)之後，我們得到10個"有指定用指定值，沒指定用預設值"的屬性供後續使用。

jQuery.fn.makeTextColored = function(settings){
		var config = {
			'color': 'red'
		};
		if (settings){$.extend(config, settings);}
	
	return this.each(function(){
		$(this).css('color', config.color);
	});
};
$('#my-div').makeTextColored(); // make text red (default)
$('#my-div').makeTextColored('blue'); // make text blue
```

## index() always return 1;

```javascript
var $as = $('.container .product a').click(function() {
    var a = $as.index(this); 
    //or $(this).index('.container .product a');
	console.log(a);
});
```

## Cascade / depend dropdown - 聯動式選單

## Using javascript in jquery

```javascript
$('#a').on('change', function(){
    console.log(this.value);
});
this.id (as you know)
this.value (on most input types. only issues I know are IE when a <select> doesn't have value properties set on its <option> elements, or radio inputs in Safari.)
this.className to get or set an entire "class" property
this.selectedIndex against a <select> to get the selected index
this.options against a <select> to get a list of <option> elements
this.text against an <option> to get its text content
this.rows against a <table> to get a collection of <tr> elements
this.cells against a <tr> to get its cells (td & th)
this.parentNode to get a direct parent
this.checked to get the checked state of a checkbox Thanks @Tim Down
this.selected to get the selected state of an option Thanks @Tim Down
this.disabled to get the disabled state of an input Thanks @Tim Down
this.readOnly to get the readOnly state of an input Thanks @Tim Down
this.href against an <a> element to get its href
this.hostname against an <a> element to get the domain of its href
this.pathname against an <a> element to get the path of its href
this.search against an <a> element to get the querystring of its href
this.src against an element where it is valid to have a src
```

## Jquery thousand speartor - 千分位

```javascript
$(this).keyup(function(event) {
    console.log("XX");
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


