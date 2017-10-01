# 架構
導讀：[4. Architecture](https://angular.io/docs/ts/latest/guide/architecture.html)


Angular 是一個前端的類 MVC 架構，AngularJS 作為當初最先進的前端架構之一不得不面臨一次革命性的更新，因此 Angular 在整體結構上和 AngularJS 有非常大的變化，如果你沒有 AngularJS 的包袱，那學習上可以免去一些觀念上的轉換。下圖是官方用來闡述 Angular 整體架構的範例圖。


![Angular Architecture](https://angular.io/generated/images/guide/architecture/overview2.png)

從技術堆疊（technology stack）上來說 Angular 和 AngularJS 最大的差異主要來自於 Angular 全面使用 TypeScript 作為開發階段的語言（最終仍然會編譯為 JavaScript）。由 TypeScript 所帶來的最明顯的變化就是模組化，由 AngularJS 的只需要匯入一個 JS 當可以開始開發執行到，目前 Angular 把不同功能拆分成不同 TypeScript / JavaScript 模組（@angular/core, @angular/forms……）。


## 模組
### Angular 的模組系統
Angular 有自身的一套**模組系統（ Module ）**，有別於我們熟悉的 **Javascript 模組系統**。JavaScript 模組是以檔案為單位，每個檔案唯一個模組，所有內部所宣告的項目都屬於該模組，若要公開某些項目則使用 `export` 輸出。相對的， Angular 模組是透過`@NgModule`所修飾的一個 class ，除了 `imports` 和 `exports` 之外，還可以在模組內指名宣告的項目（declarations）和註冊（pproviders）的服務。
```
Module
|--declarations
   |--Component
   |--Directive
   |--Pipe
|--providers
   |--Service
|--imports
   |--Module
|--exports
   |--Module
```

- Component、Directive、Pipe 需要在模組內「宣告」``` @NgModule({ declarations: [] }) ```，經宣告後，該模組內的所有模板就可以自由地使用。
- Service 需要在模組內「註冊」``` @NgModule({ providers: [] })```，經註冊後，服務就會被 Injector 接管，當模組內或**模組外**有需要使用相依性注入時， Injector 就會自動判斷並注入對應的依賴目標。
- Module 可以透過```imports```另一個 Module 來使用該模組內「宣告」和「註冊」的項目，但是謹記被匯入的模組必須在自身的定義中匯出相關的「宣告」和「匯入」，則匯入後的模組才能正確使用這些東西，但「註冊」服務則不需要特別匯出，因為 Injector 會自動接管。

  ```javascript
  // 被匯入
  @NgModule({
    imports: [ModuleA, ModuleB],
    declarations: [ComponentA, ComponentB],
    exports: [ComponentA, ModuleA] // 匯出的部分才能被使用
  }) export class ModuleToImport {}

  // 要匯入
  @NgModule({
    imports: [ModuleToImport]
  }) export class ModuleImport
  ```


### 模組（Module）
如同上面講述的，Module 是為了管理，把相關東西整理在一起，讓程式碼更好維護。你可以在一個大型的應用程式中把所有東西都寫在 AppModule 中，程式一樣跑得動，只是維護性會很差，執行效率應該也不會好。


Module 的 Decorator 是``` @NgModule() ```，它的 metadata 如上例所示。一般的 Module 就是 export 後被其他 Module 所 import。
> 別搞混 ES6（``` import / export ```）和 Angular （``` imports / exports ```）模組匯入匯出。
>
> ES6 的模組是以檔案為單位，可以匯入匯出多個項目，不論是物件還是函數甚至是檔案內容。
>
> Angular 模組匯入匯出則是寫在``` @NgModule({ imports: [], exports: [] }) ```中。
>
> 要把一個 class 定義為一個 Angular 的模組，只需要使用``` @NgModule() ```進行修飾即可。

但是 AppModule 屬於「特殊的一個」，你不會在其他地方用``` imports ```的方式匯入 AppModule。AppModule 要透過一個特殊的方法來啟動它。通常寫在``` main.ts ```。
```javascript
// main.ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.Module';

platformBrowserDynamic().bootstrapModule(AppModule);

```


### Angular 程式庫
我們會用到很多 Angular 定義好的「Angular 模組」，這些東西存放在 npm 的封裝（package）中，我們只需要下載安裝，然後在適當的檔案中``` import ```即可。好比我們會定義很多 Component 的 class，要讓 Angular 知道這個 class 是一個 Angular 的 Component 我們需要用``` @Component ```去修飾這個 class。而正確使用``` @Component ```就需要正確``` import ```。
```javascript
import { Component } from '@angular/core';
@Component({
  selector: 'some-tag'
  template: ``,
  styles: []
})
export class ComponentA {}
```

這些定義好的東西，都根據功能歸類在不同的程式庫中：
- ``` @angular/core ```：核心相關
- ``` @angular/platform-browser ```：瀏覽器相關
- ``` @angular/http ```：http連線請求相關
- ``` @angular/forms ```：表單相關
- ...

雖然運用 ES2015 的模組系統讓 Angular 可以更有結構、更靈活，但這往往也是造成初學者很難上手的壁壘。當然為了推廣 Angular 讓 AngularJS 逐步退出歷史舞台，很多人會對你說 Angular 又簡單又容易學（相對 AngularJS），但是相對 AngularJS ， Angular 所須的技術門檻絕對是更高的。


### 基於 Class、Decorator、Module 的開發模式
Angular 全面擁抱最新的Javascript技術，仰賴 ES2015 / ES6 許多革命性的創新，包含像 class、decorator、Module 等，讓程式碼可以更有開發效率。TypeScript 在前瞻創新上走在 ES2015+ 之前，提供了更多新特性而且完全相容於 ECMAScript。籠統地說 TypeScript 就是在 ES2015+ 之上多了型別，讓 ES2015+ 有型別檢查的能力。


因為有了 class、decorator、Module，因此 Angular 的程式碼在撰寫C omponent、Directive、Pipe、Service、Module（Angular 的模組，不是 Javascript 的）等時，都是用 **Class-based 的形式**，基本的結構就是這樣：
```javascript
import { something } from 'ModulePath';

@Decorator({
something: []
})
export class Something {
constructor () {}
}
```
1. 首先``` import ```一些必要的東西
1. 透過``` @Decorator ```去修飾要定義``` class ```
1. ``` export ``` ``` class ```（含定義的內容）

這就是一個很典型的根模組（AppModule）定義：
```javascript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.Module';
import { LoggerService } from './logger.service';
@NgModule({
imports:      [ BrowserModule ],
declarations: [ AppComponent ],
providers:    [ LoggerService ],
exports:      [ AppComponent ],
bootstrap:    [ AppComponent ] // 只會在根模組中用到
})
export class AppModule { }
```


### 元件（Component）
Component 基本上是你寫 Angular 最常碰到的東西，它就是針對模板（template）、樣式（style）、協調服務的一個封裝。你可以想像在應用程式這個大介面上，針對每一小塊的部分都拆開來一個一個的處理。這樣我們可以很專心的針對一小塊介面做處理，測試也更容易，甚至某個切出來的小塊會重複出現多次，我們複製多幾次 Component 就好了。

```javascript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'some-tag'
  templateUrl: './Component.html',
  styles: ['./Component.css']
})
export class SomeComponent implements OnInit {

  constructor() {}

  onClick() {}

  ngOnInit() {}
}
```

這是一個很典型的 Component 定義，``` import ```必要的 Decorator。``` @Component({}) ```中提供元件必要的模板檔案路徑和樣式的路徑（樣式為什麼是陣列呢？因為你可以提供多個樣式檔）。底下``` export class ```裡，定義了這個元件中與模板互動必要成員，譬如``` onClick() ```這個方法就可以繫結到某個按鈕，當使用者點選了該按鈕時就觸發這個方法。

```html
<button (click)="onClick()"></button>
```

另外，你看到一個``` ngOnInit() {} ```的成員，它屬於元件從被產生到銷毀中其中一個生命週期的階段（lifecycle hook），顧名思義，這個方法定義了這個元件在被產生時應該做些甚麼，譬如在元件被產生的時透過api遠端請求一組資料回來顯示。


### 父子元件之間的資料傳遞
![Component-tree](https://angular.io/generated/images/guide/architecture/Component-tree.png)


上述我們把介面拆成一個個小元件，但是元件裡面還可以再塞元件，到最後就會長成一棵元件樹。父子之間若有需要做資料或事件的傳遞，是可以透過``` @Input ```來定義傳入的資料和``` @Output ```來傳出要觸發的事件。


### 資料繫結／繫結（Data binding）
Data binding 是從 AngularJS 就開始存在的特色，也是 AngularJS 成名的招牌。Angular 的繫結從概念上只有四種：
- ```DOM ← {{ value }}            ← Component```
- ```DOM → [property]="value"     → Component```
- ```DOM → (event)="handler"      → Component```
- ```DOM ← [(ngModel)]="property" → Component```

```html
<h1>{{ title }}</h1>
<hero-detail [hero]="selectedHero"></hero-detail>
<input type="text" [value]="someValue">
<button (click)="onClick()"></button>
<input type="text" [(ngModel)]="hero.name">
```

上述的例子中``` [property]="value" ```舉了兩個範例，是為了表示 property 的繫結可以是原本HTML標籤的內容（property）也可以是自訂 Component 的內容。


另外就是雙向繫結``` [(ngModel)]="hero.name" ```，這表示一旦使用者在介面的```<input>```中輸入了值，還是 Component 修改了```hero.name```，介面和變數立刻反映出值得變化。


### Directive
也是原自 AngularJS 的產物，在Angular中因為有 Property 和 Event 的繫結使得 Directive 的數量驟降。除了上述看到的```[(ngModel)]```之外，一般上在模板中最常見就是這兩個用來控制模板顯示的 Directive。
- ``` *ngIf="expression" ```
- ``` *ngFor="expression" ```
```html
<li *ngFor="let hero of heroes"></li>
<hero-detail *ngIf="selectedHero"></hero-detail>
```

顧名思義，``` *ngFor ```就是讓模板根據變數的數量產生大量相同的HTML標籤。```*ngIf```則是根據變數做判斷來從模板中取捨 HTML 標籤。除此之外，尚有類似```ngStyle ngClass```用於控制 HTML 標籤的樣式和 CSS Class，讓介面更美觀。


### 服務（Service）
分工明確在程式設計領域中有一個專有名詞——關注點分離，意思是我們不要打造一個巨無霸的怪獸元件，而是讓元件儘量單純，只管呈現介面和使用者互動。譬如向伺服器請求和寫入資料等管理資料的工作，或是專門用來維護使用者登入狀態這類的事情就把它專門寫成一個 class，Angular 把它歸類為服務。
```javascript
import { Injectable } from '@angular/core';
import { LoggerService } from './logger.service';

@Injectable()
export class SomeService {
  // 注入其他Service
  constructor(private logger: LoggerService) {}

  getHeroes() {
    return ...
  }
}
```
就像上面說的，所有東西都只是 class 而以，Service 當然也不例外，而且 Service 一樣可以注入其他 Service。透過 Injector 的幫忙，我們不需要煩惱服務是否是唯一的，我們不需要去操心管理它，一切由 Injector 接管。


![injector](https://angular.io/generated/images/guide/architecture/injector-injects.png)


但是我們在程式中任意的注入服務，Angular 到底怎麼知道你要注入的是哪個呢？一切都需要按照遊戲規則走，寫好的服務需要被註冊，透過注入時所指名的型別， Injector 就可以很聰明幫我們找到這個服務並注入到 class 中，若未有實體存在，則自動建立一個新的，往後則都只注入這次建立的實體。下例中把服務註冊到 AppModule 中，這樣所有底下元件都可以正確被注入。但少數情況下，你也可以選擇只註冊在元件中。

```javascript
// app.moodule.ts
@NgModule({
  providers: [SomeService]
})

// SomeComponent.ts
@Component({
  providers: [SomeService]
})
```

這樣你就可以在```constructor(private someService: SomeService) {}```中注入已註冊服務並使用它。


### 你還需要知道的事！
Angular還有很多程式庫，提供多樣性的功能：
- animations
  - 它負責管理動畫效果，自 Angular 4.x.x 後從其他程式庫中分離出來，爾後使用都必須另外安裝和匯入到模組中。
- forms
  - 製作表單的好幫手，ngModel 這個 Directive 就放在這個程式庫中，必須要匯入 FormsModule 才能使用。
- http
  - 涉及與伺服器通訊絕對少不了這個程式庫，匯入 HttpModule，我們才能注入 HttpService 並用它提供的方法來與伺服器通訊。
- route
  - 控制瀏覽器路由（簡單講就是網址）少不了的程式庫。


Pipe 尚未被提到，但是它的用處極大，它可以讓我們在顯示資料時起到過濾和加工的效果。譬如：
```html
<div>
  price | currency: 'USD':true
</div>
```
若```price```為42.33則介面會自動顯示為```$42.33```。

元件（Component）之間的溝通是所有前端架構都會碰到的問題，資料要如何傳遞？事件發生時改變了資料（Model）該如何連動到其他元件？這些問題都是很典型的元件間通訊問題。拜 ReactiveX 的誕生，這個問題可以用很瀟灑的方式來解決。
