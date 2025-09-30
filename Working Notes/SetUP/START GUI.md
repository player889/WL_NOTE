# Required backend service

1. cc
2. idm
3. ias-app
4. ias-gui

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
