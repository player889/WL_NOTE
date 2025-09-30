```ad-note
title: Generate a ChangeLog From an Existing Database
collapse: true
~~~mvn
mvn liquibase:generateChangeLog
~~~
```

```ad-note
title: Generate a ChangeLog From Diffs Between Two Databases
collapse: true
We can use the plugin to generate a changeLog file from the differences between two existing databases (for example, development and production)
~~~mvn
mvn liquibase:diff
~~~
```

```ad-tip
title: Skip error and continue running script
<changeSet failOnError="false" ...> ... </changeSet>
```


| Mvn Command       | 描述                                            |
| ----------------- | --------------------------------------------- |
| changelogSync     | 將changelog中未套用至db的change logs標示成已同步           |
| changelogSyncSQL  | 同changelogSync，但只產生sql，而不執行同步到db              |
| generateChangeLog | 將目前資料庫的shcema(預設不含資料內容)匯出成 xml                |
| diff              | 比對兩個資料庫間的差異                                   |
| status            | 顯示目前change set有那些change log會被套用到db            |
| tag               | 在liquibase產生在db的管理用table打上tag，之後可以當作rollback用 |
| update            | 更新未套用過的change set至db(xml -> db)               |
| updateSQL         | 同update，但產生更新的sql語法，不會真正同步db                  |

> The Active Log is store in the Database,  if the data exists the changelog will not execute.
> ![[Pasted image 20230606113648.png]]

>Reason: liquibase.exception.DatabaseException: ORA-00922: missing or invalid option
> `fas:ArrowRight`For single SQL does not add  ` ; ` end of file.

Ref.

1. [mvn liqubase offical site](https://docs.liquibase.com/tools-integrations/maven/commands/maven-generatechangelog.html)
2. [管理數據庫變更實踐](https://juejin.cn/post/6844903817956294663)
3. [數據庫的變更同步、回滾](https://blog.csdn.net/sinat_14840559/article/details/127685989?spm=1001.2101.3001.6650.13&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-13-127685989-blog-118019148.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-13-127685989-blog-118019148.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=19)
4. [通過Maven用LiquiBase對數據庫變更進行版本控制](https://www.pkslow.com/archives/liquibase)
5. [Liquibase學習並使用](https://blog.csdn.net/qq294235493/article/details/105070239?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-4-105070239-blog-78124673.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-4-105070239-blog-78124673.pc_relevant_default&utm_relevant_index=5#changelog_17)
