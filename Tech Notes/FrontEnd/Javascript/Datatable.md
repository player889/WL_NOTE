[TOC]

# DataTable

## Sample with Spring mvc

### Javascript

```javascript

$('#example').DataTable({
	"processing": true,
	// "pagingType": "full_numbers",
	"serverSide": true,
	"pageLength": 10,
	"paging": true,
	"autoWidth": false,
	"searching": false,
	"ordering": false,
	"dom": "<'row'<'col-sm-12 col-md-6'B><'col-sm-12 col-md-6'f>>" +
	"<'row'<'col-sm-12'tr>>" +
	"<'row'<'col-sm-12 col-md-5 text-left'i><'col-sm-12 col-md-7'p>>",
	"buttons": {
		buttons: [{
				text: 'Create Account',
				className: "btn btn-primary",
				action: function () {
					$('#accountModal').modal('show');
				},
			}, {
				text: 'Create Contact',
				action: function () {
					$('#contactModal').modal('show');
				}
			}
		],
		dom: {
			button: {
				tag: "button",
				className: "btn btn-primary"
			}
		}
	},
	"columns": [{
			"title": "Account Name",
			"data": "name"
		}, {
			"title": "Company Owner",
			"data": "companyOwner"
		}, {
			"title": "Phone",
			"data": "phone",
			"defaultContent": ""
		}, {
			"title": "Email",
			"data": "email",
			"defaultContent": ""
		}, {
			"title": "Fax",
			"data": "fax",
			"defaultContent": ""
		}
	],
	"sAjaxSource": "/account/lists",
	"fnServerData": retrieveData,
});

function retrieveData(sSource, aoData, fnCallback) {
	console.log(aoData);
	let token = $("meta[name='_csrf']").attr("content");
	let header = $("meta[name='_csrf_header']").attr("content");
	$.ajax({
		global: false, //取消使用Jquery global settings
		url: "/account/lists", // 这个就是请求地址对应sAjaxSource
		data: JSON.stringify(aoData), // 这个是把datatable的一些基本数据传给后台,比如起始位置,每页显示的行数
		type: 'post',
		dataType: 'json',
		contentType: "application/json",
		beforeSend: function (xhr) {
			xhr.setRequestHeader(header, token);
		},
		success: function (result) {
			console.log(result);
			fnCallback(result); // 把返回的数据传给这个方法就可以了,datatable会自动绑定数据的
		},
		error: function (msg) {
			console.log(msg);
		}
	});
}

//傳入必要資料
function filter(aoData) {
	let json = {};
	for (let key of aoData) {
		if (key.name == 'sEcho') {
			json["sEcho"] = '' + key.value;
		}
		if (key.name == 'iDisplayStart') {
			json["iDisplayStart"] = '' + key.value;
		}
		if (key.name == 'iDisplayLength') {
			json["iDisplayLength"] = '' + key.value;
		}
	}
	console.log(json);
	return json;
}

/*
aoData
(10) [{…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}, {…}]
0: {name: "sEcho", value: 3}
1: {name: "iColumns", value: 5}
2: {name: "sColumns", value: ",,,,"}
3: {name: "iDisplayStart", value: 0}
4: {name: "iDisplayLength", value: 10}
5: {name: "mDataProp_0", value: "name"}
6: {name: "mDataProp_1", value: "companyOwner"}
7: {name: "mDataProp_2", value: "phone"}
8: {name: "mDataProp_3", value: "email"}
*/
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

### Java - Controller

#### 方法一 用javascript Filter接必要數值

```java
public class VV {

	private String sEcho;
	private String iDisplayStart;
	private String iDisplayLength;
    //Getter and Setter
}
```

```
@PostMapping(value = "/account/lists")
@ResponseBody
public String tableDemoAjax(@RequestBody VV v) {
	...
}
```
#### 方法二 抓取全部資料

```java
@Data
public class DataTableReqDto {
	private String name;
	private String value;
}

@PostMapping(value = "/account/lists")
@ResponseBody
public String tableDemoAjax(@RequestBody List<DataTableDtov> v) {
    //轉型 : List to Map
	Map<String, String> result = v.stream().collect(Collectors.toMap(DataTableDtov::getName, DataTableDtov::getValue));
    int length = Integer.parseInt(result.get("iDisplayLength"));
	int start = Integer.parseInt(result.get("iDisplayStart"));
	int sEcho = Integer.parseInt(result.get("sEcho"));
    
    //Search text from datat
    String name = String.valueOf(map.get("sSearch"));
    ...
}
```

### Dao findAll

```java
int length = Integer.parseInt(map.get("iDisplayLength"));
int start = Integer.parseInt(map.get("iDisplayStart"));
int currentPage = start / length;
PageRequest pageRequest = PageRequest.of(currentPage, 10, Sort.Direction.ASC, "id");
Page<AccountEntity> list =  accountRepository.findAll(pageRequest);
```

### Dao - with Search txt

```java
int length = Integer.parseInt(map.get("iDisplayLength"));
int start = Integer.parseInt(map.get("iDisplayStart"));
String name = String.valueOf(map.get("sSearch"));
int currentPage = start / length;
Pageable pageRequest = PageRequest.of(currentPage, length, Sort.Direction.DESC, "id");
return accountRepository.findByNameContainingIgnoreCase(name, pageRequest);
```

### Return

```java
int currentPage = start / length;
Page<AccountEntity> list = accountService.findAll(currentPage);
JSONObject getObj = new JSONObject();
getObj.put("sEcho", sEcho);
getObj.put("iDisplayStart", result.get("iDisplayLength"));
getObj.put("iTotalRecords", list.getTotalElements());
getObj.put("iTotalDisplayRecords", list.getTotalElements());
getObj.put("data", list.getContent());
return getObj.toString();
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

### Warning

Using Lombok in return bean, it will cause an error that the names are different.

```java
//different field name with captical I
resp.setiDisplayStart(map.get("iDisplayLength")); // getter and setter
resp.setIDisplayStart(map.get("iDisplayLength")); // Lombok
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

## Index Column With Ajax

```htm
<table id="example" class="table table-striped table-bordered" style="width: 100%">
	<thead>
		<tr>
            <th></th> <!--Index -->
            <th>Name</th>
            <th>Owner</th>
            ...
		</tr>
	</thead>
</table>
```

```javasc
"columns": [
    {
        "data" : "",
        "title" : "Index",
        "render": function ( data, type, full, meta ) {
        	return meta.row + parseInt(meta.settings._iDisplayStart)+ 1;
    	}
	},
	...
]
```

## Customized button in each row

```html
            ...
			<th>Fax</th>				
            <th></th>
		</tr>
	</thead>
</table>
```

```javascript
"columns": [
    {},
    ...
	{
        "data": "",
        "title": "Function",
        "className": "center text-center",
        "defaultContent" : "<button class='btn btn-outline-info'>Edit</button>"
	}
]

$('#example tbody').on( 'click', 'button', function () {
    var data = t.row( $(this).parents('tr')).data();
    console.log(data);
});
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

## Text Align

```javascript
"columnDefs": [ { className: 'text-center', targets: '_all'} ]
```

```css
table.dataTable tbody td {
	vertical-align: middle;
}
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

### BlockUI

```javascript
$("#myDataTalbe").on('preXhr.dt', function (e, settings, data) {
	$.blockUI();   
})
.on('xhr.dt', function (e, settings, json, xhr) { 
	$.unblockUI();
})
.DataTable({  ... });
```

<p style="text-align:right">
<a href="#DataTable">Back to top</a>
</p>

## ChildRow open/close icon

```css
/*with font awsome 5*/
.details-control{
	text-align: center;
}

table.dataTable td.details-control:before {
	content: '\f0fe';
	font-family: 'Font Awesome\ 5 Free';
	cursor: pointer;
	font-size: 22px;
	color: #55a4be;
}
table.dataTable tr.shown td.details-control:before {
	content: '\f146';
	color: black;
}

/*with image*/
td.details-control {
	background: url('../image/details_open.png') no-repeat center center;
	cursor: pointer;
}

tr.shown td.details-control {
	background: url('../image/details_close.png') no-repeat center center;
}
```



## Other

```
(document).ready(function () {
	$('#example').dataTable({
		"sScrollX": "100%", //表格的寬度
		"sScrollXInner": "110%", //表格的內容寬度
		"bScrollCollapse": true, //當顯示的數據不足以支撐表格的默認的高度時，依然顯示縱向的捲動條。(默認是false)
		"bPaginate": true, //是否顯示分頁
		"bLengthChange": true, //每頁顯示的記錄數
		"bFilter": true, //搜索欄
		"bSort": true, //是否支持排序功能
		"bInfo": true, //顯示表格信息
		"bAutoWidth": false, //自適應寬度
		"aaSorting": [[1, "asc"]], //給列表排序 ，第一個參數表示數組 (由0開始)。1 表示Browser列。第二個參數為 desc或是asc
		"aoColumns": [
			null,
			null, {
				"bVisible": true, //不可見
				"bSearchable": false, //參與搜索
			},
			null,
			null
		], //列設置，表有幾列，數組就有幾項
		"bStateSave": true, //保存狀態到cookie *************** 很重要 ， 當搜索的時候頁面一刷新會導致搜索的消失。使用這個屬性就可避免了
		"sPaginationType": "full_numbers", //分頁，一共兩種樣式，full_numbers和two_button(默認)
		"oLanguage": {
			"sLengthMenu": "每頁顯示 _MENU_ 條記錄",
			"sZeroRecords": "對不起，查詢不到任何相關數據",
			"sInfo": "當前顯示 _START_ 到 _END_ 條，共 _TOTAL_ 條記錄",
			"sInfoEmtpy": "找不到相關數據",
			"sInfoFiltered": "數據表中共為 _MAX_ 條記錄)",
			"sProcessing": "正在加載中...",
			"sSearch": "搜索",
			"sUrl": "", //多語言配置文件，可將oLanguage的設置放在一個txt文件中，例：Javascript/datatable/dtCH.txt
			"oPaginate": {
				"sFirst": "第一頁",
				"sPrevious": " 上一頁 ",
				"sNext": " 下一頁 ",
				"sLast": " 最後一頁 "
			}
		}, //多語言配置
		"bJQueryUI": false, //可以添加 jqury的ui theme  需要添加css
		"aLengthMenu": [[10, 25, 50, -1, 0], ["每頁10條", "每頁25條", "每頁50條", "顯示所有數據", "不顯示數據"]]//設置每頁顯示記錄的下拉菜單
	});
});
直接調用默認的設置
$('#example').dataTable();

//設置欄位顯示
columns: [{
		"name": "name",
		"data": "name",
		"title": "aae"
	}, {
		"name": "name",
		"data": "value",
		"title": "vv"
	}
]

Hover:
table.dataTable.hover tbody tr: hover, table.dataTable.display tbody tr: hover{
	background - color:  # F7A6A6
}

If you are trying to set the column header color based on a specific data column, use this:

dtTable = $('#example').DataTable({
		data: myData,
		columns: [{
				data: "Title",
				title: "Person Name"
			}, {
				data: "PositionTitle",
				title: "Position Title",
				className: "dt-specialColor"
			}
		]

	});
Once the class name is set, set the background color in your css file for "th.dt-sepecialColor".

change header backgroud:
table.dataTable.no - footer {
	border - bottom: 1px solid # 111

}

//align text
"columnDefs": [{
		"targets": -1,
		"data": null,
		"defaultContent": "",
		"sClass": center
	}
]

//center pageation
"sDom": '<"row view-filter"<"col-sm-12"<"pull-left"l><"pull-right"f><"clearfix">>>t<"row view-pager"<"col-sm-12"<"text-center"ip>>>'

```

