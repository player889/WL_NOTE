1. Run liquibase script, if there is an error run script in `optimis-abo`
    1. batch module currently no used so no need to change db connections
2. Using `wlps-cc` 、`ctbc-wlps-gui` and `ctbc-extras-gui`

<span style="color:red;">`fas:ExclamationCircle`</span> change version 1.10.0 to 1.10.2  
"resolved": "https://registry.npmjs.org/yaml/-/yaml-1.10.2.tgz",

```ad-tip
title: Dependency error

To resolve the dependency issue, we need to install the Main Project "@optimus/core" library into the node module in the CTBC Customisation Project by running "npm install <library path>".
~~~node
npm install dist/optimus/core
~~~



```

---

1. npm uninstall -g @angular/cli
2. npm cache clean
3. npm install -g @angular/cli@12.2.13

---

- npm install --force
- npm install -g @angular/cli
- npm install --legacy-peer-deps

```ad-note
title: npm install --legacy-peer-deps
collapse: true

npm install --legacy-peer-deps

是用於安裝依賴項，同時忽略對等依賴警告的命令。當您的依賴項版本與包的 package.json 中的 peerDependencies 部分指定的期望版本不匹配時，npm通常會發出警告並可能拒絕安裝這些包。

--legacy-peer-deps 參數告訴 npm 使用較舊、更寬鬆的規則來處理對等依賴。這使得 npm 可以安裝那些版本不嚴格滿足對等依賴約束的包。這在處理尚未更新以明確支持您所使用的對等依賴版本的包時可能很有用。

請注意，使用此參數可能會導致項目中的兼容性問題或意外行為。通常建議嘗試通過將項目的依賴項更新為彼此兼容的版本來解決對等依賴衝突。然而，在某些情況下，使用 --legacy-peer-deps 可能是一種快速的解決方法。

```

# Pipe

#pipe

```ts
  status$ = ClearingTransactionDropdown.clearingTransactionStatus
```

```html
<input
	matInput
	[value]="form.value.status | dropdownTranslate: status$"
	disabled
	[size]="form.value.status.length ? form.value.status.length : 13"
/>
```

```ts
@Component({
  selector: 'lib-ctbc-aml-branch-office-view',
  templateUrl: './ctbc-aml-branch-office-view.component.html',
  styleUrls: ['./ctbc-aml-branch-office-view.component.scss'],
  providers: [DropdownTranslatePipe]
})
```

```ts
record.customerCapType = this.dropdownTranslatePipe.transform(record.customerCapType, this.cappingMethodOptions);
```

```ts
import { AsyncPipe } from '@angular/common';
import { Pipe, PipeTransform } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import { Observable } from 'rxjs'

@Pipe({
  name: 'dropdownTranslate'
})
export class DropdownTranslatePipe implements PipeTransform {
  constructor(protected asyncPipe: AsyncPipe, protected translateService: TranslateService){}
  transform(key: string | boolean, map: Map<any, any>): unknown {
    let output: any = null
    if((typeof key === 'string' && key && key != " - ") || (typeof key === 'boolean' && key !== null && key !== undefined)){
      let translation$: Observable<any> = this.translateService.get(map.get(key))
      output = this.asyncPipe.transform(translation$);
    }
    if(key === " - ") {
      output = key;
    }
    return output
  }
}
```

# Country

constructor

```js
this.countryService.queryByGql([]).subscribe();
```

```ts
let countryCodes = Array.from(this.countryService.recordMap.values());
//or
const out = this.countryService.recordIdMap.get(record.ownerCountry);
```

# FormControl

```ts
graceDate: new FormControl({ value: "", disabled: true }, CustomValidators.required),
```

```ts
cardScheme: [{value: '', disabled: this.isView || this.isEdit}, Validators.required],
```

```ts
 this.form.disable();
```

# <span style="color:red;">`rir:ArrowRight`</span> JPA hibernate Error

```yaml
logging:
  level:
    root: trace
    '[org.hibernate.SQL]': TRACE
    '[org.hibernate.type.descriptor.sql.BasicBinder]': TRACE
    '[com.ibm.cbmp.fabric.foundation]': DEBUG
    '[com.ibm.cbmp.fabric.foundation.aspect]': TRACE
    '[com.worldline.ctbc.extras.abo]': DEBUG
    '[ org.springframework.orm.jpa]': TRACE <--- add this
    org.springframework.transaction: TRACE <--- add this
```

```
#    org.springframework.cloud.gateway: INFO
#    reactor.netty: info
#    root: trace
#    '[org.hibernate.SQL]': TRACE
#    '[org.hibernate.type.descriptor.sql.BasicBinder]': TRACE
#    '[com.ibm.cbmp.fabric.foundation]': DEBUG
#    '[com.ibm.cbmp.fabric.foundation.aspect]': TRACE
#    '[com.worldline.ctbc.extras.abo]': DEBUG
#    '[ org.springframework.orm.jpa]': TRACE
#    org.springframework.transaction: TRACE
```

# dirty properties

```text
2024-02-25 00:26:43.795 [TRACE] [http-nio-8381-exec-1]
org.hibernate.event.internal.DefaultFlushEntityEventListener.logDirtyProperties:703 -
Found dirty properties [[com.worldline.ctbc.extras.abo.entity.CompanyBranch#a0744af8-66e7-4c83-9773-5b98bccc3a74]] :
[lastModifiedDate, registerNameEng, registryName]
```

<span style="color:red;">`rir:ArrowRight`</span> Add value of version column to the table

# GUI

![[Pasted image 20240311102108.png]]

# Save data

> should using jpa to save data, because system will save record the XXXX_AUD table also.

# Transaction roll back error message during the processing of itemWriter of Spring batch.

<span style="color:red;">`fas:ExclamationCircle`</span> @Transactional(propagation = Propagation.REQUIRES_NEW)



root
L@dy9a9a
