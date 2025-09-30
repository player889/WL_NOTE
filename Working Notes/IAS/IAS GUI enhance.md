```ts
onOrgChange() {
	this.form.get('itnIdnCde').valueChanges.pipe(
	  distinctUntilChanged(),
	  switchMap((itnIdnCdeValue: any) => {
		if (itnIdnCdeValue) {
		  const bussCaseDefAPI = this.businessCaseDefService.iasListByGraphql([
			{ type: "single", op: "eq", name: "itnIdnCde", value: itnIdnCdeValue.itnIdnCde },
			{ type: "single", op: "eq", name: "atvInd", value: '1' }
		  ]);
		  const velocityChecKDefAPI = this.velocityCheckDefinitionService.iasListByGraphql([
			{ type: "single", op: "eq", name: "itnIdnCde", value: itnIdnCdeValue.itnIdnCde }
		  ]);
		  return forkJoin([bussCaseDefAPI, velocityChecKDefAPI]).pipe(
			catchError(error => {
			  console.error('Error:', error);
			  return of([[], []]);
			})
		  );
		} else {
		  return forkJoin([of([]), of([])])
		}
	  })
	).subscribe(
	  ([bussCaseDefList, velocityChecKDefList]) => {
		this.businessCaseCodeList = bussCaseDefList.map((element: any) =>
		  ({ "value": element.busCseCde, "text": element.busCseCde + ' - ' + element.desTxt })
		);
		this.velocityCheckDefinitionIdList = velocityChecKDefList.map((element: any) =>
		  ({ "value": element.vtyDefIdnCde, "text": element.vtyDefIdnCde + ' - ' + element.vtyChkDesTxt })
		);
	  },
	  error => {
		console.error('Error:', error);
	  }
	);
}

```

# Autocomplete

```ts

getCustomAutocompleteDropdownList(input: any, criterias: any[], recordMap: Map<string, any>,
    recordFirstKeyMap: Map<string, any>, customDisplayKey: any, displayKeyVariables: string[]) {
    console.log('getCustomAutocompleteDropdownList')
    this.customDisplayKey = false;
    this.propertyFilterKey = this.filterKey;
    if (customDisplayKey) {
      this.customDisplayKey = customDisplayKey;
    }
    if (displayKeyVariables.length > 0) {
      this.displayKeyVariables = displayKeyVariables;
      this.sortingProperty = displayKeyVariables[0];
      if (displayKeyVariables.length > 1) {
        this.propertyFilterKey = [displayKeyVariables[0], this.displayKeyVariables[1]];
      } else {
        this.propertyFilterKey = [displayKeyVariables[0]];
      }
    }
    console.log('criterias  ==> ')
    console.log(criterias)
    const queryObjectJSON = this.getIASJSONQueryObject(input, criterias, recordMap, recordFirstKeyMap);
    return this.pageByGraphql(queryObjectJSON).pipe(mergeMap((x: any) => this.iasGetOptionDisplayKey(x)));
  }
  getIASJSONQueryObject(value: any, criterias: any[], recordMap: Map<string, any>,
    recordFirstKeyMap: Map<string, any>, customDisplayKey?: boolean, customSort?: string, customFilterKey?: any[]): any {
    console.log("VVVVVVVVVVV")
    let sort = [];
    if (customDisplayKey != null && customSort && customFilterKey) {
      this.customDisplayKey = customDisplayKey;
      this.sortingProperty = customSort;
      this.propertyFilterKey = customFilterKey;
    }
    if (this.sortingProperty) {
      sort = [{ "name": this.sortingProperty, "asc": true }];
    }
    if (typeof value === 'object') {
      value = this.getDisplayKeyMethod(value);
    }
    if (this.propertyFilterKey.length > 1) {
      let inputs: string[] = [];
      if (value) {
        inputs = this.getInputArray(value, recordMap, recordFirstKeyMap, inputs);
      }
      if (value && value.indexOf("-") > 0 && inputs.length > 1) {
        if (this.propertyFilterKey[0]) {
          criterias.push({ type: "single", op: "eq", name: this.propertyFilterKey[0], value: inputs[0] });
          if (this.propertyFilterKey[1]) {
            criterias.push({ type: "single", op: "like", name: this.propertyFilterKey[1], value: inputs[1] });
          }
        }
      }
    } else {
      if (this.propertyFilterKey[0] && value) {
        criterias.push({ type: "single", op: "like", name: this.propertyFilterKey[0], value: value });
      }
    }
    return JSONQueryUtils.generateIASQueryObjectJSON(criterias, 1, this.iasAutocompleteReturnSize, sort);
  }
  customListByCriterias(criterias: any[], multipleCustomAutoCompleteMethod?: string) {
    console.log('criterias')
    console.log(criterias)
    return this.pageByGraphql(JSONQueryUtils.generateIASQueryObjectJSON(criterias, 1, 100, []));
  }
  getCustomDisplayKey(record: any, displayKeyVariables?: string[]): string {
    console.log('getCustomDisplayKey')
    if (displayKeyVariables.length > 0) {
      if (displayKeyVariables.length > 1) {
        return record ? record[displayKeyVariables[0]] + ' - ' + record[displayKeyVariables[1]] : "";
      }
      else {
        return record ? record[displayKeyVariables[0]] : "";
      }
    }
    else {
      this.getDisplayKey(record);
    }
  }

```

# Autocomplete with parent inputs value

````ad-note

```ad-tip
title:html
~~~html
<optimus-autocomplete-input inputLabel="{{ 'TRANSACTION_PROCESS_CONTROL.INPUT_FORM.ORGANIZATION_ID' | translate }}"
[service]="institutionService" formControlName="itnIdnCde" [required]="true" [queryByPage]="true"
[customDisplayKey]="true" [displayKeyVariables]="['nmeTxt']" (inputChanged)="checkUnique()"
[customErrors]="_form.controls.itnIdnCde.errors"
>
<span *ngIf="_form.controls.itnIdnCde.errors?.unique">
	{{ 'TRANSACTION_PROCESS_CONTROL.ERROR_MESSAGE.UNIQUE_TRANSACTION_PROCESS_CONTROL' | translate }}
</span>
</optimus-autocomplete-input>
~~~

```



```ad-info
title:html
~~~html
<mat-form-field class="input-lg" appearance="outline">
	<mat-label>{{ 'TRANSACTION_PROCESS_CONTROL.INPUT_FORM.AUTHORISATION_SEQUENCE_CODE' | translate }}</mat-label>
	<input matInput placeholder="Auth Seq Code" formControlName="athSeqIdnCde" [matAutocomplete]="auto" #athSeqIdnCdeInput (keydown)="onkeydownRemoveAutocomplet($event, form.get('athSeqIdnCde'))" (cut)="false">
	<button mat-button *ngIf="form.get('athSeqIdnCde').value" matSuffix mat-icon-button aria-label="Clear" (click)="onRemoveAutocomplete(form.get('athSeqIdnCde'), athSeqIdnCdeInput, true)">
		<mat-icon>close</mat-icon>
	</button>
	<img *ngIf="!form.get('athSeqIdnCde').value" matSuffix mat-icon-button src="assets/images/Dropdown-Arrow-2.svg">
	<!-- [displayWith]="displayAutocomplete"  -->
	<mat-autocomplete autoActiveFirstOption #auto="matAutocomplete" [displayWith]="displayAutocomplete" (optionSelected)='athSeqIdnCdeInput.blur()'>
		<mat-option *ngFor="let option of athSeqIdnCde$ | async" [value]="option">
			{{option.displayKey}}
		</mat-option>
	</mat-autocomplete>
	<mat-error *ngIf="_form.controls.athSeqIdnCde.errors?.invalidAutoComplete">
		{{'COMMON.ERROR_MESSAGE.AUTOCOMPLETE' | translate}}
	</mat-error>
</mat-form-field>
~~~
```

```ad-info
title:component
~~~ts
this.form.get("itnIdnCde").valueChanges.pipe(
  distinctUntilChanged(),
  tap(x => {
	this.form.get('athSeqIdnCde').setValue('');
	this.athSeqIdnCde$ = this.form.get('athSeqIdnCde').valueChanges.pipe(
	  startWith(''),
	  debounceTime(400),
	  distinctUntilChanged(),
	  switchMap(val =>
		this.authSequenceCodeService.getAutocompleteOptionList(x, val || '')
	  )
	)
  })).subscribe();
~~~
```



```ad-info
title: service.ts
~~~ts
  getAutocompleteOptionList(constantCriteriaObj: any, criteriaObj: any) {
    const constantCriteriaVal = this.getAutoCompleteValue(constantCriteriaObj, 'itnIdnCde');
    const criertiaVal = this.getAutoCompleteValue(criteriaObj, 'athSeqCde');
    const queryObjectJSON = this.getQueryObjectJSON(constantCriteriaVal, criertiaVal);
    return this.pageByGraphql(queryObjectJSON).pipe(mergeMap((x: any) => this.iasGetOptionDisplayKey(x)));
  }

  private getQueryObjectJSON(constantCriteriaVal: string, criertiaVal: string,): any {
    const sort = [{ "name": this.sortingProperty, "asc": true }];
    let criterias: any[] = [];
    criterias.push({ type: "single", op: "eq", name: 'itnIdnCde', value: constantCriteriaVal });
    if ('' != criertiaVal) {
      criterias.push({ type: "single", op: "like", name: 'athSeqCde', value: criertiaVal });
    }
    console.log(criterias)
    return JSONQueryUtils.generateIASQueryObjectJSON(criterias, 1, this.iasAutocompleteReturnSize, sort);
  }

  private getAutoCompleteValue(input: any, key: string): string {
    return 'object' === typeof input ? (input[key]).toLowerCase() : input.toLowerCase();
  }


  displayAutocomplete(option: any): string {
    if('object' ===  typeof option && null != option){
      return ('displayKey' in option) ? option.displayKey : this.getDisplayKey(option);
    }
    return '';
  }
~~~

```




````