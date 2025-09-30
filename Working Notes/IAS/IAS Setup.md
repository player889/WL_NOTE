    # Setup IAS
    Maven version 3.8.2

1. Download [SourceCode](https://gitlab.kazan.myworldline.com/apac-e2e-optimus/optimus-ias-v1.2)
2. Setup Oracle Databaes (Local or Docker)
3. Run Modules: CAS-app, CMS-app, IAS-app, cardlite-gui
4. Changes Database connection to Oracle (Add dependency) include the db.properties
    ```yaml
    spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
    worldline.default.db-dialect=oracle
    spring.datasource.driver-class-name=oracle.jdbc.driver.OracleDriver
    spring.datasource.url=jdbc:oracle:thin:@//localhost:1521/IASLOCAL
    spring.datasource.username=ias
    spring.datasource.password=ias
    ```
5. Execute the api-libs mvn file
6. `mvn compile` from root pom.xml
7. Run Application [`fas:ExclamationCircle`How to configure "Shorten command line" method for whole project in IntelliJ](https://stackoverflow.com/questions/47926382/how-to-configure-shorten-command-line-method-for-whole-project-in-intellij)
    1. CASServiceApplication `rir:CheckboxCircle`
        1. Caused by: java.net.UnknownHostException: xxl-node1-server.ias.worldline.com `ris:ErrorWarning`
    2. CardliteGuiApplication `rir:CheckboxCircle`
    3. IASServiceApplication `rir:CheckboxCircle`
    4. CMSServiceApplication `rir:CheckboxCircle`
        1. Caused by: java.net.UnknownHostException: xxl-node1-server.ias.worldline.com `ris:ErrorWarning`
    5. `rir:CheckDouble` Ignore xxl-job message
8. GUI - `npm run serve`
9. Login `cardlite-gui`
    1. poc01/123456
10. IAS - myBatis generator  
    ![[2023-06-06_114812.png|900]]
11. `spring.kafka.bootstrap-servers=127.0.0.1:9092`

### Start cc module

![[Pasted image 20230601164230.png]]

### Error msg during the startup of the IAS service

```txt
Consider defining a bean of type 'com.hazelcast.core.HazelcastInstance' in your configuration.

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'cryptoKeyCacheService': Unsatisfied dependency expressed through field 'hazelCastInstance'; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'com.hazelcast.core.HazelcastInstance' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}

```

add following config in application.properties ias-app

```txt
spring.autoconfigure.exclude=com.worldline.optimus.plat.core.config.FeignAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.actuate.autoconfigure.security.servlet.ManagementWebSecurityAutoConfiguration,\
com.worldline.optimus.plat.crypto.config.CryptoAutoConfiguration
```

Result

```txt
spring.autoconfigure.exclude=com.worldline.optimus.plat.core.config.FeignAutoConfiguration,\
org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
org.springframework.boot.actuate.autoconfigure.security.servlet.ManagementWebSecurityAutoConfiguration,\
com.worldline.optimus.plat.crypto.config.CryptoAutoConfiguration,\
com.worldline.optimus.plat.core.config.KafkaAutoConfiguration,\
com.worldline.optimus.plat.jpa.config.JpaAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration
```

# Data Sync

> When the data sync is 1st time running, there isn't any `AccountCreditData` until your `CreditAdjustSetupJob` is being triggered

# X-Token

![[Pasted image 20230821175031.png]]
#X-Token

# IAS System Date

update cps_system set SYS_DTE = '2023-06-16'  
update trigger_table set AVN_DTE = DATE '2023-06-17' where TGR_IDN_NBR = '310'

# Run prod build

```node
npm run build:prod
```

# Run prod build with specific module

```shell
npm run build:prod -- --scope=@optimus/authorisation
```

# Install with specific module

```
npm install @optimus/authorisation
```

# cache clear

```shell
npm cache verify
```

https://nx.dev/recipes/troubleshooting/resolve-circular-dependencies

# FeignProviderConfig - conflicts with existing

```shell
mvn clean
mvn install -DskipTests -pl '!liquibase-app'
```

# i18n

Prevent the cache when adding / modify new i18n file.
![[Pasted image 20231130113643.png]]

# IAS code generator

## Create Table

![[Pasted image 20231219111634.png]]

1. Add new SQL script
2. Comment other already use SQL script
3. Run service

# 1.2.1 plat version



# IDM

env.js

```
// Default values
// window.__env.authRealm = "optimus" || {};
window.__env.authRealm = "optimus_dev" || {}; // for dev
window.__env.authClientId = "optimus" || {};
// window.__env.authClientSecret = "610bd67b-5bf7-41fd-b81c-57700a5b21fe" || {};
window.__env.authClientSecret = "jAPYjZrfi35rGUxyNpkdBoxP9ItqMd60" || {}; // for dev
```

```sql
INSERT INTO USERS
(ID, USER_NAME, FIRST_NAME, LAST_NAME, LOCAL_NAME, EMAIL, MOBILE_NUMBER, MOBILE_NUMBER_ISO, OFFICE_PHONE_NUMBER, OFFICE_PHONE_NUMBER_ISO, IDM_USER_ID, PROFILE_PHOTO, IS_SYSTEM, DELETED_DATE, CREATED_DATE, CREATED_BY, LAST_MODIFIED_DATE, LAST_MODIFIED_BY, VERSION, LAST_LOGON_TIME)
VALUES('369A26DA96C049FCBACBBACCF804E249', 'jay', 'jay', 'hsiao', NULL, NULL, NULL, NULL, NULL, NULL, '0bbbbde0-049e-49e9-932b-b4351b51086b', NULL, 0, NULL, TIMESTAMP '2024-05-15 10:13:03.132000', NULL, TIMESTAMP '2024-05-15 10:15:44.250000', 'foo', 1, TIMESTAMP '2024-05-15 09:40:59.816000');
```

```sql
INSERT INTO USER_ROLES (ID, USER_ID, ROLE_ID, DELETED_DATE, CREATED_DATE, CREATED_BY, LAST_MODIFIED_DATE, LAST_MODIFIED_BY, VERSION)
VALUES(sys_guid(), '369A26DA96C049FCBACBBACCF804E249', 'A3CA036B14F7411BBAC6229B53CC4C48', NULL, SYSTIMESTAMP, 'system', SYSTIMESTAMP, 'system', 0);
```

![[Pasted image 20240823143148.png]]

```ad-note
title: optimus-routing.module.ts
collapse: close
~~~
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { AuthGuard } from '@optimus/core';
import { OptimusComponent } from './optimus.component';
const routes: Routes = [
  {
    path: "",
    component: OptimusComponent,
    canActivate: [AuthGuard],
    // canActivateChild: [AuthGuard],
    children: [
      {
        path: "",
        redirectTo: "dashboard",
        pathMatch: "full"
      },
      {
        path: "dashboard",
        loadChildren: () => import('../@lib-modules/dashboard/dashboard.module').then(m => m.DashboardModule),
        data: {
          breadcrumb: "DASHBOARD.BREADCRUMB.DASHBOARD.SECTION_LABEL"
        }
      },
      {
        path: "common",
        loadChildren: () => import('../@lib-modules/common/gm.module').then(m => m.GmModule),
        data: {
          breadcrumb: "CC.BREADCRUMB.GENERAL_MANAGEMENT.SECTION_LABEL"
        }
      },
      // {
      //   path: "bwm",
      //   loadChildren: () => import('../@lib-modules/bwm/bwm.module').then(m => m.BwmModule),
      //   data: {
      //     breadcrumb: "BWM.BREADCRUMB.BUSINESS_WORKFLOW_MANAGEMENT.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "ibo",
      //   loadChildren: () => import('../@lib-modules/ibo/ibo.module').then(m => m.IboModule),
      //   data: {
      //     breadcrumb: "IBO.BREADCRUMB.ISSUER_OFFICE.SECTION_LABEL"
      //   }
      // },
      {
        path: "idm",
        loadChildren: () => import('../@lib-modules/idm/idm.module').then(m => m.IdmModule),
        data: {
          breadcrumb: "IDM.BREADCRUMB.ADMIN.SECTION_LABEL"
        }
      },
      // {
      //   path: "rta",
      //   loadChildren: () => import('../@lib-modules/rta/rta.module').then(m => m.RtaModule),
      //   data: {
      //     breadcrumb: "RTA.BREADCRUMB.RECURRING_TRANSACTION_ACQUIRING.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "abo",
      //   loadChildren: () => import('../@lib-modules/abo/abo.module').then(m => m.AboModule),
      //   data: {
      //     breadcrumb: "Acquiring Office"
      //   }
      // },
      // {
      //   path: "srs",
      //   loadChildren: () => import('../@lib-modules/srs/srs.module').then(m => m.SrsModule),
      //   data: {
      //     breadcrumb: "SRS.BREADCRUMB.SWITCH_OFFICE.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "epp",
      //   loadChildren: () => import('../@lib-modules/epp/epp.module').then(m => m.EppModule),
      //   data: {
      //     breadcrumb: "EPP.BREADCRUMB.INSTALMENT.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "mache",
      //   loadChildren: () => import('../@lib-modules/mache/mache.module').then(m => m.MacheModule),
      //   data: {
      //     breadcrumb: "MACHE.BREADCRUMB.MAKER_CHECKER.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "dms",
      //   loadChildren: () => import('../@lib-modules/dms/dms.module').then(m => m.DmsModule),
      //   data: {
      //     breadcrumb: "DMS.BREADCRUMB.DISPUTE_MANAGEMENT.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "business-report",
      //   loadChildren: () => import('../@lib-modules/business-report/business-report.module').then(m => m.BusinessReportModule),
      //   data: {
      //     breadcrumb: "Reports"
      //   }
      // },
      // {
      //   path: "ims",
      //   loadChildren: () => import('../@lib-modules/ims/ims.module').then(m => m.ImsModule),
      //   data: {
      //     breadcrumb: "IMS.BREADCRUMB.INTERCHANGE_MANAGEMENT.SECTION_LABEL"
      //   }
      // },
      {
        path: "ias",
        loadChildren: () => import('../@lib-modules/ias/ias.module').then(m => m.IasModule),
        data: {
          breadcrumb: "IAS.BREADCRUMB.SECTION_LABEL"
        }
      },
      // {
      //   path: "lrs",
      //   loadChildren: () => import('../@lib-modules/lrs/lrs.module').then(m => m.LrsModule),
      //   data: {
      //     breadcrumb: "LRS.BREADCRUMB.LOYALTY.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "sdi",
      //   loadChildren: () => import('../@lib-modules/sdi/sdi.module').then(m => m.SdiModule),
      //   data: {
      //     breadcrumb: "SDI.BREADCRUMB.SECURITY.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "operation",
      //   loadChildren: () => import('../@lib-modules/operation/operation.module').then(m => m.OperationModule),
      //   data: {
      //     breadcrumb: "OPERATION.BREADCRUMB.SECTION_LABEL"
      //   }
      // },
      // {
      //   path: "notification",
      //   loadChildren: () => import('../@lib-modules/notification/notification.module').then(m => m.NotificationModule),
      //   data: {
      //     breadcrumb: "NOTIFICATION.BREADCRUMB.NOTIFICATION.SECTION_LABEL"
      //   }
      // }
    ]
  }
];
@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class OptimusRoutingModule { }
~~~

```
