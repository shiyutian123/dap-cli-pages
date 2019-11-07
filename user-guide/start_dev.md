## 开始开发
***

### 开发页面

在 `src/plugins/[pluginName]/`目录中创建Angular模块，具体可参考`src/plugins/plan-demo`

!> 注意：需要运行的代码必须写在`src/plugin/[pluginName]/`, 其他目录内修改的代码在云打包时会被丢弃

#### 模块定义

> `SharedModule` 必须引入, 其他地方和Angular7开发方式一致即可

```TS
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { SharedModule } from 'src/app/shared.module';
import { PlanSetComponent } from './plan-demo.component';
import { PlanSetRoutingModule } from './plan-demo-routing.module';
import { PlanSetService } from './plan-demo.service';

@NgModule({
    declarations: [PlanSetComponent],
    imports: [
      SharedModule, // SharedModule 必须导入
      CommonModule,
      PlanSetRoutingModule,
    ],
    providers: [PlanSetService],

  })
export class PlanSetModule { }
```

#### 组件开发

> `@Component`中`selector`必须以 app-plugin- 为前缀, 其他和Angular7开发方式一致，并可以使用`ng-zorro`全部的服务和组件

`plan-demo.component.ts`
```TS
/*
 * @Author: Devin Shi
 * @Email: yutian.shi@definesys.com
 * @Date: 2019-11-05 11:42:51
 * @LastEditTime: 2019-11-06 21:41:12
 * @LastEditors: Devin Shi
 * @Description:
 */
import { Component, OnInit } from '@angular/core';
import { PlanSetService } from './plan-demo.service';
import { NzNotificationService} from 'ng-zorro-antd';
import { DdmpAuthService } from 'src/app/auth/ddmp-auth.service';

@Component({
    selector: 'app-plugin-plan-demo', // 此处必须以 app-plugin- 为前缀
    templateUrl: './plan-demo.component.html',
    styleUrls: ['./plan-demo.component.less']
})
export class PlanSetComponent implements OnInit {
    pageTitle: any;
    pageType: String = 'planSet';
    isEdit: Boolean = false; // 是否是编辑状态
    year: number;
    repeatValue: String = ''; // 重复次数
    classesValue: '';
    workItemValue: '';
    teamsValue: '';
    userInfo: any = {
        base: '',
        factory: '',
        userName: '',
        workShop: ''
    };
    nowDate: any; // 今天的日期
    num: any = 1000; // 序号之前的数据不改变
    planTableData: any[] = []; // 计划表格数据
    monDateNum: any[] = []; // 一年中周一的日期
    times: any;
    searchData: any = {
        year: '',
        name: '',
        base: ''
    };
    isVisible: Boolean = false;
    keyWord: '';
    pageAll: any = {
        pageSize: 10,
        page: 1,
        pageSizeOptions: [10, 20, 30, 40, 50],
        pageTotal: 10
    };
    workcrsList: any = [];
    modelIndex: any;

    currentUser;
    currentToken;

    constructor(
        private ddmpAuthService: DdmpAuthService,
        private planSetService: PlanSetService,
        private nzNotificationService: NzNotificationService) {

      this.currentUser = this.ddmpAuthService.currentUser ;
      this.currentToken = this.ddmpAuthService.currentToken ;
      this.planSetService.getPlanDemo().subscribe((res) => {
        if (res.code === 'ok') {
          this.planTableData = res.table ;
        }
      });

    }
    ngOnInit() {
    }
}

```

`plan-demo.component.html`
```html
<ng-container>
  <div class="plansSet">
    <div class="back">
        <div>
            <!-- <div class="page-tag title">{{pageTitle}}列表</div> -->
            <div class="">当前用户是：{{currentUser && currentUser.name}}</div>
            <div class="">当前token是：{{currentToken && currentToken.accessToken}}</div>
          </div>
    </div>
    <div class="showTable">
        <nz-table class="table" [nzSize]="'small'" #basicTable nzBordered [nzData]="planTableData"
            [nzShowPagination]="false" [nzScroll]="{ y: 'calc(100vh - 450px)' }">
            <thead>
                <tr>
                    <th nzWidth="4%" style="min-width: 60px;">#</th>
                    <th nzWidth="11%">日期</th>
                    <th nzWidth="12%">班次</th>
                    <th nzWidth="12%">工段</th>
                    <th nzWidth="12%">班组</th>
                    <th nzWidth="12%">计划工位</th>
                    <th nzwidth="11%">实际工位</th>
                    <th nzWidth="11%">实际完成日期</th>
                    <th nzWidth="11%">执行状态</th>
                </tr>
            </thead>
            <tbody>
                <tr *ngFor="let planItem of planTableData;let i = index;">
                    <td>{{i + 1}}</td>
                    <td>{{planItem.plan_date}}</td>
                    <td>
                        <ng-container *ngIf="planItem.exp">
                            <!-- <ng-container [ngStyle]="{'display' : planItem.exp ? 'block' : 'none'}"> -->
                            {{planItem.shift_name}}
                        </ng-container>
                        <ng-container *ngIf="!planItem.exp">
                            <!-- <ng-container [ngStyle]="{'display' : !planItem.exp ? 'block' : 'none'}"> -->
                            <ng-container *ngIf="!isEdit">
                                <!-- <ng-container [ngStyle]="{'display' : !isEdit ? 'block' : 'none'}">     -->
                                {{planItem.shift_name}}
                            </ng-container>
                            <!-- <nz-select [ngStyle]="{'display' : isEdit ? 'block' : 'none'}" nzShowSearch [nzDisabled]="!isEdit  || planItem.exp" style="width: 120px;" [(ngModel)]="planItem.shift"> -->
                            <nz-select *ngIf="isEdit" nzShowSearch nzAllowClear [nzDisabled]="!isEdit  || planItem.exp"
                                style="width: 120px;" [(ngModel)]="planItem.shift">
                                <ng-container *ngFor="let item of userInfo.workClasses">
                                    <nz-option [nzValue]="item.code" [nzLabel]="item.meaning"></nz-option>
                                </ng-container>
                            </nz-select>
                        </ng-container>
                    </td>
                    <td>
                        <ng-container *ngIf="planItem.exp">
                            {{planItem.workshop_section_name}}
                        </ng-container>
                        <ng-container *ngIf="!planItem.exp">
                            <ng-container *ngIf="!isEdit">
                                {{planItem.workshop_section_name}}
                            </ng-container>
                            <nz-select *ngIf="isEdit" nzShowSearch nzAllowClear [nzDisabled]="!isEdit  || planItem.exp"
                                style="width: 120px;" [(ngModel)]="planItem.workshop_section">
                                <ng-container *ngFor="let item of userInfo.workSections">
                                    <nz-option [nzValue]="item.code" [nzLabel]="item.meaning"></nz-option>
                                </ng-container>
                            </nz-select>
                        </ng-container>
                    </td>
                    <td>
                        <ng-container *ngIf="planItem.exp">
                            {{planItem.team_name}}
                        </ng-container>
                        <ng-container *ngIf="!planItem.exp">
                            <ng-container *ngIf="!isEdit">
                                {{planItem.team_name}}
                            </ng-container>
                            <nz-select *ngIf="isEdit" nzShowSearch nzAllowClear [nzDisabled]="!isEdit  || planItem.exp"
                                style="width: 120px;" [(ngModel)]="planItem.team">
                                <ng-container *ngFor="let item of userInfo.classGroups">
                                    <nz-option [nzValue]="item.code" [nzLabel]="item.meaning"></nz-option>
                                </ng-container>
                            </nz-select>
                        </ng-container>
                    </td>
                    <td>
                        <ng-container *ngIf="planItem.exp">
                            {{planItem.station}}
                        </ng-container>
                        <ng-container *ngIf="!planItem.exp">
                            <ng-container *ngIf="!isEdit">
                                {{planItem.station}}
                            </ng-container>
                            <!-- <nz-select *ngIf="false && isEdit" nzShowSearch [nzDisabled]="!isEdit || planItem.exp" style="width: 120px;" [(ngModel)]="planItem.station">
                                    <ng-container *ngFor="let item of userInfo.workcrs">
                                            <nz-option [nzValue]="item.WORKCR_CODE" [nzLabel]="item.WORKCR_CODE"></nz-option>
                                        </ng-container>
                                </nz-select> -->

                            <nz-input-group *ngIf="isEdit" (click)="showModel(i)" style="width: 120px;"
                                [nzSuffix]="suffixIconSearch">
                                <input [disabled]="!isEdit || planItem.exp" type="text" nz-input placeholder="请选择工位"
                                    [(ngModel)]="planItem.station" />
                            </nz-input-group>
                            <ng-template #suffixIconSearch>
                                <i nz-icon type="search"></i>
                            </ng-template>
                        </ng-container>
                    </td>
                    <td>{{planItem.real_station}}</td>
                    <td>{{planItem.finish_date}}</td>
                </tr>
            </tbody>
        </nz-table>
    </div>
  </div>
</ng-container>

<ng-template #suffixIconButton>
  <button nz-button nzType="primary" nzSearch (click)="query(keyWord)"><i nz-icon type="search"></i></button>
</ng-template>
<ng-template #totalPage>
  共{{pageAll.pageTotal}}条
</ng-template>
```

`./plan-demo.component.html`
```less
.showItem {
  background: #fff;
  height: 80px;
  margin-bottom: 10px;
  div {
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      font-weight:bolder;
      padding: 0 10px;
      span {
          display: inline;
          text-align: center;
          width: 100%;
          overflow: hidden;
          white-space: nowrap;
          text-overflow: ellipsis;
      }
  }
}
.planSet {
  background: #fff;
  height: 100px;
  margin-bottom: 10px;
  > div {
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      .showYear,.set {
          font-weight:bolder;
          font-size: 28px;
      }
      .set {
          cursor: pointer;
          display: flex;
          flex-direction: column;
          > i {
              color: rgba(23, 121, 254, 1);
          }
          > i:active {
             color: rgba(23, 121, 254, 0.4);
          }
      }
  }
  .setYear {
      justify-content: space-around;
  }
  .allSet {
      >div {
          display: flex;
          justify-content: center;
          align-items: center;
      }
  }
}
.showTable {
  background: #fff;
  // max-height: calc(100vh - 300px);
  margin-bottom: 10px;
  box-sizing: border-box;
  padding: 5px;
  th {
      background: rgb(233, 235, 239) !important;
  }
}
.table {
  th, td {
      text-align: center;
      box-sizing: border-box;
      width: calc((100% - 680px) / 4);
  }
  td {
      // padding-top: 0 !important;
      // padding-bottom: 0 !important;
      margin: 0 auto;
      background: #fff;
  }
  tr:nth-of-type(2n) {
      td {
          background: rgb(249, 249, 249);
      }
  }
}
.separator {
  margin-left: 10px;
}
.active {
  color: #1890ff
}
.title {
  cursor: pointer;
}
.search{
  background: #fff;
  height: 80px;
  margin-bottom: 10px;
  >div {
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      span {
          min-width: 50px;
          text-align: end;
          font-weight: bolder;
      }
      input {
          max-width: 150px;
          min-width: 120px;
      }
  }
}

:host ::ng-deep .ant-table-body{
  margin: 0 !important;
}
.plansSet {
  padding: 0 10px;
}
.back {
  background: #eeeded;
  margin: 10px 4px;
  border:1px solid rgb(226,228,228);
  height: auto ;
}

.ant-input-group-addon{
  padding: 0 !important;
}
.ant-input-search {
  margin-bottom: 10px;
}

.modalTable {
   tr {
      th {
          background: #e9ebef !important;
          text-align: center;
      }
      td {
          text-align: center;
      }
      &:nth-of-type(2n) {
          background: #f9f9f9;
      }
  }
  :host ::ng-deep .ant-table-body {
      margin: 0 !important;
  }
}
tr.tr-single-selected {
  background-color: #1890ff !important;
  color: #fff;

  a {
      color: #fff;
  }
}
tr.tr-single-selected:hover {
  td {
      background-color: #1890ff !important;
      color: rgba(0, 0, 0, 0.65);

      a {
          color: #1890ff;
      }
  }
}
```

#### 服务开发

> 服务和组件中可以引入2个全局服务`NetworkService`, `DdmpAuthService`

```ts
import { Injectable } from '@angular/core';
import { NetworkService } from 'src/app/network/network.service';
import { DdmpAuthService } from 'src/app/auth/ddmp-auth.service';

@Injectable()
export class PlanSetService {
  constructor(
    private network: NetworkService,
    private ddmpAuthService: DdmpAuthService,
  ) {
  }

  getPlanDemo() {
    return this.network.get('http://k8s.definesys.com:30577/process/authProxy/getAuthorizations');
  }
}

```

#### 路由模块

> 和 Angular7 开发方式完全一致

`plan-demo-routing.module.ts`
```ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { PlanSetComponent } from './plan-demo.component';

const routes: Routes = [
  {
    path: '',
    component: PlanSetComponent
  }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class PlanSetRoutingModule { }
```

#### 模块引入

> 在`src/plugins/[pluginName]/index.ts` 中引入模块即可。如下

```TS
import { PlanSetModule } from './plan-demo/plan-demo.module';

const CITEMODULE = [PlanSetModule, /* 多余模块在此添加即可 */];
```

#### 路由配置

> 路由需要配置在`.dap-cli.json` 中配置, 将配置配置在`devRoutes`的数组中。

!> 1. 必须采用懒加载的方式

!> 2. `loadChildren`的路径必须从`src`开始的路径

!> 3. 开发时的路由名称，需要定义在 `data.name` 中，会显示在菜单栏


```
{
  "version": "0.0.1",
  "name": "hello",
  "package": {
    "src": {
      "code": "./hello/",
      "assets": "./hello/"
    }
  },
  "dist": {
    "outputPath": "./dist/",
    "outputName": "view.zip"
  },
  "deps": [
  ],
  "devRoutes": [
    {
      "path": "app-plugin-plan-demo",
      "loadChildren": "src/plugins/hello/plan-demo/plan-demo.module#PlanSetModule",
      "data": {
        "name": "计划Demo"
      }
    }
  ],
  "devEnv": {
    "needSSO": false,
    "baseDomain": "http://k8s.definesys.com:30557",
    "ssoDomain": "http://k8s.definesys.com:30008",
    "appInfo": {
      "appId" : "fad5aa1e-335e-45d5-8ade-ba02f02d0c94",
      "appSecre" : "e13f61fe-6b3a-4fd1-8ff3-89b8b8e8fbc9",
      "appCode" : "plan-demo",
      "sidebarEnable": false,
      "title": "DAP脚手架"
    },
    "clientId": "dapAppClient"
  }
}
```

### 资源使用
> `HTML`文件中需要引入的静态资源(*一般是图片*)，需要放在 `assets/plugins/[pluginName]/`目录中，并且引用格式如下
```html
 <img src="./assets/plugins/hello/150x150.png"/>
```

### 测试运行
配置好之后直接执行即可

```shell
npm run start
```
