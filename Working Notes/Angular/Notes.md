1. optimus-autocomplete-input (autocomplete-input.component.ts)
2. mat-tab-group
    1. [Ref. 1](https://stackoverflow.com/questions/47469844/angular-cdk-how-to-set-inputs-in-a-componentportal)
    2. [Ref. 2](https://www.jianshu.com/p/dc21f1537879)
3. Mat-doc
    1. @Input() `(selectedIndex)`
    2. @Output() `selectedIndexChange`
    3. [@Inject](https://angular.io/guide/dependency-injection-in-action)
    4. page && "default" -> default value
    5. employee?.rating <=> employee !== null ? employee.rating : null
4. Tab
    1. ![[Pasted image 20230209113350.png|800]]
    2. ![[Pasted image 20230209113735.png|700]]
    3. [Ref. 1](https://www.twblogs.net/a/61aa15cdf3329018fde7af6c)
5. To run pipeline in my local.
   `npm run build:prod`
6. Import service or component from another module.
   Look into `public-api.ts` at service or component level
   ![[Pasted image 20230720103740.png]]

# Missing @optimus/core lib

Run `maven install` at root of project, it takes 10 minutes and run the `npm install` at angular project