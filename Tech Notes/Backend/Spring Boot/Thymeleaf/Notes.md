[TOC]

# Thymeleaf

## Template

```html
<div th:fragment="accountModal" class="modal fade" id="accountModal" tabindex="-1" role="dialog" aria-labelledby="accountModal" aria-hidden="true" data-keyboard="false" data-backdrop="static">
    ...
    <!-- USing th:block layout:fragment="script"> to load javascript file -->
    <th:block layout:fragment="script">
        <script type="text/javascript" th:src="@{/js/account/account.js}"></script>
    </th:block>
</div>
```

```html
<div th:replace="/common/js :: js"></div>
<div th:insert="/modal/accountModal :: accountModal"></div>
<div th:insert="/modal/contactModal :: contactModal"></div>
```

## Load model data from javascript in model

```java
model.addAttribute("editData", Object);
```

```html
<div>
	<input type="text" ... />
</div>
<script th:inline="javascript">
	$(document).ready(function () {
		'use strict';
		(function () {
			/*<![CDATA[*/
			let editData = $.parseJSON(JSON.stringify(/*[[${editData}]]*/));
			/*]]>*/
		})()
	});
</script>
```

## Default value

```html
<input type="text" class="form-control" placeholder="" name="name" required 
       th:value="${editData != null ? editData.name : ''}" />
```

```html
<select class="form-control" placeholder="" id="" name="accountSource">
	<option value="">--</option>
	<option th:each="object : ${accountSource}" 
            th:value="${object.key}" th:text="${object.value}"
		   th:selected="${editData != null && editData.accountSource eq object.key}" >
    </option>
</select>
```





