---
title: Anaular 中文文档
date: 2024-05-12 14:08:47
tags: 
  - 前端
  - Angular
categories: 
  - 前端
top_img: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Angular%20%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3/preview.jpg
cover: https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Angular%20%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3/preview.jpg
---



# Anaular 中文文档

## 快速上手

### 简介



Angular 是一个基于 TypeScript 构建的开发平台。它包括：

- 一个基于组件的框架，用于构建可伸缩的 Web 应用
- 一组完美集成的库，涵盖各种功能，包括路由、表单管理、客户端-服务器通信等
- 一套开发工具，可帮助你开发、构建、测试和更新代码

借助 Angular，无论单人项目还是企业级应用，你都能获得平台带来的优势。Angular 的设计目标之一就是让更新更容易，因此你可以用最小的成本升级到最新的 Angular 版本。最重要的是，Angular 的生态系统由包括 170 万名开发人员、库作者和内容创作者在内的多元团队构成



------

### 搭建环境



要想在你的本地系统中安装 Angular，需要先安装 Node.js 和 npm，可以通过 https://angular.cn/guide/versions 来查看版本兼容指南



1. 安装 CLI

   ```SH
   npm install -g @angular/cli
   ```

2. 安装完成后，尝试创建一个新的工作区和初始入门应用，在工作目录下输入如下指令

   ```sh
   ng new my-app
   ```

   `ng new` 命令会提示你提供要把哪些特性包含在初始应用中。按 Enter 或 Return 键可以接受默认值

   Angular CLI 会安装必要的 Angular npm 包和其它依赖包。这可能要花几分钟的时间

3. 运行下列命令

   ```SH
   cd my-app
   ng serve --open
   ```

   `ng serve` 命令会启动开发服务器、监视文件，并在这些文件发生更改时重建应用

   `--open`（或者只用 `-o` 缩写）选项会自动打开你的浏览器，并访问 `http://localhost:4200/`

   如果你的安装和环境搭建成功了，就会看到如下页面

   ![image-20231230232454482](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Angular%20%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3/image-20231230232454482.png)



------

## 组件

### 概览



组件是 Angular 应用的主要构造块。每个组件包括如下部分：

- 一个 HTML 模板，用于声明页面要渲染的内容
- 一个用于定义行为的 TypeScript 类
- 一个 CSS 选择器，用于定义组件在模板中的使用方式
- （可选）要应用在模板上的 CSS 样式



创建一个组建的方式有两种，Angular CLI 是用来创建组件的最简途径，也可以手动创建一个组件

* 使用 Angular CLI 创建一个组件

  在终端窗口中，导航到要放置你应用的目录

  运行 `ng generate component <component-name>` 命令，其中 `<component-name>` 是新组件的名字

  默认情况下，该命令会创建以下内容：

  - 一个以该组件命名的文件夹
  - 一个组件文件 `<component-name>.component.ts`
  - 一个模板文件 `<component-name>.component.html`
  - 一个 CSS 文件，`<component-name>.component.css`
  - 测试文件 `<component-name>.component.spec.ts`

* 使用手动方式创建的话就需要自己创建文件夹及文件，并添加文件必需内容



------

### 组件的声明周期



当 Angular 实例化组件类并渲染组件视图及其子视图时，组件实例的生命周期就开始了。生命周期一直伴随着变更检测，Angular  会检查数据绑定属性何时发生变化，并按需更新视图和组件实例。当 Angular 销毁组件实例并从 DOM  中移除它渲染的模板时，生命周期就结束了。当 Angular 在执行过程中创建、更新和销毁实例时，指令就有了类似的生命周期

应用可以使用生命周期钩子方法来触发组件或指令生命周期中的关键事件，以初始化新实例，需要时启动变更检测，在变更检测过程中响应更新，并在删除实例之前进行清理



可以通过实现一个或多个 Angular `core` 库中定义的生命周期钩子接口来响应组件或指令生命周期中的事件。这些钩子让你有机会在适当的时候对组件或指令实例进行操作，比如 Angular 创建、更新或销毁这个实例时

每个接口都有唯一的一个钩子方法，它们的名字是由接口名再加上 `ng` 前缀构成的。比如，`OnInit` 接口的钩子方法叫做 `ngOnInit()`。如果你在组件或指令类中实现了这个方法，Angular 就会在首次检查完组件或指令的输入属性后，紧接着调用它

```TS
@Directive({selector: '[appPeekABoo]'})
export class PeekABooDirective implements OnInit {
  constructor(private logger: LoggerService) { }

  // implement OnInit's `ngOnInit` method
  ngOnInit() {
    this.logIt('OnInit');
  }

  logIt(msg: string) {
    this.logger.log(`#${nextId++} ${msg}`);
  }
}
```

你不必实现所有生命周期钩子，只要实现你需要的那些就可以了



Angular 会按以下顺序执行钩子方法。可以用它来执行以下类型的操作

|        钩子方法         |                             用途                             |                             时机                             |
| :---------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|      ngOnChanges()      | 当 Angular 设置或重新设置数据绑定的输入属性时响应。该方法接受当前和上一属性值的 `SimpleChanges` 对象 | 如果组件绑定过输入属性，那么在 `ngOnInit()` 之前以及所绑定的一个或多个输入属性的值发生变化时都会调用 |
|       ngOnInit()        | 在 Angular 第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件 | 在第一轮 `ngOnChanges()` 完成之后调用，只调用**一次**。而且即使没有调用过 `ngOnChanges()`，也仍然会调用 `ngOnInit()`（比如当模板中没有绑定任何输入属性时） |
|       ngDoCheck()       | 检测，并在发生 Angular 无法或不愿意自己检测的变化时作出反应  | 紧跟在每次执行变更检测时的 `ngOnChanges()` 和 首次执行变更检测时的 `ngOnInit()` 后调用 |
|  ngAfterContentInit()   | 当 Angular 把外部内容投影进组件视图或指令所在的视图之后调用  |          第一次 `ngDoCheck()` 之后调用，只调用一次           |
| ngAfterContentChecked() |    每当 Angular 检查完被投影到组件或指令中的内容之后调用     |     `ngAfterContentInit()` 和每次 `ngDoCheck()` 之后调用     |
|    ngAfterViewInit()    | 当 Angular 初始化完组件视图及其子视图或包含该指令的视图之后调用 |    第一次 `ngAfterContentChecked()` 之后调用，只调用一次     |
|  ngAfterViewChecked()   | 每当 Angular 做完组件视图和子视图或包含该指令的视图的变更检测之后调用 | `ngAfterViewInit()` 和每次 `ngAfterContentChecked()` 之后调用 |
|      ngOnDestroy()      | 每当 Angular 每次销毁指令/组件之前调用并清扫。在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏 |            在 Angular 销毁指令或组件之前立即调用             |



------

### 视图封装



在 Angular 中，组件的样式可以封装在组件的宿主元素中，这样它们就不会影响应用程序的其余部分

`Component` 的装饰器提供了 [`encapsulation`](https://angular.cn/api/core/Component#encapsulation) 选项，可用来控制如何基于每个组件应用视图封装

从以下模式中选择：

| 模式                        | 详情                                                         |
| --------------------------- | ------------------------------------------------------------ |
| ViewEncapsulation.ShadowDom | 组件样式仅添加到 shadow DOM 宿主中，确保它们仅影响各自组件视图中的元素 |
| ViewEncapsulation.Emulated  | 组件的样式会添加到文档的 `<head>` 中，使它们在整个应用程序中可用，但它们的选择器只会影响它们各自组件模板中的元素 |
| ViewEncapsulation.None      | 组件的样式会添加到文档的 `<head>` 中，使它们在整个应用程序中可用，因此是完全全局的，会影响文档中的任何匹配元素 |

下面展示了具有不同 `ViewEncapsulation` 的组件的样式如何交互的示例



第一个示例显示了一个具有 `ViewEncapsulation.None` 的组件。此组件将其模板元素着色为红色

```TS
@Component({
  selector: 'app-no-encapsulation',
  template: `
    <h2>None</h2>
    <div class="none-message">No encapsulation</div>
  `,
  styles: ['h2, .none-message { color: red; }'],
  encapsulation: ViewEncapsulation.None,
})
export class NoEncapsulationComponent { }
```

Angular 将此组件的样式作为全局样式添加到文档的 `<head>` 中

如前所述，Angular 还会将这些样式添加到所有 shadow DOM 宿主。因此，样式在整个应用程序中都可用

![image-20231231165832118](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Angular%20%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3/image-20231231165832118.png)



第二个示例显示了一个具有 `ViewEncapsulation.Emulated` 的组件。此组件将其模板元素着色为绿色

```TS
@Component({
  selector: 'app-emulated-encapsulation',
  template: `
    <h2>Emulated</h2>
    <div class="emulated-message">Emulated encapsulation</div>
    <app-no-encapsulation></app-no-encapsulation>
  `,
  styles: ['h2, .emulated-message { color: green; }'],
  encapsulation: ViewEncapsulation.Emulated,
})
export class EmulatedEncapsulationComponent { }
```

与 `ViewEncapsulation.None` 类似，Angular 会将此组件的样式添加到文档的 `<head>` 中，但它们是带有“作用域”的样式。只有直接在该组件模板中的元素才会匹配其样式。由于来自 `EmulatedEncapsulationComponent` 的样式是特化的，因此它们会覆盖来自 `NoEncapsulationComponent` 的全局样式

在此示例中，`EmulatedEncapsulationComponent` 包含着 `NoEncapsulationComponent`，但 `NoEncapsulationComponent` 仍然如预期般生效了，因为 `EmulatedEncapsulationComponent` 的“范围化”样式与其模板中的元素并不匹配



第三个示例显示了一个具有 `ViewEncapsulation.ShadowDom` 的组件。此组件会将其模板元素着色为蓝色

```TS
@Component({
  selector: 'app-shadow-dom-encapsulation',
  template: `
    <h2>ShadowDom</h2>
    <div class="shadow-message">Shadow DOM encapsulation</div>
    <app-emulated-encapsulation></app-emulated-encapsulation>
    <app-no-encapsulation></app-no-encapsulation>
  `,
  styles: ['h2, .shadow-message { color: blue; }'],
  encapsulation: ViewEncapsulation.ShadowDom,
})
export class ShadowDomEncapsulationComponent { }
```

Angular 仅将此组件的样式添加到 shadow DOM 宿主，因此它们在 shadow DOM 之外是不可见的

在这个例子中，`ShadowDomEncapsulationComponent` 包含一个 `NoEncapsulationComponent` 和 `EmulatedEncapsulationComponent`。

`ShadowDomEncapsulationComponent` 组件添加的样式在该组件的整个 shadow DOM 中都可用，在 `NoEncapsulationComponent` 和 `EmulatedEncapsulationComponent` 中也是如此。

`EmulatedEncapsulationComponent` 具有特化的“范围化”样式，因此该组件模板的样式不受影响。

由于 `ShadowDomEncapsulationComponent` 中的样式是在全局样式之后添加到 Shadow Host 中的，因此 `h2` 样式会覆盖 `NoEncapsulationComponent` 中的样式。结果是 `NoEncapsulationComponent` 中的 `<h2>` 元素被着色为蓝色而不是红色

![image-20231231173719805](https://simon-bookcase.oss-cn-beijing.aliyuncs.com/%E5%89%8D%E7%AB%AF/Angular%20%E4%B8%AD%E6%96%87%E6%96%87%E6%A1%A3/image-20231231173719805.png)



------

### 组件之间的交互



**通过输入型绑定把数据从父组件传到子组**

`HeroChildComponent` 有两个输入型属性，它们通常带`@Input()` 装饰器

```TS
import { Component, Input } from '@angular/core';

import { Hero } from './hero';

@Component({
  selector: 'app-hero-child',
  template: `
    <h3>{{hero.name}} says:</h3>
    <p>I, {{hero.name}}, am at your service, {{masterName}}.</p>
  `
})
export class HeroChildComponent {
  @Input() hero!: Hero;
  @Input('master') masterName = '';
}
```

第二个 `@Input` 为子组件的属性名 `masterName` 指定一个别名 `master`

父组件 `HeroParentComponent` 把子组件的 `HeroChildComponent` 放到 `*ngFor` 循环器中，把自己的 `master` 字符串属性绑定到子组件的 `master` 别名上，并把每个循环的 `hero` 实例绑定到子组件的 `hero` 属性

```TS
import { Component } from '@angular/core';

import { HEROES } from './hero';

@Component({
  selector: 'app-hero-parent',
  template: `
    <h2>{{master}} controls {{heroes.length}} heroes</h2>

    <app-hero-child
      *ngFor="let hero of heroes"
      [hero]="hero"
      [master]="master">
    </app-hero-child>
  `
})
export class HeroParentComponent {
  heroes = HEROES;
  master = 'Master';
}
```



**通过 setter 截听输入属性值的变化**

使用一个输入属性的 setter，以拦截父组件中值的变化，并采取行动

子组件 `NameChildComponent` 的输入属性 `name` 上的这个 setter，会 trim 掉名字里的空格，并把空值替换成默认字符串

```TS
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-name-child',
  template: '<h3>"{{name}}"</h3>'
})
export class NameChildComponent {
  @Input()
  get name(): string { return this._name; }
  set name(name: string) {
    this._name = (name && name.trim()) || '<no name set>';
  }
  private _name = '';
}
```

下面的 `NameParentComponent` 展示了各种名字的处理方式，包括一个全是空格的名字

```TS
import { Component } from '@angular/core';

@Component({
  selector: 'app-name-parent',
  template: `
    <h2>Master controls {{names.length}} names</h2>

    <app-name-child *ngFor="let name of names" [name]="name"></app-name-child>
  `
})
export class NameParentComponent {
  // Displays 'Dr. IQ', '<no name set>', 'Bombasto'
  names = ['Dr. IQ', '   ', '  Bombasto  '];
}
```



**通过 `ngOnChanges()` 来截听输入属性值的变化**

使用 `OnChanges` 生命周期钩子接口的 `ngOnChanges()` 方法来监测输入属性值的变化并做出回应

这个 `VersionChildComponent` 会监测输入属性 `major` 和 `minor` 的变化，并把这些变化编写成日志以报告这些变化

```TS
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-version-child',
  template: `
    <h3>Version {{major}}.{{minor}}</h3>
    <h4>Change log:</h4>
    <ul>
      <li *ngFor="let change of changeLog">{{change}}</li>
    </ul>
  `
})
export class VersionChildComponent implements OnChanges {
  @Input() major = 0;
  @Input() minor = 0;
  changeLog: string[] = [];

  ngOnChanges(changes: SimpleChanges) {
    const log: string[] = [];
    for (const propName in changes) {
      const changedProp = changes[propName];
      const to = JSON.stringify(changedProp.currentValue);
      if (changedProp.isFirstChange()) {
        log.push(`Initial value of ${propName} set to ${to}`);
      } else {
        const from = JSON.stringify(changedProp.previousValue);
        log.push(`${propName} changed from ${from} to ${to}`);
      }
    }
    this.changeLog.push(log.join(', '));
  }
}
```

`VersionParentComponent` 提供 `minor` 和 `major` 值，把修改它们值的方法绑定到按钮上

```TS
import { Component } from '@angular/core';

@Component({
  selector: 'app-version-parent',
  template: `
    <h2>Source code version</h2>
    <button type="button" (click)="newMinor()">New minor version</button>
    <button type="button" (click)="newMajor()">New major version</button>
    <app-version-child [major]="major" [minor]="minor"></app-version-child>
  `
})
export class VersionParentComponent {
  major = 1;
  minor = 23;

  newMinor() {
    this.minor++;
  }

  newMajor() {
    this.major++;
    this.minor = 0;
  }
}
```



**父组件监听子组件的事件**

子组件暴露一个 `EventEmitter` 属性，当事件发生时，子组件利用该属性 `emits` （发出）事件。父组件绑定到这个事件属性，并在事件发生时作出回应

子组件的 `EventEmitter` 属性是一个输出属性，通常带有`@Output()` 装饰器，就像在 `VoterComponent` 中看到的

```TS
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-voter',
  template: `
    <h4>{{name}}</h4>
    <button type="button" (click)="vote(true)"  [disabled]="didVote">Agree</button>
    <button type="button" (click)="vote(false)" [disabled]="didVote">Disagree</button>
  `
})
export class VoterComponent {
  @Input()  name = '';
  @Output() voted = new EventEmitter<boolean>();
  didVote = false;

  vote(agreed: boolean) {
    this.voted.emit(agreed);
    this.didVote = true;
  }
}
```

点击按钮会触发 `true` 或 `false`的事件

父组件 `VoteTakerComponent` 绑定了一个事件处理器（`onVoted()`），用来响应子组件的事件（`$event`）并更新一个计数器

```TS
import { Component } from '@angular/core';

@Component({
  selector: 'app-vote-taker',
  template: `
    <h2>Should mankind colonize the Universe?</h2>
    <h3>Agree: {{agreed}}, Disagree: {{disagreed}}</h3>

    <app-voter
      *ngFor="let voter of voters"
      [name]="voter"
      (voted)="onVoted($event)">
    </app-voter>
  `
})
export class VoteTakerComponent {
  agreed = 0;
  disagreed = 0;
  voters = ['Dr. IQ', 'Celeritas', 'Bombasto'];

  onVoted(agreed: boolean) {
    if (agreed) {
      this.agreed++;
    } else {
      this.disagreed++;
    }
  }
}
```



**父组件与子组件通过本地变量互动**

父组件不能使用数据绑定来读取子组件的属性或调用子组件的方法。但可以在父组件模板里，新建一个本地变量来代表子组件，然后利用这个变量来读取子组件的属性和调用子组件的方法，如下例所示

子组件 `CountdownTimerComponent` 进行倒计时，归零时发射一个导弹。`start` 和 `stop` 方法负责控制时钟并在模板里显示倒计时的状态信息

```TS
import { Component, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-countdown-timer',
  template: '<p>{{message}}</p>'
})
export class CountdownTimerComponent implements OnDestroy {
  message = '';
  seconds = 11;

  ngOnDestroy() { this.clearTimer?.(); }

  start() { this.countDown(); }
  stop()  {
    this.clearTimer?.();
    this.message = `Holding at T-${this.seconds} seconds`;
  }

  private clearTimer: VoidFunction | undefined;

  private countDown() {
    this.clearTimer?.();
    const interval = setInterval(() => {
      this.seconds -= 1;
      if (this.seconds === 0) {
        this.message = 'Blast off!';
      } else {
        if (this.seconds < 0) { this.seconds = 10; } // reset
        this.message = `T-${this.seconds} seconds and counting`;
      }
    }, 1000);
    this.clearTimer = () => clearInterval(interval);
  }
}
```

计时器组件的宿主组件 `CountdownLocalVarParentComponent` 如下：

```TS
import { Component } from '@angular/core';
import { CountdownTimerComponent } from './countdown-timer.component';

@Component({
  selector: 'app-countdown-parent-lv',
  template: `
    <h3>Countdown to Liftoff (via local variable)</h3>
    <button type="button" (click)="timer.start()">Start</button>
    <button type="button" (click)="timer.stop()">Stop</button>
    <div class="seconds">{{timer.seconds}}</div>
    <app-countdown-timer #timer></app-countdown-timer>
  `,
  styleUrls: ['../assets/demo.css']
})
export class CountdownLocalVarParentComponent { }
```

父组件不能通过数据绑定使用子组件的 `start` 和 `stop` 方法，也不能访问子组件的 `seconds` 属性

把本地变量 `#timer` 放到 `<app-countdown-timer>` 标签中，用来代表子组件。这样父组件的模板就得到了子组件的引用，于是可以在父组件的模板中访问子组件的所有属性和方法



**父级调用 `@ViewChild()`**

本地变量方法是个简单明了的方法。但是它也有局限性，因为父组件-子组件的连接必须全部在父组件的模板中进行。父组件本身的代码对子组件没有访问权

如果父组件的类需要依赖于子组件类，就不能使用本地变量方法。组件之间的父子关系 组件的父子关系不能通过在每个组件的*类*中各自定义本地变量来建立。这是因为这两个类的实例互相不知道，因此父类也就不能访问子类中的属性和方法

当父组件*类*需要这种访问时，可以把子组件作为 *ViewChild*，注入到父组件里面

下面的例子用与倒计时相同的范例来解释这种技术。它的外观或行为没有变化。子组件 CountdownTimerComponent 也和原来一样

```TS
import { AfterViewInit, ViewChild } from '@angular/core';
import { Component } from '@angular/core';
import { CountdownTimerComponent } from './countdown-timer.component';

@Component({
  selector: 'app-countdown-parent-vc',
  template: `
    <h3>Countdown to Liftoff (via ViewChild)</h3>
    <button type="button" (click)="start()">Start</button>
    <button type="button" (click)="stop()">Stop</button>
    <div class="seconds">{{ seconds() }}</div>
    <app-countdown-timer></app-countdown-timer>
  `,
  styleUrls: ['../assets/demo.css']
})
export class CountdownViewChildParentComponent implements AfterViewInit {

  @ViewChild(CountdownTimerComponent)
  private timerComponent!: CountdownTimerComponent;

  seconds() { return 0; }

  ngAfterViewInit() {
    // Redefine `seconds()` to get from the `CountdownTimerComponent.seconds` ...
    // but wait a tick first to avoid one-time devMode
    // unidirectional-data-flow-violation error
    setTimeout(() => this.seconds = () => this.timerComponent.seconds, 0);
  }

  start() { this.timerComponent.start(); }
  stop() { this.timerComponent.stop(); }
}
```



**父组件和子组件通过服务来通讯**

父组件和它的子组件共享同一个服务，利用该服务在组件家族内部实现双向通讯

该服务实例的作用域被限制在父组件和其子组件内。这个组件子树之外的组件将无法访问该服务或者与它们通讯

这个 `MissionService` 把 `MissionControlComponent` 和多个 `AstronautComponent` 子组件连接起来

```TS
import { Injectable } from '@angular/core';
import { Subject } from 'rxjs';

@Injectable()
export class MissionService {

  // Observable string sources
  private missionAnnouncedSource = new Subject<string>();
  private missionConfirmedSource = new Subject<string>();

  // Observable string streams
  missionAnnounced$ = this.missionAnnouncedSource.asObservable();
  missionConfirmed$ = this.missionConfirmedSource.asObservable();

  // Service message commands
  announceMission(mission: string) {
    this.missionAnnouncedSource.next(mission);
  }

  confirmMission(astronaut: string) {
    this.missionConfirmedSource.next(astronaut);
  }
}
```

`MissionControlComponent` 提供服务的实例，并将其共享给它的子组件（通过 `providers` 元数据数组），子组件可以通过构造函数将该实例注入到自身

```TS
import { Component } from '@angular/core';

import { MissionService } from './mission.service';

@Component({
  selector: 'app-mission-control',
  template: `
    <h2>Mission Control</h2>
    <button type="button" (click)="announce()">Announce mission</button>

    <app-astronaut
      *ngFor="let astronaut of astronauts"
      [astronaut]="astronaut">
    </app-astronaut>

    <h3>History</h3>
    <ul>
      <li *ngFor="let event of history">{{event}}</li>
    </ul>
  `,
  providers: [MissionService]
})
export class MissionControlComponent {
  astronauts = ['Lovell', 'Swigert', 'Haise'];
  history: string[] = [];
  missions = ['Fly to the moon!',
              'Fly to mars!',
              'Fly to Vegas!'];
  nextMission = 0;

  constructor(private missionService: MissionService) {
    missionService.missionConfirmed$.subscribe(
      astronaut => {
        this.history.push(`${astronaut} confirmed the mission`);
      });
  }

  announce() {
    const mission = this.missions[this.nextMission++];
    this.missionService.announceMission(mission);
    this.history.push(`Mission "${mission}" announced`);
    if (this.nextMission >= this.missions.length) { this.nextMission = 0; }
  }
}
```

`AstronautComponent` 也通过自己的构造函数注入该服务。由于每个 `AstronautComponent` 都是 `MissionControlComponent` 的子组件，所以它们获取到的也是父组件的这个服务实例

```TS
import { Component, Input, OnDestroy } from '@angular/core';

import { MissionService } from './mission.service';
import { Subscription } from 'rxjs';

@Component({
  selector: 'app-astronaut',
  template: `
    <p>
      {{astronaut}}: <strong>{{mission}}</strong>
      <button
        type="button"
        (click)="confirm()"
        [disabled]="!announced || confirmed">
        Confirm
      </button>
    </p>
  `
})
export class AstronautComponent implements OnDestroy {
  @Input() astronaut = '';
  mission = '<no mission announced>';
  confirmed = false;
  announced = false;
  subscription: Subscription;

  constructor(private missionService: MissionService) {
    this.subscription = missionService.missionAnnounced$.subscribe(
      mission => {
        this.mission = mission;
        this.announced = true;
        this.confirmed = false;
    });
  }

  confirm() {
    this.confirmed = true;
    this.missionService.confirmMission(this.astronaut);
  }

  ngOnDestroy() {
    // prevent memory leak when component destroyed
    this.subscription.unsubscribe();
  }
}
```



------

### 组件样式



Angular 应用使用标准的 CSS 来设置样式。这意味着你可以把关于 CSS 的那些知识和技能直接用于 Angular 程序中，比如：样式表、选择器、规则以及媒体查询等

另外，Angular 还能把组件样式捆绑在组件上，以实现比标准样式表更加模块化的设计



有几种方式把样式加入组件：

1. 设置 `styles` 或 `styleUrls` 元数据

   ```TS
   @Component({
     selector: 'app-root',
     template: `
       <h1>Tour of Heroes</h1>
       <app-hero-main [hero]="hero"></app-hero-main>
     `,
     styles: ['h1 { font-weight: normal; }']
   })
   export class HeroAppComponent {
   /* . . . */
   }
   ```

   ```TS
   @Component({
     selector: 'app-root',
     template: `
       <h1>Tour of Heroes</h1>
       <app-hero-main [hero]="hero"></app-hero-main>
     `,
     styleUrls: ['./hero-app.component.css']
   })
   export class HeroAppComponent {
   /* . . . */
   }
   ```

2. 内联在模板的 HTML 中

   ```TS
   @Component({
     selector: 'app-hero-controls',
     template: `
       <style>
         button {
           background-color: white;
           border: 1px solid #777;
         }
       </style>
       <h3>Controls</h3>
       <button type="button" (click)="activate()">Activate</button>
     `
   })
   ```

3. 通过 CSS 文件导入

   ```TS
   @Component({
     selector: 'app-hero-team',
     template: `
       <!-- We must use a relative URL so that the AOT compiler can find the stylesheet -->
       <link rel="stylesheet" href="../assets/hero-team.component.css">
       <h3>Team</h3>
       <ul>
         <li *ngFor="let member of hero.team">
           {{member}}
         </li>
       </ul>`
   })
   ```

   

如果使用 CLI 进行构建，那么你可以用 sass 或 less 来编写样式，并使用相应的扩展名（.scss、.less）把它们指定到 @Component.styleUrls 元数据中。例子如下：

```TS
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
…
```

CLI 的构建过程会运行相关的预处理器



------

### 在父子指令及组件之间共享数据



Angular 中的一个常见模式就是在父组件和一个或多个子组件之间共享数据。可以用 `@Input()` 和 `@Output()` 来实现这个模式

`@Input()` 和 `@Output()` 为子组件提供了一种与其父组件通信的方法。`@Input()` 允许父组件更新子组件中的数据。相反，`@Output()` 允许子组件向父组件发送数据



**把数据发送到子组件**

子组件或指令中的 `@Input()` 装饰器表示该属性可以从其父组件中获取值

要使用 `@Input()` 装饰器，首先要导入 `Input`，然后用 `@Input()` 装饰该属性，如下例所示

```TS
import { Component, Input } from '@angular/core'; // First, import Input
export class ItemDetailComponent {
  @Input() item = ''; // decorate the property with @Input()
}
```

接下来，在子组件模板中添加以下内容：

```HTML
<p>
  Today's item: {{item}}
</p>
```

下一步是在父组件的模板中绑定该属性，使用属性绑定把子组件的 `item` 属性绑定到父组件的 `currentItem` 属性上

```TS
<app-item-detail [item]="currentItem"></app-item-detail>
```

在父组件类中，为 `currentItem` 指定一个值：

```TS
export class AppComponent {
  currentItem = 'Television';
}
```

通过 `@Input()`，Angular 把 `currentItem` 的值传给子组件，以便 `item` 渲染为 `Television`



**把数据发送到父组件**

子组件或指令中的 `@Output()` 装饰器允许数据从子组件传给父组件

子组件使用 `@Output()` 属性来引发事件，以通知父组件这一变化。为了引发事件，`@Output()` 必须是 `EventEmitter` 类型，它是 `@angular/core` 中用来发出自定义事件的类

在子组件类中导入 `Output` 和 `EventEmitter`

```TS
import { Output, EventEmitter } from '@angular/core';
```

在组件类中，用 `@Output()` 装饰一个属性。下面的例子中 `newItemEvent` 这个 `@Output()` 的类型为 `EventEmitter`，这意味着它是一个事件

```TS
@Output() newItemEvent = new EventEmitter<string>();
```

在同一个组件类中创建一个 `addNewItem()` 方法：

```TS
export class ItemOutputComponent {

  @Output() newItemEvent = new EventEmitter<string>();

  addNewItem(value: string) {
    this.newItemEvent.emit(value);
  }
}
```

子组件的模板有两个控件。第一个是带有模板引用变量 `#newItem` 的 `<input>`，用户可在其中输入条目名称。`#newItem` 变量的 `value` 属性存储了用户输入到 `<input>` 中的值。第二个元素是带有 `click` 事件绑定的 `<button>` 元素，`(click)` 事件绑定到了子组件类中 `addNewItem()` 方法。`addNewItem()` 方法接受一个 `#newItem.value` 属性的值作为参数

```TS
<label for="item-input">Add an item:</label>
<input type="text" id="item-input" #newItem>
<button type="button" (click)="addNewItem(newItem.value)">Add to parent's list</button>
```

在父模板中，把父组件的方法绑定到子组件的事件上

把子组件选择器（`<app-item-output>`）放在父组件的模板 `app.component.html` 中

```TS
<app-item-output (newItemEvent)="addItem($event)"></app-item-output>
```

事件绑定 `(newItemEvent)='addItem($event)'` 会把子组件中的 `newItemEvent` 事件连接到父组件的 `addItem()` 方法

`$event` 中包含用户在子组件模板上的 `<input>` 中键入的数据

```TS
<ul>
  <li *ngFor="let item of items">{{item}}</li>
</ul>
```



**同时使用 `@Input()` 和 `@Output()`**

可以在同一个子组件上使用 `@Input()` 和 `@Output()`，范例如下：

```TS
<app-input-output
  [item]="currentItem"
  (deleteRequest)="crossOffItem($event)">
</app-input-output>
```

目标 `item` 是子组件类中的一个 `@Input()` 属性，它会从父组件的 `currentItem` 属性中获取它的值。当你单击“删除”时，子组件就会引发一个事件 `deleteRequest`，它会作为父组件中 `crossOffItem()` 方法的参数



------

### 内容投影



内容投影是一种模式，你可以在其中插入或投影要在另一个组件中使用的内容。比如，你可能有一个 `Card` 组件，它可以接受另一个组件提供的内容



**单插槽内容投影**

内容投影的最基本形式是*单插槽内容投影*。单插槽内容投影是指创建一个组件，你可以在其中投影一个组件

要创建使用单插槽内容投影的组件，请执行以下操作：

1. 创建一个组件
2. 在组件模板中，添加 `<ng-content>` 元素，让你希望投影的内容出现在其中

比如，以下组件使用 `<ng-content>` 元素来显示消息

```TS
import { Component } from '@angular/core';

@Component({
  selector: 'app-zippy-basic',
  template: `
    <h2>Single-slot content projection</h2>
    <ng-content></ng-content>
  `
})
export class ZippyBasicComponent {}
```

有了 `<ng-content>` 元素，该组件的用户现在可以将自己的消息投影到该组件中。比如：

```TS
<app-zippy-basic>
  <p>Is content projection cool?</p>
</app-zippy-basic>
```

`<ng-content>` 元素是一个占位符，它不会创建真正的 DOM 元素。`<ng-content>` 的那些自定义属性将被忽略



**多插槽内容投影**

一个组件可以具有多个插槽。每个插槽可以指定一个 CSS 选择器，该选择器会决定将哪些内容放入该插槽。该模式称为多插槽内容投影。使用此模式，你必须指定希望投影内容出现在的位置。你可以通过使用 `<ng-content>` 的 `select` 属性来完成此任务

要创建使用多插槽内容投影的组件，请执行以下操作：

1. 创建一个组件
2. 在组件模板中，添加 `<ng-content>` 元素，让你希望投影的内容出现在其中
3. 将 `select` 属性添加到 `<ng-content>` 元素上。Angular 使用的选择器支持标签名、属性、CSS 类和 `:not` 伪类的任意组合

比如，以下组件会使用两个 `<ng-content>` 元素

```TS
import { Component } from '@angular/core';

@Component({
  selector: 'app-zippy-multislot',
  template: `
    <h2>Multi-slot content projection</h2>

    Default:
    <ng-content></ng-content>

    Question:
    <ng-content select="[question]"></ng-content>
  `
})
export class ZippyMultislotComponent {}
```

使用 `question` 属性的内容将投影到带有 `select=[question]` 属性的 `<ng-content>` 元素

```TS
<app-zippy-multislot>
  <p question>
    Is content projection cool?
  </p>
  <p>Let's learn about content projection!</p>
</app-zippy-multislot>
```



**有条件的内容投影**

如果你的组件需要有条件地渲染内容或多次渲染内容，则应配置该组件以接受一个 `<ng-template>` 元素，其中包含要有条件渲染的内容

在这种情况下，不建议使用 `<ng-content>` 元素，因为只要组件的使用者提供了内容，即使该组件从未定义 `<ng-content>` 元素或该 `<ng-content>` 元素位于 `ngIf` 语句的内部，该内容也总会被初始化

使用 `<ng-template>` 元素，你可以让组件根据你想要的任何条件显式渲染内容，并可以进行多次渲染。在显式渲染 `<ng-template>` 元素之前，Angular 不会初始化该元素的内容

`<ng-template>` 进行条件内容投影的典型实现

1. 创建一个组件

2. 在接受 `<ng-template>` 元素的组件中，使用 `<ng-container>` 元素渲染该模板，比如：

   ```TS
   <ng-container [ngTemplateOutlet]="content.templateRef"></ng-container>
   ```

3. 将 `<ng-container>` 元素包装在另一个元素（比如 `div` 元素）中，然后应用条件逻辑

   ```TS
   <div *ngIf="expanded" [id]="contentId">
       <ng-container [ngTemplateOutlet]="content.templateRef"></ng-container>
   </div>
   ```

4. 在要投影内容的模板中，将投影的内容包装在 `<ng-template>` 元素中，比如：

   ```TS
   <ng-template appExampleZippyContent>
     It depends on what you do with it.
   </ng-template>
   ```

   这个 `<ng-template>` 元素定义了一个组件可以根据其自身逻辑渲染的内容块。组件可以使用 `@ContentChild` 或 `@ContentChildren` 装饰器获得对此模板内容的引用（即 `TemplateRef`）。前面的示例创建了一个自定义指令 `appExampleZippyContent` 作为 API，以将 `<ng-template>` 标记为组件内容。借助这个 `TemplateRef`，组件可以使用 `ngTemplateOutlet` 指令或 `ViewContainerRef` 的 `createEmbeddedView()` 方法来渲染所引用的内容

5. 创建一个属性型指令，它具有与这个模板的自定义属性相匹配的选择器。在此指令中，注入 `TemplateRef` 实例

   ```TS
   @Directive({
     selector: '[appExampleZippyContent]'
   })
   export class ZippyContentDirective {
     constructor(public templateRef: TemplateRef<unknown>) {}
   }
   ```

6. 在你要将内容投影到的组件中，使用 `@ContentChild` 获取此投影内容的模板

   ```TS
   @ContentChild(ZippyContentDirective) content!: ZippyContentDirective;
   ```

   

------

## 模板

### 介绍



在 Angular 中，模板就是一块 HTML。你可以在模板中通过一种特殊的语法来使用 Angular 的诸多特性

在模板中使用 Angular 的特有语法来扩展应用程序的 HTML 词汇表。比如，Angular 可以通过内置的模板函数、变量、事件监听和数据绑定等功能来帮助你动态获取和设置 DOM（文档对象模型）中的值

几乎所有的 HTML 语法都是有效的模板语法。但是，由于 Angular 模板只是整个网页的一部分，而不是整个网页，因此你不需要包含诸如 `<html>`，`<body>` 或 `<base>` 元素。这样你可以专注于正在开发的那部分页面



------

### 文本插值



插值是指将表达式嵌入到被标记的文本中。默认情况下，插值使用双花括号 `{{` 和 `}}` 作为定界符

为了说明插值的工作原理，请考虑一个包含 `currentCustomer` 变量的 Angular 组件：

```TS
currentCustomer = 'Maria';
```

可以用插值在相应的组件模板中显示此变量的值：

```TS
<h3>Current customer: {{ currentCustomer }}</h3>
```

Angular 会用相应组件属性的字符串值替换掉 `currentCustomer`。在这里，它的值是 `Maria`



在以下示例中，Angular 会求出 `title` 和 `itemImageUrl` 属性的值，以显示一些标题文本和图像

```TS
<p>{{title}}</p>
<div><img alt="item" src="{{itemImageUrl}}"></div>
```



------

### 模板语句



模板语句是可在 HTML 中用于响应用户事件的方法或属性。使用模板语句，你的应用可以通过诸如显示动态内容或提交表单之类的动作吸引用户

在以下示例中，模板语句 `deleteHero()` 出现在 `=` 号右侧的引号中，`(event)="statement"`

```TS
<button type="button" (click)="deleteHero()">Delete hero</button>
```

当用户单击 Delete hero 按钮时，Angular 就会调用组件类中 `deleteHero()` 方法

可以将模板语句与元素、组件或指令一起使用以响应事件



------

###  Property 绑定



Angular 中的属性绑定可帮助你设置 HTML 元素或指令的属性值。使用属性绑定，可以执行诸如切换按钮、以编程方式设置路径，以及在组件之间共享值之类的功能



要绑定到元素的属性，请将其括在方括号 `[]` 内，这会将此属性标为目标属性。目标属性就是你要对其进行赋值的 DOM 属性

要为 image 元素的目标属性（`src`）赋值，请键入以下代码：

```TS
<img alt="item" [src]="itemImageUrl">
```

在大多数情况下，目标名称是 Property（属性）名称，即使它看起来是 Attribute（属性）名称，在这个例子中，`src` 就是 `<img>` 元素的 Property 名称

如果不使用方括号，Angular 就会将右侧视为字符串字面量并将此属性设置为该静态值

要将字符串赋值给属性，请键入以下代码：

```TS
<app-item-detail childItem="parentItem"></app-item-detail>
```

省略方括号就会渲染出字符串 `parentItem`，而不是 `parentItem` 的值



要将 `<img>` 的 `src` 属性绑定到组件的属性，请将 `src` 放在方括号中，后跟等号，然后是组件的属性

使用属性 `itemImageUrl`，键入以下代码：

```TS
<img alt="item" [src]="itemImageUrl">
```

在组件类 `AppComponent` 中声明 `itemImageUrl` 属性

```TS
itemImageUrl = '../assets/phone.svg';
```



------

### Attribute 绑定



Angular 中的 Attribute 绑定可帮助你直接设置 Attribute 值。使用 Attribute 绑定，你可以提升无障碍性、动态设置应用程序样式以及同时管理多个 CSS 类或样式

Attribute 绑定语法类似于 Property 绑定，但不是直接在方括号之间放置元素的 Property，而是在 Attribute 名称前面加上前缀 `attr`，后跟一个点 `.`。然后，使用解析为字符串的表达式设置 Attribute 值

```TS
<p [attr.attribute-you-are-targeting]="expression"></p>
```



Attribute 绑定的主要用例之一是设置 ARIA Attribute

要绑定到 ARIA Attribute，请键入以下内容：

```TS
<!-- create and set an aria attribute for assistive technology -->
<button type="button" [attr.aria-label]="actionName">{{actionName}} with Aria</button>
```



Attribute 绑定的另一个常见用例是绑定到表格中的 `colspan` Attribute。`colspan` Attribute 可帮助你以编程方式让表格保持动态。根据应用中用来填充表的数据量，某一行要跨越的列数可能会发生变化

要将 Attribute 绑定到 `<td>` 的 `colspan` Attribute

1. 使用以下语法指定 `colspan`：`[attr.colspan]`
2. 将 `[attr.colspan]` 设置为等于某个表达式

在下面的示例中，我们将 `colspan` Attribute 绑定到表达式 `1 + 1`

```TS
<!--  expression calculates colspan=2 -->
<tr><td [attr.colspan]="1 + 1">One-Two</td></tr>
```



------

### 类和样式绑定



使用类和样式绑定从元素的 `class` 属性中添加和删除 CSS 类名，以及动态设置样式



**绑定到多个 CSS 类**

下表是各种类绑定语法的小结

| 绑定类型   | 语法                      | 输入属性                                     | 范例输入值                         |
| ---------- | ------------------------- | -------------------------------------------- | ---------------------------------- |
| 单一类绑定 | [class.sale]="onSale"     | boolean \| undefined \| null                 | true, false                        |
| 多重类绑定 | [class]="classExpression" | string                                       | "my-class-1 my-class-2 my-class-3" |
| 多重类绑定 | [class]="classExpression" | Record<string, boolean \| undefined \| null> | {foo: true, bar: false}            |
| 多重类绑定 | [class]="classExpression" | Array                                        | ['foo', 'bar']                     |



**绑定到单一样式**

要创建单个样式绑定，请使用 `style` 前缀，后跟一个点和 CSS 样式的名称

比如，要设置 'width' 样式，请键入以下内容：`[style.width]="width"`



**绑定到多个样式**

要切换多个样式，请绑定到 `[style]` Attribute，比如 `[style]="styleExpression"`。`styleExpression` 可以是如下格式之一：

* 样式的字符串列表，比如 `"width: 100px; height: 100px; background-color: cornflowerblue;"`
* 一个对象，其键名是样式名，其值是样式值，比如 `{width: '100px', height: '100px', backgroundColor: 'cornflowerblue'}`

注意，不支持把数组绑定给 `[style]`



------

### 事件绑定



通过事件绑定，你可以侦听并响应用户操作，比如按键、鼠标移动、点击和触摸



**绑定到事件**

要绑定到事件，你可以使用 Angular 事件绑定语法。此语法由等号左侧括号中的目标事件名称和右侧带引号的模板语句组成

创建以下示例；目标事件名是 `click`，模板语句是 `onSave()`

```TS
<button (click)="onSave()">Save</button>
```

事件绑定侦听按钮的单击事件，并在发生单击时调用组件的 `onSave()`



**确定事件目标**

为了确定事件目标，Angular 会检查目标事件的名称是否与已知指令的 event 属性匹配

创建以下示例：（Angular 会检查 `myClick` 是否是自定义 `ClickDirective` 上的事件）

```TS
<h4>myClick is an event on the custom ClickDirective:</h4>
<button type="button" (myClick)="clickMessage=$event" clickable>click with myClick</button>
{{clickMessage}}
```

如果目标事件名称 `myClick` 与 `ClickDirective` 的输出属性不匹配，Angular 将改为绑定到基础 DOM 元素上的 `myClick` 事件



**绑定到键盘事件**

可以用 Angular 的绑定语法绑定到键盘事件。你可以指定要绑定到键盘事件的键值或代码。它们的 `key` 和 `code` 字段是浏览器键盘事件对象的原生部分。默认情况下，事件绑定假定你要使用键盘事件上的 `key` 字段。你还可以用 `code` 字段

键的组合可以用点（`.`）分隔。例如，`keydown.enter` 将允许你将事件绑定到 `enter` 键。你还可以用修饰键，例如 `shift` 、 `alt` 、 `control` 和 Mac 中的 `command` 键。以下示例展示了如何将键盘事件绑定到 `keydown.shift.t`

```TS
<input (keydown.shift.t)="onKeydown($event)" />
```

根据操作系统的不同，某些组合键可能会创建特殊字符，而不是你期望的组合键。例如，当你同时使用 option 和 shift 键时，MacOS 会创建特殊字符。如果你绑定到 `keydown.shift.alt.t`，在 macOS 上，该组合会生成 `ˇ` 而不是 `t`，它与绑定不匹配，也不会触发你的事件处理程序。要绑定到 macOS 上的 `keydown.shift.alt.t`，请使用 `code` 键盘事件字段来获取正确的行为，例如此示例中显示的 `keydown.code.shiftleft.altleft.keyt`

```TS
<input (keydown.code.shiftleft.altleft.keyt)="onKeydown($event)" />
```

`code` 字段比 `key` 字段更具体。`key` 字段总是会报告 `shift`，而 `code` 字段将指明 `leftshift` 或 `rightshift`。使用 `code` 字段时，你可能需要添加单独的绑定以捕获你想要的所有行为。使用 `code` 字段时无需处理操作系统特有的行为，例如 macOS 上的 `shift + option` 行为



------

### 双向绑定



双向绑定为应用中的组件提供了一种共享数据的方式。使用双向绑定绑定来侦听事件并在父组件和子组件之间同步更新值



Angular 的双向绑定语法是方括号和圆括号的组合 `[()]`。`[]` 进行属性绑定，`()` 进行事件绑定，如下所示

```TS
<app-sizer [(size)]="fontSizePx"></app-sizer>
```



为了使双向数据绑定有效，`@Output()` 属性的名字必须遵循 `inputChange` 模式，其中 `input` 是相应 `@Input()` 属性的名字。比如，如果 `@Input()` 属性为 `size`，则 `@Output()` 属性必须为 `sizeChange`

后面的 `sizerComponent` 具有值属性 `size` 和事件属性 `sizeChange`。`size` 属性是 `@Input()`，因此数据可以流入 `sizerComponent`。`sizeChange` 事件是一个 `@Output()`，它允许数据从 `sizerComponent` 流出到父组件

接下来，有两个方法，`dec()` 用于减小字体大小，`inc()` 用于增大字体大小。这两种方法使用 `resize()` 在最小/最大值的约束内更改 `size` 属性的值，并发出带有新 `size` 值的事件

```TS
export class SizerComponent {

  @Input()  size!: number | string;
  @Output() sizeChange = new EventEmitter<number>();

  dec() { this.resize(-1); }
  inc() { this.resize(+1); }

  resize(delta: number) {
    this.size = Math.min(40, Math.max(8, +this.size + delta));
    this.sizeChange.emit(this.size);
  }
}
```

`sizerComponent` 模板有两个按钮，分别将 click 事件绑定到 `inc()` 和 `dec()` 方法。当用户单击按钮之一时，`sizerComponent` 调用相应的方法。`inc()` 和 `dec()` 这两个方法分别使用 `+1` 或 `-1` 调用 `resize()` 方法，它使用新的 size 值引发 `sizeChange` 事件

```TS
<div>
  <button type="button" (click)="dec()" title="smaller">-</button>
  <button type="button" (click)="inc()" title="bigger">+</button>
  <span [style.font-size.px]="size">FontSize: {{size}}px</span>
</div>
```

在 `AppComponent` 模板中，`fontSizePx` 被双向绑定到 `SizerComponent`

```TS
<app-sizer [(size)]="fontSizePx"></app-sizer>
<div [style.font-size.px]="fontSizePx">Resizable Text</div>
```

在 `AppComponent` 中，通过将 `fontSizePx` 的值设置为 `16` 来设置初始 `SizerComponent.size` 值

```TS
fontSizePx = 16;
```

单击这些按钮将更新 `AppComponent.fontSizePx`。修改后的 `AppComponent.fontSizePx` 值将更新样式绑定，从而使显示的文本变大或变小

双向绑定语法是属性绑定和事件绑定的组合的简写形式。拆成单独的属性绑定和事件绑定形式的 `SizerComponent` 代码如下

```TS
<app-sizer [size]="fontSizePx" (sizeChange)="fontSizePx=$event"></app-sizer>
```

`$event` 变量包含 `SizerComponent.sizeChange` 事件的数据。当用户单击按钮时，Angular 将 `$event` 赋值给 `AppComponent.fontSizePx`



------

## 管道



管道是在模板表达式中使用的简单函数，用于接受输入值并返回转换后的值。管道很有用，因为你可以在整个应用程序中使用它们，同时每个管道只声明一次。例如，你可以用管道将日期显示为1988 年 4 月 15 日，而不是原始字符串格式

Angular 为典型的数据转换提供了内置的管道，包括国际化的转换（i18n），它使用本地化信息来格式化数据。数据格式化常用的内置管道如下：

- [`DatePipe`](https://angular.cn/api/common/DatePipe)：根据本地环境中的规则格式化日期值
- [`UpperCasePipe`](https://angular.cn/api/common/UpperCasePipe)：把文本全部转换成大写
- [`LowerCasePipe`](https://angular.cn/api/common/LowerCasePipe)：把文本全部转换成小写
- [`CurrencyPipe`](https://angular.cn/api/common/CurrencyPipe)：把数字转换成货币字符串，根据本地环境中的规则进行格式化
- [`DecimalPipe`](https://angular.cn/api/common/DecimalPipe)：把数字转换成带小数点的字符串，根据本地环境中的规则进行格式化
- [`PercentPipe`](https://angular.cn/api/common/PercentPipe)：把数字转换成百分比字符串，根据本地环境中的规则进行格式化

关于内置管道的完整列表，请参阅[管道 API 文档](https://angular.cn/api/common#pipes)



管道操作符要比三目运算符（`?:`）的优先级高，这意味着 `a ? b : c | x` 会被解析成 `a ? b : (c | x)`

由于这种优先级设定，如果你要用管道处理三目元算符的结果，就要把整个表达式包裹在括号中，比如 `(a ? b : c) | x`



可以用可选参数微调管道的输出。比如，你可以使用 [`CurrencyPipe`](https://angular.cn/api/common/CurrencyPipe) 和国家代码（如 EUR）作为参数。模板表达式 `{{ amount | currency:'EUR' }}` 会把 `amount` 转换成欧元。紧跟在管道名称（`currency`）后面的是冒号（`:`）和参数值（`'EUR'`）

如果管道能接受多个参数，就用冒号分隔这些值。比如，`{{ amount | currency:'EUR':'Euros '}}` 会把第二个参数（字符串 `'Euros '`）添加到输出字符串中。你可以使用任何有效的模板表达式作为参数，比如字符串字面量或组件的属性

有些管道需要至少一个参数，并且允许使用更多的可选参数，比如 [`SlicePipe`](https://angular.cn/api/common/SlicePipe)。比如，`{{ slice:1:5 }}` 会创建一个新数组或字符串，它以第 `1` 个元素开头，并以第 `5` 个元素结尾



------

## 模板变量



模板变量可以帮助你在模板的另一部分使用这个部分的数据。使用模板变量，你可以执行某些任务，比如响应用户输入或微调应用的表单

模板变量可以引用这些东西：

* 模板中的 DOM 元素
* 指令或组件
* 来自 ng-template 的 TemplateRef
* Web 组件



在模板中，要使用井号 `#` 来声明一个模板变量。下列模板变量 `#phone` 声明了一个名为 `phone` 的变量，其值为此 `<input>` 元素

```TS
<input #phone placeholder="phone number" />
```

可以在组件模板中的任何地方引用某个模板变量。这里的 `<button>` 就引用了 `phone` 变量

```TS
<input #phone placeholder="phone number" />

<!-- lots of other elements -->

<!-- phone refers to the input element; pass its `value` to an event handler -->
<button type="button" (click)="callPhone(phone.value)">Call</button>
```



------

## 指令



指令是为 Angular 应用程序中的元素添加额外行为的类。使用 Angular 的内置指令，你可以管理表单、列表、样式以及要让用户看到的任何内容

Angular 指令的不同类型如下：

* 组件：带有模板的指令。这种指令类型是最常见的指令类型
* 属性型指令：更改元素、组件或其他指令的外观或行为的指令
* 结构型指令：通过添加和删除 DOM 元素来更改 DOM 布局



------

### 内置属性型指令



属性型指令会监听并修改其它 HTML 元素和组件的行为、Attribute 和 Property

许多 NgModule（比如 `RouterModule` 和 `FormsModule` 都定义了自己的属性型指令。最常见的属性型指令如下：

* NgClass：添加和删除一组 CSS 类
* NgStyle：添加和删除一组 HTML 样式
* NgModel：将双向数据绑定添加到 HTML 表单元素



可以用 NgClass 同时添加或删除多个 CSS 类，用法如下：

1. 将 `NgClass` 与表达式一起使用

   ```TS
   <!-- toggle the "special" class on/off with a property -->
   <div [ngClass]="isSpecial ? 'special' : ''">This div is special</div>
   ```

2. 将 `NgClass` 与方法一起使用

   ```TS
   currentClasses: Record<string, boolean> = {};
   /* . . . */
   setCurrentClasses() {
     // CSS classes: added/removed per current state of component properties
     this.currentClasses =  {
       saveable: this.canSave,
       modified: !this.isUnchanged,
       special:  this.isSpecial
     };
   }
   ```

   ```TS
   <div [ngClass]="currentClasses">This div is initially saveable, unchanged, and special.</div>
   ```

   

可以用 `NgStyle` 根据组件的状态同时设置多个内联样式，用法如下：

```TS
currentStyles: Record<string, string> = {};
/* . . . */
setCurrentStyles() {
  // CSS styles: set per current state of component properties
  this.currentStyles = {
    'font-style':  this.canSave      ? 'italic' : 'normal',
    'font-weight': !this.isUnchanged ? 'bold'   : 'normal',
    'font-size':   this.isSpecial    ? '24px'   : '12px'
  };
}
```

```TS
<div [ngStyle]="currentStyles">
  This div is initially italic, normal weight, and extra large (24px).
</div>
```



可以用 `NgModel` 指令显示数据属性，并在用户进行更改时更新该属性，用法如下：

1. 导入 `FormsModule`，并将其添加到 NgModule 的 `imports` 列表中

   ```TS
   import { FormsModule } from '@angular/forms'; // <--- JavaScript import from Angular
   /* . . . */
   @NgModule({
     /* . . . */
   
     imports: [
       BrowserModule,
       FormsModule // <--- import into the NgModule
     ],
     /* . . . */
   })
   export class AppModule { }
   ```

2. 在 HTML 的 `<form>` 元素上添加 `[(ngModel)]` 绑定，并将其设置为等于此属性，这里是 `name`

   ```TS
   <label for="example-ngModel">[(ngModel)]:</label>
   <input [(ngModel)]="currentItem.name" id="example-ngModel">
   ```

3. 要自定义配置，你可以编写可展开的表单，该表单将属性绑定和事件绑定分开。使用属性绑定来设置属性，并使用事件绑定来响应更改。以下示例将 `<input>` 值更改为大写：

   ```TS
   <input [ngModel]="currentItem.name" (ngModelChange)="setUppercaseName($event)" id="example-uppercase">
   ```

   

------

### 内置结构型指令



结构型指令的职责是 HTML 布局。它们塑造或重塑 DOM 的结构，这通常是通过添加、移除和操纵它们所附加到的宿主元素来实现的

最常见的内置结构型指令：

* NgIf：有条件地从模板创建或销毁子视图
* NgFor：为列表中的每个条目重复渲染一个节点
* NgSwith：一组在备用视图之间切换的指令



可以将 `NgIf` 指令应用于宿主元素来添加或删除元素

如果 `NgIf` 为 `false`，则 Angular 将从 DOM 中移除一个元素及其后代。然后，Angular 会销毁其组件，从而释放内存和资源

要添加或删除元素，请在以下示例 `*ngIf` 绑定到条件表达式，比如 `isActive`

```TS
<app-item-detail *ngIf="isActive" [item]="item"></app-item-detail>
```

当 `isActive` 表达式返回真值时，`NgIf` 会把 `ItemDetailComponent` 添加到 DOM 中。当表达式为假值时，`NgIf` 会从 DOM 中删除 `ItemDetailComponent` 并销毁该组件及其所有子组件



可以用 `NgFor` 来指令显示条目列表

```TS
<div *ngFor="let item of items">{{item.name}}</div>
```

可以获取 `*ngFor` 的 `index`，并在模板中使用它

```TS
<div *ngFor="let item of items; let i=index">{{i + 1}} - {{item.name}}</div>
```



就像 JavaScript 的 `switch` 语句一样。`NgSwitch` 会根据切换条件显示几个可能的元素中的一个。Angular 只会将选定的元素放入 DOM

`NgSwitch` 是一组指令（共三个）：

* NgSwitch：一个属性型指令，会更改其伴生指令的行为
* NgSwitchCase：当其绑定值等于开关值时将其元素添加到 DOM 中，而在其不等于开关值时将其绑定值移除
* NgSwitchDefault：当没有选中的 `NgSwitchCase` 时，将其宿主元素添加到 DOM 中

1. 在每个元素（比如 `<div>`）上，把 `[ngSwitch]` 绑定到一个返回开关值的表达式（比如 `feature`）。尽管这个例子中 `feature` 值是字符串，但此开关值可以是任何类型

2. 将各个分支元素绑定到 `*ngSwitchCase` 和 `*ngSwitchDefault`

   ```TS
   <div [ngSwitch]="currentItem.feature">
     <app-stout-item    *ngSwitchCase="'stout'"    [item]="currentItem"></app-stout-item>
     <app-device-item   *ngSwitchCase="'slim'"     [item]="currentItem"></app-device-item>
     <app-lost-item     *ngSwitchCase="'vintage'"  [item]="currentItem"></app-lost-item>
     <app-best-item     *ngSwitchCase="'bright'"   [item]="currentItem"></app-best-item>
   <!-- . . . -->
     <app-unknown-item  *ngSwitchDefault           [item]="currentItem"></app-unknown-item>
   </div>
   ```

3. 在父组件中，定义 `currentItem` 以便可以在 `[ngSwitch]` 表达式中使用它

   ```TS
   currentItem!: Item;
   ```

4. 在每个子组件中，添加一个输入属性 `item`，该属性会绑定到父组件的 `currentItem`。以下两个片段显示了父组件和其中一个子组件。其他子组件与 `StoutItemComponent` 中的相同

   ```TS
   export class StoutItemComponent {
     @Input() item!: Item;
   }
   ```

   

------

### 属性型命令



使用属性型指令，可以更改 DOM 元素和 Angular 组件的外观或行为

下面展示如何创建一个简单的属性型指令，该指令会将宿主元素的背景色设置为黄色



1. 要创建指令，请使用 CLI 命令 `ng generate directive`

   ```SH
   ng generate directive highlight
   ```

   CLI 生成默认的 `src/app/highlight.directive.ts`，如下所示：

   ```TS
   import { Directive } from '@angular/core';
   
   @Directive({
     selector: '[appHighlight]'
   })
   export class HighlightDirective {
   
   }
   ```

   `@Directive()` 装饰器的配置属性会指定指令的 CSS 属性选择器 `[appHighlight]`

2. 从 `@angular/core` 导入 `ElementRef`。`ElementRef` 的 `nativeElement` 属性会提供对宿主 DOM 元素的直接访问权限

3. 在指令的 `constructor()` 中添加 `ElementRef` 以[注入](https://angular.cn/guide/dependency-injection)对宿主 DOM 元素的引用，该元素就是 `appHighlight` 的作用目标

4. 向 `HighlightDirective` 类中添加逻辑，将背景设置为黄色

   ```TS
   import { Directive, ElementRef } from '@angular/core';
   
   @Directive({
     selector: '[appHighlight]'
   })
   export class HighlightDirective {
       constructor(private el: ElementRef) {
          this.el.nativeElement.style.backgroundColor = 'yellow';
       }
   }
   ```

5. 要使用 `HighlightDirective`，请将 `<p>` 元素添加到 HTML 模板中，并以伪指令作为属性

   ```TS
   <p appHighlight>Highlight me!</p>
   ```

   Angular 会创建 `HighlightDirective` 类的实例，并将 `<p>` 元素的引用注入到该指令的构造函数中，它会将 `<p>` 元素的背景样式设置为黄色



------

### 结构型命令



结构指令是通过添加和删除 DOM 元素来更改 DOM 布局的指令

Angular 提供了一组内置的结构指令（例如 `NgIf` 、 `NgForOf` 、 `NgSwitch` 等），在所有 Angular 项目中通用。有关更多信息，请参阅[内置指令](https://angular.cn/guide/built-in-directives)

应用结构指令时，它们通常以星号 `*` 为前缀，例如 `*ngIf`。本约定是 Angular 解释并转换为更长形式的速记。Angular 会将结构指令前面的星号转换为围绕宿主元素及其后代的 `<ng-template>`

例如，让我们采取以下代码，如果 `hero` 存在，则使用 `*ngIf` 来显示英雄的名字：

```TS
<div *ngIf="hero" class="name">{{hero.name}}</div>
```

不简写的话则需要这么写

```TS
<ng-template [ngIf]="hero">
  <div class="name">{{hero.name}}</div>
</ng-template>
```



------

## 依赖注入





依赖注入（DI）是 Angular 中的基本概念之一。DI 被装配进 Angular 框架，并允许带有 Angular 装饰器的类（例如组件、指令、管道和可注入对象）配置它们所需的依赖项

DI 系统中存在两个主要角色：依赖使用者和依赖提供者



**提供依赖项**

假设有一个名为 HeroService 的类需要用作组件中的依赖项

第一步是添加 `@Injectable` 装饰器以表明此类可以被注入

```TS
@Injectable()
class HeroService {}
```

下一步是提供它，以便让其在 DI 中可用。可以在多种地方提供依赖项

如果是在组件级别

```TS
@Component({
  selector: 'hero-list',
  template: '...',
  providers: [HeroService]
})
class HeroListComponent {}
```

如果是 NgModule 级别

```TS
@NgModule({
  declarations: [HeroListComponent]
  providers: [HeroService]
})
class HeroListModule {}
```

如果是应用程序级别

```TS
@Injectable({
  providedIn: 'root'
})
class HeroService {}
```



**注入依赖项**

注入依赖项的最常见方法是在类的构造函数中声明它。当 Angular 创建组件、指令或管道类的新实例时，它会通过查看构造函数的参数类型来确定该类需要哪些服务或其他依赖项。例如，如果 `HeroListComponent` 要用 `HeroService`，则构造函数可以如下所示：

```TS
@Component({ … })
class HeroListComponent {
  constructor(private service: HeroService) {}
}
```

