# Create Project 
## Cli Command
- `ng version`
- `ng new <project_name>`
- `ng serve`
## Structure
- scr/app 组件代码
- arc/assets 静态资源
- src/envirionments 配置文件
- src/main.ts 程序入口，初始化**根模块 AppModule**
## webpack
Angular 运行项目时会生成多个bundle文件，并注入到 index.html 中

![assets/Angular Tutorial_image_1.png](assets/Angular%20Tutorial_image_1.png)

# Typescript OOP
参考 [[TypeScript#Class & OOP]]
## Type Assertions
两种方式：
```ts
let message;
message = "abc"
let endsWithC = (<string>message).endsWith("c")
// or
let endsWithC = (message as string).endsWith("c")
```
## Interface
接口，用于**声明**一个对象的类型，用于描述对象应该具有哪些属性、方法以及它们的类型
接口不能包含任何**实现**
```ts
interface Point{
	x: number,
	y: number,
	draw: () => void
}
```
## Class
类是一种模板，用于创建具有相同属性和方法的对象。类可以包含属性、方法和构造函数，并且可以通过实例化来创建对象
和 interface 的联系：类可以实现一个或多个接口
类是用来实例化对象的，而接口只是对对象的描述
```ts
/**以下例子仅仅是演示类实现接口
	可以看出接口是用来强制类型检测的
*/
class PointClass implements Point{
	x: number;
	y: number;

	draw(){
		console.log(this.x, thos)
	}
	getDistance(){
		console.log("")
	}
}
```

### Constructor 
- 参数可以使用 `?` 设置为 Optional
- 参数可以设置 访问修饰符 
	- public
	- private
	- protected
- 使用访问修饰符的构造器变量，在实例化时对象时编译器自动生成

> 访问修饰符不仅可以在constructer 中使用，它可以修饰任意类变量

### Property
使用 get set 关键字可以将方法设置成属性
```ts
class Person {
  constructor(private _name: string){
  }
  get name(): string {
    return this._name;
  }
  set name(name: string) {
    this._name = name;
  }
}
```
# Component 
## Concept
一个组件应该包括
- Data
- HTML template
- Logic

Angular中的 Module 模块是多个有关联的组件的容器
## Usage
1. 创建一个组件
2. 将组件注册在一个模块中
3. 在HTML中添加渲染这个组件
## Create a component
- 创建一个类并导出
- 使用装饰器函数 `@Component` 修饰类
- 装饰器的参数是一个对象，包括：
	- `selector: string` CSS 选择器，指定渲染该组件的元素
	- `template: string`  组件渲染的HTML内容
- 在对应的 Module 的 `declaration` 中添加组件
- 在需要渲染组件的HTML模板中添加 匹配CSS选择的元素

使用 Cli 创建组件
`ng g c course`  会创建4个文件：

 ![assets/Angular Tutorial_image_2.png](assets/Angular%20Tutorial_image_2.png)

并且自动将这个组件更新到根组件 AppModule 中

## Template
在组件对应的 template 中，可以直接使用组件实例的属性和方法
- 使用 `{{ content }}` 进行字符串插值，content 可以是JS变量或表达式
- 使用指令增减或改变DOM元素，同样，指令可以引用属性或方法

## Service
组件要使用服务，首先要对服务类进行实例化，使用 **依赖注入** 可以让 Angular 来**实例化**，并且它会对实例化的服务进行管理

如何实现：
1. 服务类使用 `@Injectable` 装饰
2. 在模块的 `providers` 中注册服务类
3. 在组件的构造器方法的参数中添加所需要的服务和它的类型，Angular会感知到参数中有一个服务类型，于是会自动实例化

```ts
@Component({
  selector: 'app-author',
  templateUrl: './author.component.html',
  styleUrls: ['./author.component.css'],
})
export class AuthorComponent {
  authors: string[];
  constructor(author: AuthorService) {  // 编译器感知到参数类型为AuthorService
    this.authors = author.getAuthors();
  }
}
```

> @Injectable
> 1. 当服务类的构造器也需要依赖注入时，就需要@Injectable装饰
> 2. 为什么组件不需要？因为@Component内置了@Injectable

使用依赖注入让 Angular 自动管理服务实例还有一个好处，就是实现**单例模式**
- 在Module级别指定服务，服务实例会在Module的所有组件中共享
- 在根级别指定服务，服务实例会在整个App中共享

使用 Cli 创建服务
`ng g s email`  会创建2个文件：一个服务类文件和一个测试文件，但是不会注册该服务

## Module
模块是提供相对独立功能的代码集合，包括：组件、服务、指令、管道等
模块的元数据：
- Imports 该模块依赖的其他**模块**
- Exports 该模块给其他模块暴露的组件、服务、指令、管道、模块等
- Declarations **该模块自己**创建的组件、指令、管道（这样模板才能识别）
- Providers 该模块在依赖注入中使用的服务

注意事项：
- 如果导入模块是为了使用其中的服务，那么在根模块中导入一次即可，比如 HttpClientModule
- 如果导入模块是为了使用其中的组件、指令、管道，需要在每个需要的模块中进行导入，比如 FormsModule

# Display Data 
## Property Binding
将 DOM 元素的属性 property 与组件实例中的属性绑定起来
写法：`<img [src]="imageUrl"/>`
- 属性名用 `[] `
- 属性值用双引号 `""` 
- 注意和上面提到的字符串插值语法区分
- 绑定是**单向**的，当组件实例中的数据更新，会导致DOM属性更新；反之不然
## Attribute Binding
和 Property Binding 类似，但是绑定的是 HTML 元素的属性，而不是DOM元素
大多数情况使用不到
写法：`<td [attr.colspan]="colSpan"/>`
## Class Binding
给元素的类属性添加或减少某个类
写法：`<button class="btn" [class.active]="isActive"> save </button>`
- 属性名用 `[class.类名] `
- 属性值用双引号 `""`  值为 boolean 类型，表示这个类有或没有
## Style Binding
写法和 Class Binding 类似，用于给元素的style属性添加样式和对应值
写法：`<button [style.backgroundColor]="isActive ? 'blue' : white"> save </button>`
## Event Binding
给元素的事件绑定回调函数
写法：`<button (onclick)="onSave($event)"> save </button>`
- 事件名用小括号 `()`
- 回调函数要调用，和 React 不一样
- 如果需要引用事件对象，使用 `$event`
- 在回调函数中，可以调用 `$event.stopPropagation()` 阻止事件冒泡
## Event Filtering
可以给事件添加过滤条件
如监听所有的 keyup 事件，但是只对 enter 进行处理
写法：`<input (keyup.enter)="onEnter($event)"/>`
## Template Variables
获取触发事件的元素的值，比如 input 元素的输入值，可以使用原始方式，通过事件对象 `$event.target.value` 获取
Angular 还支持在模板的元素中用`#`定义一个变量，称为模板变量，它可以引用模板中的元素
类似 React 中的 Ref
写法：`<input #email (keyup.enter)="onEnter(email.value)">`

模板变量引用的对象：
- 如果在组件上声明变量，该变量就会引用该组件实例
- 如果在标准的 HTML 标记上声明变量，该变量就会引用该元素
- 如果在 `<ng-template>` 元素上声明变量，该变量就会引用一个 `TemplateRef` 实例来引用此模板
- 如果给在右侧指定了一个名字，那么该变量就会引用所在元素上具有这个 `exportAs` 名字的指令或组件：
```html
<!-- > 引用 FormControl 组件 <-->
<input [(ngModel)]="myValue" #myInput="ngModel">
```

## Two-way Binding 
Angular 是基于OOP思想的，所以上述使用模版变量传递值的方式并不好，组件实例应该自己能够获取到 HTML 中的最新值，同时 HTML 能够跟随实例变量的变化为更新，这才符合组件：模板、数据、逻辑一体的原则
可以通过 数据的双向绑定实现 
双向绑定是指：绑定实例对象的属性和一个表单元素的值
写法：
- 使用 `[(ngModel)]` 表示使用双向绑定
- 使用 `""` 指定要绑定的实例的属性名
- 需要导入 `FormsModule` 模块才能使用
```ts
@Component({
	selector: "courses",
	template: `
		<input [(ngModel)]="email" (keyup.enter)="onEnter() >
	`
})
export class CoursesComponent{
	email = "me@example.com"
	onEnter(){
		console.log(this.email)
	}
}
```
## Pipe
Pipe 是一种用于转换数据的机制，接收输入数据，输出转换后的数据。Pipe 可以用于格式化、过滤、排序等场景，可以大大简化代码。
Angular 提供了一些内置的 Pipe：
- `DatePipe`：用于格式化日期
- `UpperCasePipe`：将字符串转换为大写字母
- `LowerCasePipe`：将字符串转换为小写字母
- `DecimalPipe`：用于格式化数字
- `CurrencyPipe`：用于格式化货币
- `PercentPipe`：用于格式化百分比

写法：`<p>{{ 'hello world' | uppercase }}</p>`
- 在模板中使用 `|` 管道符号连接值和 Pipe
- 多个 Pipe 可以串联使用
- Pipe 可以接受参数，用冒号指定 `{{ birthday | date:"MM/dd/yy" }}`
- 在组件中也可以使用管道，需要依赖注入
- 内置组件不需要注册

```ts
@Component({
  selector: 'app-root',
  template: `
    <p>{{ name }}</p>
    <p>{{ name | uppercase }}</p>
  `
})
export class AppComponent {
  name = 'hello world';

  constructor(private upperCasePipe: UpperCasePipe) { // 注入
    console.log(this.upperCasePipe.transform('hello world')); // 输出 'HELLO WORLD'
  }
}
```

## Custom Pipe
在 Angular 中，管道的实现方式是创建一个 Pipe 类，并实现 `PipeTransform` 接口中的 `transform` 方法

```ts
@Pipe({
	name: "summary"
})
export class SummaryPipe implements PipeTransform{
	transform(value: string, limit ?: number){
		if(!value)
			return null
		let actualLimit = limit ? limit : 50
		return value.substring(0, actualLimit) + "..."
	}
}
```

- 自定义的 Pipe 需要在模块的 declaration 中注册
- 使用：`<p>{{ text | summary:10 }}</p>`

cli 创建：
- `ng g p title-case`
- 自动在根模块中注册

# Re-useable Component
## Component API

对于一个组件，与外界交互，主要是两方面：从外界接收数据（输入）、像外界抛出事件（输出）
在Angular中，组件输入通过 property binding 实现，给元素的输入属性绑定值；组件输出通过抛出事件实现
暴露给外界的输出和输出就是组件的 public API 
![assets/Angular Tutorial_image_3.png](assets/Angular%20Tutorial_image_3.png)
## Input Property
将一个组件的属性标记为输入属性，外界在渲染该组件时，可以通过 DOM 属性绑定传值
```html
<!--> 在 app 组件中，渲染 favorite 组件， post 是 app 组件的实例属性 <-->
<!--> isFavorite 是 favorite 组件定义的输入属性 <-->
<favorite [isFavorite]="post.isFavorite"> 
```

两种方式：
1. 使用 `@Input()` 装饰器将类属性标记为**输入属性** （常用）
2. 在 `@Component` 参数的的 `inputs` 中添加输入属性名

设置别名：
可以给输入属性设置别名，让它更符合HTML属性的命名规范；也可以保证在属性名称变化以后，对外的输入接口仍然不变
写法：`@input("is-favorite")`

## Output Property 
父组件调用子组件时，可以给子组件的**自定义事件**绑定回调函数
这个自定义事件是一个 `EventEmitter`  实例变量，需要设置成 **输出属性**
```html
<!--> 在 app 组件中，渲染 favorite 组件
post 是 app 组件的值，onFavoriteChange是 app 组件的方法  <-->

<favorite [is-favorite]="post.isFavorite" [change]="onFavoriteChange($event)"> 

<!--> isFavorite 是 favorite 组件定义的输入属性 
change 是 favorite 组件定义的事件，它是输出属性<-->
```

参数传递：
- 输出属性是一个 `EventEmitter` 实例，用这个实例抛出事件等价于调用绑定给它的回调元素
- 绑定的回调函数中的 `$event` 表示 `emit` 传出的值，可以是任何值

```ts
// 装饰器部分省略
export class FavorateComponent{
	@Input("is-favorite") isFavorate: boolean;
	@Output() change = new EventEmitter() // 输出属性是一个 EventEmitter 实例
	
	onClick(){
		this.isFavorate = !isFavorate
		this.change.emit(this.isFavorate) // emitter 抛出事件，调用绑定的回调
	}
}
```

数据流通过程：
子元素上监听到DOM事件，比如点击 -> 调用事件绑定的回调函数 onClick -> 在函数中使用某个输出属性抛出自定义事件 -> Angular 调用该属性绑定的父组件上的方法

设置别名：
同样可以给输出属性设置别名，给装饰器传递参数即可

## View Encapsulation
在Angular中，使用了类似于 Shadow DOM 的概念
在 Shadow DOM 中，每个组件都有一个独立的 DOM 树和样式环境，组件内部的样式只会影响组件内部的 DOM 结构，不会影响外部文档中的其他元素

Shadow DOM 在浏览器环境的使用：

![](assets/Angular%20Tutorial_image_4.png)

在 Angular 中可以使用组件的元数据（`@Component`的参数）中的 `encapsulation` 属性来控制是否隔离组件，有三个选项：
- `Emulated`（默认选项）：使用类似于 Shadow DOM 的方式来模拟样式的隔离（因为有些浏览器不支持 Shadow DOM），在这种模式下，Angular 会将组件的样式转化为带有唯一前缀的 CSS 类，从而避免与其他组件的样式发生冲突
- `Native`：使用浏览器原生的 Shadow DOM 特性来实现组件的样式隔离，在这种模式下，组件的样式只能作用于组件内部，不会影响外部元素
- `None`：不进行样式隔离，在这种模式下，组件的样式会影响整个应用程序

## ngContent
`ng-content` 指令可以在组件模板中定义一个插槽 / 占位符
如果一个模板中有多个插槽，需要设置CSS选择器 `select` 进行区分
```html
<header>
  <!-->两个插槽，设置选择器为属性选择器 <-->
  <ng-content select="[slot=title]"></ng-content>
  <ng-content select="[slot=subtitle]"></ng-content>
</header>
```
父组件在渲染时可以指定子组件元素的**嵌套内容**，符合选择器的子元素会被插入到对应插槽中
```html
<app-header>
  <!--> 在子元素中写嵌套内容 <-->
  <h1 slot="title">Welcome to my app!</h1>
  <p slot="subtitle">This is a sample app built with Angular.</p>
</app-header>
```
## ngContainer
`ng-container` 是一个用于在模板中创建一个逻辑容器，它不会渲染任何元素，只会渲染其内容；常用于和结构性指令配合生成DOM
类似 React 中的 Fragment
```html
<!-->此处避免出现一层 div 标签 <-->
<ng-container *ngIf="condition"> </ng-container>
```

## ngTemplate
用于定义一个模板块，可以使用模板变量给它命名并引用

## Life Circle Hooks
1. `constructor` 不属于Hook，在类被实例化时执行一次，一般只在其中操作服务实例
2. `ngOnChanges` 输入属性变化时
	- 首次初始化时也会被调用
	- 接收一个 `SimpleChanges` 对象，可以获取**当前改变的**输入属性的值、过去值、是否第一次改变
	- 对于基本类型的改变，都可以被监测
	- 对于引用类型，只能监测到引用地址的改变，对于同一个引用，其内部值的变化，监测不到；但是不影响页面的数据更新渲染
3. `ngOnInit` 组件类初始化时，在其中可以安全地使用组件的属性和方法
4. `ngDoCheck` 当Angular检测到组件发生变化时调用（脏值检测），和`ngOnChanges`有重叠的功能，但是它能检测到的组件的状态的变化更多（比如引用对象内部的变化）
5. `ngAfterContentInit` 在组件的**内容**投影（ng-content 嵌套内容）初始化完成后调用
6. `ngAfterContentChecked` 在组件的内容投影发生变化（脏值检测）时被调用
7. `ngAfterViewInit` 在组件的**视图**初始化完成后调用（组件、子组件、嵌套内容都渲染完成）
8. `ngAfterViewChecked` 在组件的视图发生变化（脏值检测）时被调用
9. `ngOnDestroy` 组件被销毁时

# Directives
指令是没有模板的组件，或者说组件是有模板的指令

指令的两种类型：

![](assets/Angular%20Tutorial_image_5.png)

## ngIf
根据条件来添加或移除 HTML 元素
写法：在元素上使用 `*ngIf="condition"` 当 condition 为真时渲染该元素

还可以使用 `then` 或 `else` 子句实现更复杂的逻辑，此时需要使用 `ng-template` 充当容器
`ng-template` 中使用模板变量引用该元素，它本身不会被渲染，只会渲染它的子元素
```html
<div *ngIf="condition; else elseBlock">Hello, World!</div>
<ng-template #elseBlock>Goodbye, World!</ng-template>
```

```html
<div *ngIf="condition; then thenBlock else elseBlock"></div>
<ng-template #thenBlock>Hello, World!</ng-template>
<ng-template #elseBlock>Goodbye, World!</ng-template>
```

## hidden property
在 HTML 中，`hidden` 是一个布尔属性，用于将元素隐藏，当一个元素设置了 `hidden` 属性时，该元素不会在页面上显示，**但它仍然存在于 DOM 树中**，可以通过 JavaScript 访问和操作
因此在Angular中可以通过属性绑定隐藏元素：
```ts
<div [hidden]="condition">Hello, World!</div>
```


> [!Tip] ngIf vs hidden
> 1.  `ngIf` 可以将元素从DOM中移除，而 `hidden` 仅仅是不展示
> 2. 对于层级多的树，使用 `ngIf` 把整个树移除对性能更好
> 3. 但是如果这个树重建的开销很大、并且存在隐藏和显示的操作频繁（比如 toggle ），那么使用 `hidden` 更合适

## ngSwitchCase
`ngIf` 指令用于处理只有两个条件的情况：真或假，`ngSwitchCase` 用于处理条件更多的情况
下面示例用 `ng-container` 避免多余层级的 div
```html
<ng-container [ngSwitch]="viewMode">
  <div *ngSwitchCase="'map'">Map mode content</div>
  <div *ngSwitchCase="'list'">List mode content</div>
  <div *ngSwitchDefault>Default content</div>
</ng-container>
```

## ngFor
用于在模板中循环遍历数组或对象来生成多个元素
```html
<!-- 循环渲染数组 -->
<ul>
  <li *ngFor="let item of items">{{ item }}</li>
</ul>
<!-- 循环渲染对象 -->
<ul>
  <li *ngFor="let key of Object.keys(obj)">
    {{ key }}: {{ obj[key] }}
  </li>
</ul>
```

Angular 支持一些导出变量：
- `index: number`: The index of the current item in the iterable.
- `count: number`: The length of the iterable.
- `first: boolean`: True when the item is the first item in the iterable.
- `last: boolean`: True when the item is the last item in the iterable.
- `even: boolean`: True when the item has an even index in the iterable.
- `odd: boolean`: True when the item has an odd index in the iterable.

```html
<li *ngFor="let user of users; index as i; first as isFirst">
  {{i}} / {{users.length}}. {{user}} <span *ngIf="isFirst">default</span>
</li>
```

在指令中，`trackBy` 是一个可选的函数，用于指定 Angular 跟踪列表中的元素的方式，默认情况下是根据引用跟踪，当新的对象引用和旧的不一致时，就更新元素（即使前后内容可能一致）
当使用 `trackBy` 函数时，Angular 会根据 `trackBy` 函数返回的值来跟踪每个元素，而不是根据元素的引用，只有当这个值更新了，才更新页面元素
```html
<ul>
  <li *ngFor="let item of items; trackBy: trackById">{{ item }}</li>
</ul>
```

```ts
trackById(index: number, item: any) {
  return item ? item.id : undefined;
}
```

## ngClass
使用前述的 class binding 在绑定多个类时比较繁琐，可以使用 `ngClass` 简化
它绑定一个对象
```html
<button 
	[ngClass]="{ 
		'isActive' : false, '
		btn' : true
	}"
> save </button>
```

## ngStyle
使用前述的 style binding 在绑定多个类时比较繁琐，可以使用 `ngStyle` 简化
它绑定一个对象
```html
<button 
	[ngStyle]="{
		'backgroundColor': canSave ? 'blue' : 'grey',
		'color': canSave ? 'white' : 'black'
	}"
> save </button>
```

## Custom Directive
使用 cli 创建指令：
`ng g d input-format` 自动在根模块注册

`ElementRef`：
- 是一个服务，用于获取模板根元素的引用
- 使用时，在对应类的构造函数中注入服务，并调用`nativeElement` 就能获取当前元素的引用

`@HostListener`：
- 装饰器，用于监听宿主元素的事件
- 接受一个字符串参数，表示事件名称

```ts
@Directive({
	selector: [appInputFormat]
})
export class InputFomatDirective {
	constructor(private el: ElementRef){ // 注入
	}
	@HostListener("blur") onBlur() { 
		let value: string = this.el.nativeElement.value
		this.el.nativeElement.value = value.toLowercase() // 改变DOM
	}
}
```

使用：在需要使用指令的元素上声明 selector 
```html
<input type="text" appInputFormat />
```

实现机制：
Angular 会根据指令的选择器 selector 和元数据信息，找到所有符合选择器规则的 DOM 元素，并为每个元素创建一个指令实例

指令如何接收参数：
指令本质上是一个没有模板的组件，也可以设置 **输入属性**来接收参数
```ts
@Directive({
	selector: [appInputFormat]
})
export class InputFomatDirective {
	@Input("appInputFormat") format: string // 输入属性
	constructor(private el: ElementRef){ // 注入
	}
	@HostListener("blur") onBlur() { 
		let value: string = this.el.nativeElement.value
		if(this.format === "lowercase")
			this.el.nativeElement.value = value.toLowerCase() // 改变DOM
		else
			this.el.nativeElement.value = value.toUpperCase() // 改变DOM
	}
}
```

```html
<input type="text" [appInputFormat]="uppercase" />
```

指令也有生命周期

# DOM 
## @ViewChild
`@ViewChild` 是一个属性装饰器，用于在**组件类**中获取模板中的元素、组件或指令
> 动态查询的引用，要在 `ngAfterViewInit` 钩子函数中使用最安全

元数据对象参数：
- selector 选择器
- read 用于从查询的元素中返回的是指定的属性或方法
- static 布尔值，是否在静态查询
选择器支持的类型：
- 组件、指令、服务
- 字符串形式的模板变量、字符串形式的 ng-template 引用
- 通过字符串令牌定义的提供者

> [!Question] 静态查询和动态查询
> - 静态查询在编译时解析，其结果在组件创建之前确定，查询一次，结果会被存储，后续对相同查询的请求将返回相同的结果。因此静态查询比动态查询更快、更高效，不需要进行变更检测
> - 动态查询在运行时解析，结果可能在组件的生命周期内发生变化，动态查询比静态查询更慢，因为它们需要进行变更检测
> - 一般没有绑定结构性指令的元素使用静态查询，否则使用动态查询
> - 如果要查询的元素是在  `ngAfterViewInit` 生命周期钩子中才创建，也要使用动态查询
>

## @ViewChildren
`@ViewChild` 只匹配符合选择器的第一个元素，如果想选择所有匹配的元素，使用 `@ViewChildren`
它返回的类型是 `QueryList<>` ，需要使用它上面的 `toArray` 方法才能获取到引用数组

## ElementRef
是一个服务，用于获取模板根元素 / 宿主元素的引用
使用时，在类的构造函数中注入服务，并调用 `nativeElement` 就能获取当前元素的引用
常用于在自定义指令中获取到指令的宿主元素
参考[[#Custom Directive]]

## Renderer2
用于在组件类中操作 DOM 元素的服务
它并不直接操作 DOM 元素，而是通过底层的渲染引擎来执行 DOM 操作，可以兼容不同的渲染引擎，并且做了一些安全上的加强

> [!Important] 最佳实践
> 所有DOM的操作都应该使用 Renderer2 实现

[支持的API](https://angular.cn/api/core/Renderer2)
```ts
export class AppComponent implements OnInit {
  constructor(private renderer: Renderer2, private el: ElementRef) { }
  ngOnInit() {
    const container = this.el.nativeElement.querySelector('#container');
    const newElement = this.renderer.createElement('p');
    const text = this.renderer.createText('Hello, world!');
    this.renderer.appendChild(newElement, text);
    this.renderer.appendChild(container, newElement);
  }
}
```

## @HostListener
方法装饰器，用于监听宿主元素的一个事件，并将它修饰的方法作为事件的回调函数
装饰器接收两个参数：
- DOM事件名称
- 配置对象
	- capture 使用捕获阶段，默认 false，使用冒泡
	- passive 被动监听，默认 false
	- once 事件是否仅触发一次，默认 false

## @HostBinding
属性装饰器，用于将**类属性**绑定到**宿主元素的属性**，当类中的属性变化会引起元素属性的变化
装饰器接收一个参数，表示要绑定的元素的属性名称
```ts
export class ExampleComponent {
  @HostBinding('class.highlight')
  isHighlighted = false;
}
```
# Template-driven Form
控制 Form 的两种方式：
- 在模板中控制
- 在类中控制

![](assets/Angular%20Tutorial_image_6.png)

使用模板驱动型表单需要在组件模块中导入 `FormsModule `模块

## ngModel 
指令的作用是创建一个 `FormControl` 实例 ，并绑定在输入元素上
- 使用`[(ngModel)]="username"`的语法，可以实现数据的双向绑定
- 也可以单独使用 `ngModel` 指令 
- 两种语法都会在输入元素上创建一个 `FormControl` 实例
- Form 中的每个输入框需要指定 `name`，用于区别同一个 Form 中的不同输入框
- 通过模板变量可以获取到 Angular 创建的 `FormControl` 实例，它会随着输入元素的变化而更新
```html
<!--> username 变量为 FormControl 实例的引用 <-->
<input ngModel name="username" type="text" #username="ngModel">
```

## Validation
在输入元素上可以直接添加 HTML5 支持的 Validation 属性：
- `required` 
- `minlength="3"`
- `maxlength="10"`
- `pattern="regExp"`

`FormControl` 实例上常用的数据
- `username.valid ` 输入是否有效
- `username.touched` 是否输入过
- `username.errors` 一个对象，信息很丰富，可以打印出来看看

## Style 
Angular 会给根据输入元素的状态自动添加类
- `form-control `
- `ng-touched`
- `ng-invalid`
- `ng-dirty`
可以使用选择器给非法输入添加样式：
```css
.form-control.ng-touched.ng-invalid {
	border: 2px solid red
}
```

## ngForm
- Augular 会自动给 `form` 元素添加 `ngForm` 指令，这个指令创建一个 `FormGroup` 实例，使用模板变量引用这个实例
- 例外：设置了 `ngNoForm` 或 `FormGroup` 属性的元素不会再添加
-  `FormGroup` 有一个输出属性 `ngSubmit` 可以指定提交表单的回调，实例上的 `value` 属性可以获取到表单值，它是一个对象

```html
<form #f="ngForm" (ngSubmit)="submit(f)"> </form>
```

```ts
export class AppComponent {
     onSubmit(form: NgForm) {
       console.log(form.value) //  对象
     }
}
```

## ngModelGroup
- 可以把表单分为多个组，每个组用一个容器元素表示，给元素上添加 `ngModelGroup` 指令
- 会在输入元素上创建一个 `FormGroup` 实例，但是没有输出属性 `ngSubmit`
- 每个组的表单值单独形成一个 Object

```html
<form #f="ngForm" (submit)="onSubmit(f)">
     <div ngModelGroup="user">
       <input type="text" name="username" ngModel />
     </div>
     <div ngModelGroup="contact">
       <input type="text" name="phone" ngModel />
     </div>
     <button type="submit" [disable]="!f.valid">提交</button>
</form>
```

## ngValue
用于处理 `select` 下拉框的选择项 `option` 所绑定的值
`option` 的 `value` 只能是一个字符串，无法绑定更复杂的对象，使用 `ngValue` 可以绑定复杂对象
当使用 `ngValue` 指令时，`value` 属性将被忽略
```html
<select ngModel name="contact">
	<option value="">...</option>
	<!--> 这里的 contact 是一个复杂对象 <--> 
	<option *ngFor="let contact of contacts" [ngValue]="contact">
		{{contact.name}}
	</option>
</select>

```

# Reactive Forms
使用 reactive forms 需要在组件模块中导入 `ReactiveFormsModule `模块
## Create Controls
在组件类中，使用 `FormGroup` 和 `FormControl` 来创建表单控件
 `FormGroup` 接收一个对象参数，每一项为一个 `FormControl`  实例
```ts
export class SignupFormComponent {
	form = new FormGroup({
		username: new FormControl(),
		password: new FormControl()
	})
}
```

在模板中给元素绑定 `FormGroup` 和 `FormControl` 实例
```html
<form [formGroup]="form">
	<input  formControlName="username" type="text"/>
	<input  formControlName="password" type="password"/>
</form>
```

## Validation
`FormControl` 构造器接收两个三个可选的参数：
- 表单控件初始值
- 验证方法（一个方法，或者方法的数组）
- 异步验证方法
- 更新配置，用于配置表单控件的更新行为

可以使用 `Validators` 类中的静态方法来添加验证规则
```ts
export class SignupFormComponent {
	form = new FormGroup({
		username: new FormControl('', [Validators.required, Validators.minLength(3)]),
		password: new FormControl('', Validators.required)
	})
}
```

在模板中通过 `get` 方法获取 `FormControl` 实例
```html
<div *ngIf="form.get('password').touched && form.get('password').invalid">Password is required </div>
```

## Custom Validation
validators 函数的接口：

![](assets/Angular%20Tutorial_image_7.png)
- 接收一个 `AbstractControl` 类型的参数
- 返回一个 `ValidationErrors` 类型值

> [!Tip] AbstractControl
> FormControl 和 FromGroup 都是它的实现，所以 validator 可以在二者上使用

![](assets/Angular%20Tutorial_image_8.png)
```ts
// 新建一个 validators 文件
export class UsernameValidators {
	// 编写 validator 函数
	static cannotContainSpace(control: AbstractControl): ValidationErrors {
		if((control.value) as string).indexOf(" ") >= 0)
			return {cannotContainSpace : true}
		return null
	}
}
```

## Async Validators
`FormControl`  的第三个参数接收异步验证器
异步验证函数需要返回一个 `Promise` 或 `Observable`

![tmp1691932573114_Angular Tutorial_image_1](assets/Angular%20Tutorial_image_9.png)

Promise 实例在异步操作结束后要返回一个 `ValidationErrors` 或者 `null`
```ts
export class AsyncValidators{
	static shouleBeUnique(control: AbstractControl): Promise(ValidationErrors | null){
		return new Promise((resolve, reject) => {
			setTimeout(() => {
				if(control.value === "mosh")
					resolve({shouleBeUnique : true})
				else resolve(null)
			}, 200)
		})
	}	
}
```

Oberverble 同理
```ts
export class AsyncValidators{
	static shouleBeUnique(control: AbstractControl): Promise(ValidationErrors | null){
		return new Observerable((observer) => {
			setTimeout(() => {
				if(control.value === "mosh")
					observer.next({shouleBeUnique : true})
				else observer.next(null)
			}, 200)
		})
	}	
}
```

当异步验证器的结果没有返回时，使用它的 `FormControl` 上的 `pending` 属性为 `true`，可以使用这个属性显示 loading

## Validating Upon Submiting 
`AbstractControl` 上有一个 `setErrors` 方法，可以手动给表单添加错误信息，它的参数也是`ValidationErrors`，使用这个方法可以在提交表单的回调函数中编写判断逻辑

```ts
myControl.setErrors({ required: true, minLength: { requiredLength: 5, actualLength: 3 } });
```

## Nested FormGroups
在一个 FormGroup 对象中可以嵌套一个 FormGroup 用于对表单控件分组：
```ts
export class SignupFormComponent {
	form = new FormGroup({
		account: new FormGroup({
			username: new FormControl(''),
			password: new FormControl('')
		})
	})

	get username() {
		return this.form.get("account.username")
	}
}
```

在模板中，要在属于一个组的控件的容器上指定 `formGroupName`
```html
<form [formGroup]="form">
	<div formGroupName="account">
		<input  formControlName="username" type="text"/>
		<input  formControlName="password" type="password"/>
	</div>
</form>
```

## FormArray
`FormArray` 也是继承自 `AbstractControl` ，除了值管理、验证等，它还具有其他功能
它的属性：
- controls：一个包含所有子控件的 `AbstractControl` 数组
- length：数组长度
- value：一个包含所有子控件的值的数组
方法：
- push：在数组末尾添加一个子控件
- removeAt：删除索引位置的子控件
- at：返回索引位置的子控件
- insertAt：在索引位置添加子控件

```ts
export class CourseFormComponent{
	form = new FormGroup({
		topics: new FormArray([]) // 创建一个FormArray
	})
	get topics(){
		return this.form.get("topics") as FormArray
	}
	// 在 FormArray 中添加 control 
	addTopics(topic: HTMLInputElement){
		this.topics.push(new FormControl(topic.value))
		topic.value = ""
	}
	// 在 FormArray 中删除 control 
	removeTopics(topic: FormControl){
		let index = this.topics.controls.indexOf(topic) // 注意
		this.topics.removeAt(index)
	}
}
```

```html
<form [formControl]="form">
  <input type="text" (keyup)="addTopic(topic)" #topic/>
  <ul>
    <li *ngFor="let topic of topics.controls)" (click)="remove(topic)">
      {{topic.value}}
    </li>
  </ul>
</form>
```

## FormBuilder
创建表单的简写方式

# HTTP
需要在根模块中导入 `HttpClientModule`，在所有的组件中都能使用其中的服务
## Getting Data
```
```
使用HTTP服务发送请求

# Route

# Authentication





