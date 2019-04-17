## Angular cookbook by hangull
Table of Contents
---
---
1. [Angular CLI](#angular-cli)
2. [PackageManager (di default npm)](#packagemanager-di-default-npm)
3. [General](#general)
   1. [Debug tools](#debug-tools)
      1. [Sourcemaps](#sourcemaps)
      2. [Augury](#augury)
   2. [Local References (label)](#local-references-label)
4. [Angular Components](#angular-components)
   1. [Component templates](#component-templates)
   2. [Selector (valido anche per @Directive)](#selector-valido-anche-per-directive)
   3. [View Encapsulation](#view-encapsulation)
   4. [Projecting content with ng-content](#projecting-content-with-ng-content)
   5. [Component Lifecycle (hooks)](#component-lifecycle-hooks)
5. [Databinding (comunicazione)](#databinding-comunicazione)
   1. [Ouput Data](#ouput-data)
   2. [React to Events](#react-to-events)
   3. [Two-Way-Binding (Ouput + React to Events)](#two-way-binding-ouput--react-to-events)
   4. [@Input and @Output](#input-and-output)
6. [Angular Directives](#angular-directives)
   1. [[(ngModel)] Directive](#ngmodel-directive)
   2. [Built-in Structural directives:](#built-in-structural-directives)
      1. [*ngIf](#ngif)
      2. [*ngFor](#ngfor)
      3. [*ngSwitch](#ngswitch)
   3. [Built-in Attribute directives](#built-in-attribute-directives)
      1. [ngStyle](#ngstyle)
      2. [ngClass](#ngclass)
   4. [Custom directives](#custom-directives)
      1. [Custom attribute directive example](#custom-attribute-directive-example)
      2. [Custom structural directive example](#custom-structural-directive-example)
7. [Models (interfacce)](#models-interfacce)
8. [Angular Services and Dependency Injection](#angular-services-and-dependency-injection)
   1. [Hierarchical Injector](#hierarchical-injector)
9. [Routing](#routing)
   1. [router-outlet](#router-outlet)
   2. [Router links](#router-links)
   3. [Navigating Programmatically](#navigating-programmatically)
   4. [Fetching route parameters](#fetching-route-parameters)
   5. [Query Parameters and Fragments](#query-parameters-and-fragments)
   6. [Nested Routes](#nested-routes)
   7. [Redirecting and Wildcard Routes](#redirecting-and-wildcard-routes)
   8. [Route Guards](#route-guards)
      1. [Protecting Child (nested) Routes with canActivateChild](#protecting-child-nested-routes-with-canactivatechild)
      2. [canDeactivate](#candeactivate)
   9. [Passing Data to a Route](#passing-data-to-a-route)
      1. [Static way](#static-way)
      2. [Dynamic way with a Resolver Guard](#dynamic-way-with-a-resolver-guard)
10. [Observables](#observables)
      1. [Operators](#operators)
   1. [Unsubscribe](#unsubscribe)
   2. [Subject](#subject)
11. [Foms](#foms)
   1. [Tempalate Driven (TD) approach](#tempalate-driven-td-approach)
      1. [Validation](#validation)
      2. [Grouping](#grouping)
      3. [Handling radio buttons](#handling-radio-buttons)
      4. [Setting/patching and resetting form values](#settingpatching-and-resetting-form-values)
   2. [Reactive approach](#reactive-approach)
      1. [Getting access to controls](#getting-access-to-controls)
      2. [Grouping controls](#grouping-controls)
   3. [Arrays of Form Controls](#arrays-of-form-controls)
   4. [Custom Validators](#custom-validators)
      1. [Async custom validators](#async-custom-validators)
# Angular CLI
```bash
ng serve # Avvia il server locale (node, webpack ecc.)
ng g c optionalPath/newComponent # Genera un componente (opzione --spec=false per non avere il file .spec.ts)
ng g d optionalPath/newDirective # Genera directive
ng g s optionalPath/newService # Genera directive
ng g g optionalPath/newService # Genera guard
```
# PackageManager (di default npm)
```bash
# da eseguire nella cartella del progetto
npm install --save package # Installa il package E LO SALVA in package.json (i file dei package vanno in node_modules)
npm install # Installa tutti i package elencati in package.json
```
# General
## Debug tools
### Sourcemaps
Grazie alla compatibilità con sourcemaps, usando la console: **F12 > source** potremo vedere il codice, che verrà ritradotto in typescript e da lì sarà possibile aggiungere dei breakpoint **DIRETTAMENTE** da browser.
> HARD WAY<br>
> trovare lo spezzone di codice dal main<br>
> EASY WAY<br>
> aprire la cartella **webpack > src** e navigare tra i file .ts del progetto
### Augury
Augury è un estensione chrome che permette di analizzare app angular.<br>
**Funzionalità:**<br>
- Lista dei components
- Injector Graph (mostra le dipendenze tra i componenti ed i servizi)
- Aiuta a controllare il routing client side
- Mostra i moduli e le relative import<br>
## Local References (label)
Tramite queste label posso riferirmi ad un elemento html e passarlo come argomento<br>
> SOLO nel html le posso usare, non nel typescript a meno che non si usi **@ViewChild** o **@ContentChild**<br>
> @ViewChild se è nella vista, @ContentChild se è nel content (ng-content)
```html
<div #LocalReference>Hello World</div>
<button (click)="myFunc(LocalReference)">Click me!</button>
<ng-content>
  <div #LocalContentReference>Hello World</div>
</ng-content>
```
.ts corrispondente all'html
```typescript
import { ViewChild, ContentChild, ElementRef } from '@angular/core';
// ...
export class ExampleClass {
  @ViewChild('LocalReference') LocalReference: ElementRef;
  @ContentChild('LocalContentReference') LocalContentReference: ElementRef;
}
```
> NOTA<br>
> un buon modo per capire di che tipo è la reference (di solito ElementRef con la proprietà nativeElement) ed analizzarne la struttura, è usare un **console.log()** per stampare l'elemento.

> NOTA<br>
> Tramite questa reference è possibile cambiare i valori del DOM (es. cambiare il .value di un elemento html), è consigliabile NON FARLO essendoci modi migliori forniti da Angular.
# Angular Components
## Component templates
Usare un inline-template al posto di usare templateUrl/styleUrls
```typescript
// ...
@Component({
  // ...
  template: `<span>
               inline-template
             </span>`,
  // Posso farlo anche per lo style
  styles: [`
    * {
      display: none
    }
  `]
  // ...
})
// ...
```
## Selector (valido anche per @Directive)
Vi sono diversi modi di definire il selettore dei componenti:
```typescript
// ...
@Component({
  // ...
  selector: 'app-selector', // Selector by element <app-selector></app-selector>
  selector: '[app-selector]', // Selector by attribute <div app-selector></div>
  selector: '.app-selector', // Selector by class <div class="app-selector"></div>
  // ...
})
// ...
```
## View Encapsulation
> I CSS del componente valgono solo per il componente, questo è possibile grazie a classi aggiunte automaticamente da angular ad ogni tag html di ogni component
```css
/*SOLO i tag p del componente sono affetti da questa regola*/
p {
  color: red;
}
```
Si può FORZARE ANGULAR a non avere questa feature (il CSS del component sarà applicato globalmente, ma con meno importanza, non avendo anche la classe specifica degli altri component):
```typescript
import { ViewEncapsulation } from '@angular/core';
//...
@Component({
  //...
  encapsulation: ViewEncapsulation.Emulated // Default
  encapsulation: ViewEncapsulation.None // Forzo a non avere ViewEncapsulation
  encapsulation: ViewEncapsulation.Native // VienEncapsulation solo sui browser che la supportano
  //...
})
```
## Projecting content with ng-content
Se si volesse avere un componente con all'interno dell'html fornito dal componente padre si dovrebbe usare la direttiva **ng-content** (direttiva che usa il selector by element, situazione inusuale).<br><br>
html del padre
```html
<child>
  <div>Content scelto dinamicamente</div>
</child>
<!--di default ignoro questo content-->
```
html del component child
```html
<div class="some-class">
  <!--altri tag-->
  <div class="custom-content">
    <ng-content></ng-content> <!--dico ad angular di mettere qui il contenuto del tag <child>-->
  </div>
</div>
<!--di default ignoro questo content-->
```
## Component Lifecycle (hooks)
I componenti Angular attraversano varie fasi, Angular ci permette di "agganciarci" a queste fasi tramite l'implementazione di metodi detti **hooks**, come **ngOnInit()**.
> NOTA: è sempre consigliato usare i console.log() per studiarne il comportamento agli inizi
```typescript
export class MyClass implements OnInit, ...
```
```typescript
import { OnInit, ..., SimpleChanges } from '@angular/core';
```
- **ngOnChanges(changes: SimpleChanges)**<br>
eseguito quando il component viene creato ed ogni volta che qualche bound input property cambia (proprietà con **@Input**).<br>
> NOTA: il tipo SimpleChanges fornisce anche il campo previousValue, utile per avere una memoria
- **ngOnInit**<br>
chiamato quando il component è inizializzato, dopo il constructor (ovvero prima che sia aggiunto al DOM).
- **ngDoCheck**<br>
chiamato ad ogni cambiamento o addirittuara azione che POTREBBE portare ad un cambiamento (utile ad esempio per avvertire Angular che qualcosa non detectabile è cambiato; advanced use case).
- **ngAfterContentInit**<br>
chiamato dopo che il projected content (ng-content) è proiettato nella view.
- **ngAfterContentChecked**<br>
chiamato ogni volta che il projected content è controllato.
- **ngAfterViewInit**<br>
chiamato dopo che la view del componente (e dei suoi child) è inizializzata.
- **ngAfterViewChecked**<br>
chiamato ogni volta che la view del componente (e dei suoi child) è controllata.
- **ngOnDestroy**<br>
chiamato quando il component sta per essere distrutto (quando sta per essere rimosso dal DOM)
# Databinding (comunicazione)
Databinding = comunicazione tra template HTML e business logic (typescript in questo caso)
## Ouput Data
- **String Interpolation** {{ expression }}
- **Property Binding** [property]="varname" -> e.g. [disabled]="expression"
> NOTA: facendo Property Binding su InnerText si ha una specie di String Interpolation
## React to Events
- **Event Binding** (event)="expression" -> e.g. il click è un evento (click)="expression"
## Two-Way-Binding (Ouput + React to Events)
- [()] -> e.g. [(ngModel)]="varname" (guarda la sezione Directives per le import necessarie)

> Property & Event binding sono usate anche per far comunicare tra di loro i components (tramite i decorators @Input() ed @Ouput())
## @Input and @Output
component.ts
```typescript
import { Input, Output, EventEmitter } from '@Angular/core';
// ...
@Input() customProp1: Any; // @Input() -> binding to custom property
@Input('optionalAlias1') customProp2: Any;
@Output() customEvent1 = new EventEmitter<string>(); // @Output() -> binding to custom event
@Output('optionalAlias2') customEvent2 = new EventEmitter<string>();
// ...
someFunc() {
  this.customEvent1.emit('father, read this');
  this.customEvent2.emit('external alias: optionalAlias2');
}
// ...
```
father.component.html
```html
<component
  [customProp1]="42"
  [optionalAlias1]="true"
  (customEvent1)="myFancyFunc($event)"
  (optionalAlias2)="myFancyFunc($event)"></component>
<!--$event ha i valori emessi dal emit es. 'father, read this'->
```
# Angular Directives
Le Directives sono istruzioni nel DOM, il selector consigliato è by attribute<br>
**I Components sono Directives**
## [(ngModel)] Directive
**[(ngModel)]="varname"**<br>
Permette di utilizzare in modo rapido e facile il Two-Way-Binding
```typescript
// imports da includere nel .module.ts corrente
import { FormsModule } from '@angular/forms';
// ...
@NgModule({
  imports: [
    // ...
    FormsModule
  ],
})
// ...
```
## Built-in Structural directives:
Cambiano il DOM, le structural directives hanno un * all'inizio che behind the scenes Angular trasforma in un property binding
```html
<div *ngIf="expression">text</div>
<!--Viene trasformato da Angular in: -->
<ng-template [ngIf]="expression">
  <div>text</div>
</ng-template>
```
> NOTA: non si può avere più di una structural directive sullo stesso elemento HTML
### *ngIf
Aggiunge o rimuove un elemento dal DOM in base alla condizione. Non è come un display: none da CSS
```html
<div *ngIf="expression; else elseTemplate">expression was true</div>
<ng-template #elseTemplate></ng-template>
```
### *ngFor
Ripete l'elemento ciclando su un array dato (purtroppo non si può ciclare su un numero)
```html
<div *ngFor="let e of array; let i=index">InnerText</div>
<!--La seconda parte è opzionale, e serve a sapere l'index attuale dell'array (tramite la variabile i)-->
```
### *ngSwitch
Visualizza il case corrispondente al value immesso
```html
<div [ngSwitch]="value">
  <div *ngSwitchCase="1">Value is 1</div>
  <div *ngSwitchCase="2">Value is 2</div>
  <div *ngSwitchCase="3">Value is 3</div>
  <div *ngSwitchDefault>Value is Default</div>
</div>
```
## Built-in Attribute directives
Cambiano solo l'elemento su cui sono piazzate
### ngStyle
> Nel seguente esempio uso il Property Binding per configurare la direttiva ngStyle
```html
<div [ngStyle]="{cssProperty: expression}">InnerText</div>
<div [style.cssProperty]="expression">InnerText</div><!--Alternativa elegante ad ngStyle-->
<!--expression deve ritornare una stringa valida per la proprietà CSS-->
```
### ngClass
```html
<div [ngClass]="{className: expression}">InnerText</div>
<div [class.className]="expression">InnerText</div><!--Alternativa elegante ad ngClass-->
<!--expression deve ritornare un boolean (true = l'elemento avrà la classe)-->
```
## Custom directives
### Custom attribute directive example
.module.ts
```typescript
import { Directive } from 'path';
// ...
@NgModule({
  desclarations: [
    // ...
    Directive
  ]
  // ...
})
```
basic-highlight.directive.ts
```typescript
import { Directive, ElementRef, OnInit } from '@angular/core';

// Directive decorator
@Directive({
  selector: '[appBasicHighlight]' // Selector by attribute
})
export class BasicHighlightDirective implements OnInit {
  constructor(private elementRef: ElementRef) {} // Typescript shortcut nel costruttore

  ngOnInit() {
    this.elementRef.nativeElement.style.backgroundColor = 'green';
  }
}
```
> NOTA: non è una good practice accedere così agli elementi del DOM, meglio usare il **renderer** o la property **@HostBinding** per evitare problemi. (es. l'app gira su un service che non ha accesso al DOM)

basic-highlight.directive.ts
```typescript
import { Directive, OnInit, ElementRef, Renderer2, HostListener, HostBinding, Input } from '@angular/core';

@Directive({
  selector: '[appBetterHighlight]' // Selector by attribute
})
export class BasicHighlightDirective implements OnInit {
  @Input() defaultColor: string = 'transparent';
  @Input() highlightColor: string = 'blue';
  // @HostBinding è un modo più veloce e semplice di usare Renderer2
  @HostBinding('style.backgroundColor') backgroundColor: string;
  // Eesempio di HostBinding su una classe:
  // @HostBinding('class.myClass') bool: boolean;
  constructor(private elRef: ElementRef, private renderer: Renderer2) {}

  ngOnInit() {
    this.backgroundColor = this.defaultColor;
  }

  @HostListener('mouseenter') mouseover(eventData: Event) {
    this.renderer.setStyle(this.elRef.nativeElement, 'background-color', this.highlightColor, false, false);
  }

  @HostListener('mouseleave') mouseleave(eventData: Event) {
    /*this.renderer.setStyle(this.elRef.nativeElement, 'background-color', 'transparent', false, false);*/
    this.backgroundColor = this.defaultColor; // Setto il bg tramite HostBinding
  }
}
```
.html che usa la directive
```html
<p appBasicHighlight>green bg</p>
<p appBetterHighlight highlightColor="black">black bg when mouse over</p>
<p
  appBetterHighlight
  [highlightColor]="'red'"
  [defaultColor]="'yellow'">red bg when mouse over, yellow bg when mouse leave</p>
```
> Angular prima controlla se la direttiva ha degli **@Input** e dopo controlla il component, quindi le custom properties delle direttive hanno la precedenza.
### Custom structural directive example
.module.ts
```typescript
import { Directive } from 'path';
// ...
@NgModule({
  desclarations: [
    // ...
    Directive
  ]
  // ...
})
```
unless.directive.ts
```typescript
import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appUnless]' // Selector by attribute
})
export class UnlessDirective {
  @Input() set appUnless(condition: boolean) {
    if (!condition) {
      this.createEmbeddedView(this.templateRef);
    } else {
      this.vcRef.clear();
    }
  }

  constructor(private templateRef: TemplateRef<any>, private vcRef: ViewContainerRef) {}
}
```
.html che usa la directive
```html
<div *appUnless="expression">if not expression</div>
```
# Models (interfacce)
I modelli definiscono la struttura degli oggetti, averli generali in un file permette di accorgersi rapidamente delle modifiche da apportare in tutto il progetto al variare della struttura della classe esportata dal modello.
> Esempio di modello:
```typescript
// example.model.ts
export class ExampleClass { // Il nome della classe è in PascalCase per convenzione
  public example: string;
  public flag: boolean;

  constructor(ex: string, f: boolean) {
    this.example = ex;
    this.flag = f;
  }
}

//shortuct offerta da typescript equivalente al codice sopra
export class ExampleClass { // Il nome della classe è in PascalCase per convenzione
  constructor(
    public example: string,
    public flag: boolean) {}
}

// Esempio di utilizzo del costruttore
example: ExampleClass = new ExampleClass('example', true); // Ridondanza evitabile
```
# Angular Services and Dependency Injection
I servizi aiutano a centralizzare la business logic, rendendola più facile da mantenere.<br>
Angular fornisce **@Injectable** per poter injectare i servizi nei componenti tramite il **dependency Hierarchical Injector**.
## Hierarchical Injector
Se metto il servizio nell'array dei providers del:
- **AppModule** -> La stessa instanza del servizio è disponibile **Application-wide** (ovunque)
- **AppComponent** -> La stessa istanza è disponibile in **tutti i components** (ma non negli altri servizi)
- **Any other component** -> La stessa istanza è disponibile per il component ed i suoi child.<br>
Come injectare un service in un component:
```typescript
import { ExampleService } from 'path';
// ...
@Component({
  // ...
  providers: [ExampleService] // Se voglio che l'istanza del servizio sia disponibile SOLO per il component ed i suoi child.
})
// ...
  constructor(private/public exService: ExampleService) {}
```
> NOTA: se il servizio avrà ad esempio un altro servizio injectato al suo interno, servirà usare il decorator **@Injectable**. E servirà rendere il servizio disponibile a livello di modulo
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // Opzione che rende disponibile a livello di root il servizio
})
```
> NOTA: di default i servizi creati tramite CLI sono creati con **@Injectable** e providedIn: 'root'
# Routing
Angular fornisce un routing client-side: l'utente ha la sensazione che il routing stia funzionando normalmente, in realtà anche cambiando pagina si è nella stessa pagina, che carica differenti components (SPA)<br>
> Da Angular 7 l'ng new chiede già se si vuole il routing, evito di descrivere la struttura della classe necessaria e le varie import

Il server deve gestire il redirect costante alla pagina index.html generata da Angular, se questo non avviene si può utilizzare un # che dice di ignorare il resto del path, in questo modo il server non deve gestire i redirect.<br><br>
app-routing.module.ts
```typescript
  // ...
  RouterModule.forRoot(appRoutes, {useHash: true}) // localhost:4200/#/
```
## router-outlet
app.component.html
```html
<header></header>
<router-outlet></router-outlet> <!--Qui ci va il component corrispondente al path attuale-->
<footer></footer>
```
## Router links
I link "normali" sembrano funzionare allo stesso modo, anche provando, il loro problema è che REFRESHANO LA PAGINA. Angular fornisce quindi una speciale directive: routerLink
```html
<!--se il link è quello aggiungo la classe-->
<!--routerLinkActiveOptions option fornita per richiedere che l'active path sia esattamente uguale-->
<div
  routerLinkActive="myClass"
  [routerLinkActiveOptions]="{exact: true}">
  <a routerLink="/"></a>
</div>
<!--Grazie al DataBinding ed alla struttura ad array si possono creare path complessi più facilmente-->
<a [routerLink]="['/users', id, name]"></a> <!--path con parametro id e name-->
```
Per definire i vari path vanno aggiunti gli oggetti all'array di tipo Routes del modulo
```typescript
const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: '/users/:id/:name', component: UsersComponent } // path con parametro (/users/67/marco, /users/78/giovanni, ecc.)
];
```
## Navigating Programmatically
Per spostarsi tra le pagine a seguito di alcune logiche typescript si può usare un **oggetto di tipo Router**.
```typescript
import { Router, ActivatedRoute } from '@angular/core';
// ...
  construcor(private router: Router, private route: ActivatedRoute) {}
// ...
  myFunc() {
    // complex calculations
    this.router.navigate(['/']);
    // relative routing
    this.router.navigate(['path'], {relativeTo: this.route});
  }
// ...
```
## Fetching route parameters
Si è visto come creare path con parametri, da typescript si può accedere a questi parametri nel seguente modo:
```typescript
import { ActivatedRoute, Params } from '@angular/core';
import { Subscription } from 'rxjs/Subscription';
// ...
  paramsSubscription: Subscription; // Credo la variabile a cui assegno la subscription

  constructor(private route: ActivatedRoute) {}
  // ...
  myFunc() {
    // snapshot eseguito solo una volta
    this.user = {
      id: this.route.snapshot.params['id'],
      name: this.route.snapshot.params['name']
    }
    // observable che si accorge dei cambiamenti
    // non ho bisogno di ditruggerlo in realtà, lo distrugge già angular
    this.paramsSubscription = this.route.params.subscribe((params: Params) => {
      this.user.id = params['id'];
      this.user.name = params['name'];
    });
  }

  ngOnDestroy() {
    // questa unsubscribe quindi è necessaria solo con observable fatti dal programmatore
    this.paramsSubscription.unsubscribe();
  }
// ...
```
## Query Parameters and Fragments
i parametri http vengono aggiunti tramite la proprietà **queryParams** della direttiva **routerLink**, mentre l'hash fragment viene aggiunto tramite **fragment**.<br>
Da html:
```html
<a
  [routerLink]="['path', subPath, 'othersubpath']"
  [queryParams]="{qp1: '1', qp2: '2'}"
  fragment="section2"></a>
<!-- path/value/othersubpath?qp1=1?qp2=2#section2 -->
```
Da typescript:
```typescript
this.router.navigate(
  ['/path'],
  {
    queryParams: {
      {qp1: '1', qp2: '2'}
    },
    queryParamsHandling: 'preserve' // esistono anche altre opzioni, con preserve ho una memoria dei valori
  },
  fragment: 'section2'
);
```
Leggere Query Parameters and Fragments dall'URL:
```typescript
// ...
  constructor(private route: ActivatedRoute) {}
  // ...
  myFunc() {
    // snapshot eseguito solo una volta
    console.log(this.route.snapshot.queryParams);
    console.log(this.route.snapshot.fragment);
    // observable che si accorge dei cambiamenti
    this.route.queryParams.subscribe();
    this.route.fragment.subscribe();
  }
```
## Nested Routes
Grazie al **child routing** è possibile avere la possibilità di mettere la direttiva \<router-outlet> anche dentro a dei component chiamati dal \<router-outlet>.<br><br>
File contenente l'array di tipo Routes del modulo:
```typescript
const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: '/users', component: UsersComponent, children: [
    { path: ':id', component: UserComponent },
    { path: ':id/edit', component: EditComponent }
  }
];
```
app.component.html
```html
<router-outlet></router-outlet> <!--Qui ci va il component corrispondente al path attuale-->
```
users.component.html
```html
<router-outlet></router-outlet> <!--Qui verranno messi i children (UserComponent o EditComponent)-->
```
## Redirecting and Wildcard Routes
> NOTA: ** identifica ogni path non trovato sopra, è importante metterlo per ultimo.

File contenente l'array di tipo Routes del modulo:
```typescript
const routes: Routes = [
  // other routes
  { path: '', redirectTo: '/somewhere' }, // ALWAYS redirect
  { path: '', redirectTo: '/somewhere', pathMatch: 'full' }, // redirect solo se sono in ''
  { path: '**', redirectTo: '/not-found' }
];
```
## Route Guards
Essenzialmente sono codice che può essere eseguito prima di caricare una route oppure appena prima di lasciarla.<br>
> SIMPLE USE CASE: dare il permesso di accedere ad una pagina solo se si è loggati. (canActivate guard).

Fake service che notifica tramite promise un login:
```typescript
// ...
  isAuthenticated() {
    const promise = new Promise(
      (resolve, reject) => {
        setTimeout(() => {
          resolve(this.loggedIn);
        }, 800);
      }
    );
    return promise;
  }
// ...
```
auth-guard.service.ts:
```typescript
import {
  CanActivate,
  ActivatedRouteSnapshot,
  RouterStateSnapshot,
  Router
} from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { Injectable } from '@angular/core';
import { FakeService } from 'path';

@Injectable() // perchè injecto un service dentro la guard
export class AuthGuard implements CanActivate {
  constructor(private authService: FakeService, private router: Router) {}

  canActivate(route: ActivatedRouteSnapshot,
              state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
    return this.authService.isAuthenticated().then(
      (authenticated: boolean) => {
        if (authenticated) {
          return true;
        } else {
          this.router.navigate(['/']);
        }
      }
    );
  }
}
```
La guardia va aggiunta ai providers del .module.ts:
```typescript
import { AuthGuard } from 'path';
import { FakeService } from 'path';
// ...
@NgModule({
  // ...
  providers: [FakeService, AuthGuard]
})
```
La guardia va assegnata alla route:
```typescript
import { AuthGuard } from 'path';
// ...
const routes: Routes = [
  { path: '/users', canActivate: [AuthGuard], component: UsersComponent }
];
```
### Protecting Child (nested) Routes with canActivateChild
Per evitare di dover mettere un canActivate su ogni child
auth-guard.service.ts:
```typescript
import {
  CanActivateChild
} from '@angular/router';
// ...
export class AuthGuard implements CanActivate, CanActivateChild {
  // ...
  canActivateChild(route: ActivatedRouteSnapshot,
                   state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
    return this.canActivate(route, state);
  }
```
File contenente l'array di tipo Routes del modulo:
```typescript
const routes: Routes = [
  { path: '', component: LoginComponent },
  { path: 'users', canActivateChild: [AuthGuard], component: UsersComponent, children: [
    { path: ':id', component: UserComponent },
    { path: ':id/edit', component: EditComponent }
  }
];
```
### canDeactivate
Guard attivata appena prima di lasciare la route
> USE CASE: per notificare l'utente che non ha salvato dei cambiamenti
can-deactivate-guard.service.ts:
```typescript
import {
  CanDeactivate,
  ActivatedRouteSnapshot,
  RouterStateSnapshot
} from '@angular/router';
import { Observable } from 'rxjs/Observable';

export interface CanComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate> {

  CanDeactivate(component: CanComponentDeactivate,
                currentRoute: ActivatedRouteSnapshot,
                currentState: RouterStateSnapshot,
                nextState?: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
    return component.canDeactivate();
  }
}
```
La guardia va assegnata alla route:
```typescript
import { CanDeactivateGuard } from 'path';
// ...
const routes: Routes = [
  { path: 'edit', canDeactivate: [CanDeactivateGuard], component: EditComponent }
];
```
La guardia va aggiunta ai providers del .module.ts:
```typescript
import { CanDeactivateGuard } from 'path';
// ...
@NgModule({
  // ...
  providers: [CanDeactivateGuard]
})
```
Va modificato anche il component la cui rout chiama canDeactivate (in questo caso EditComponent):
```typescript
import { CanComponentDeactivate } from 'path/can-deactivate-guard.service';
// ...
export class EditComponent implements CanComponentDeactivate, ... {
  // ...
  canDeactivate(): Observable<boolean> | Promise<boolean> | boolean {
    if (somethingChanged) {
      return confirm('Vuoi uscire?');
    } else {
      return true;
    }
  }
}
```
## Passing Data to a Route
### Static way
```typescript
const routes: Routes = [
  { path: 'not-found', ..., data: object }
];
```
Il component riceve i dati:
```typescript
import { ActivatedRoute, Data } from '@angular/router';

  constructor(private route: ActivatedRoute) {}

  someFunc() {
    // shortcut eseguita una volta sola
    console.log(this.route.snapshot.data);
    // observable che si aggiorna
    this.route.data.subscribe(
      (data: Data) => {
        console.log(data);
      }
    )
  }
```
### Dynamic way with a Resolver Guard
Un modo di gestire codice in modo asincrono prima ancora del constructor è affidargli un **Resolver**.<br>
Esempio di codice di una resolver guard:
```typescript
import { Resolve, ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { Injectable } from '@angular/core';

import { SomeService } from 'path';

interface Example {
  id: number;
  name: string;
}

@Injectable()
export class ExampleResolver implements Resolve<Example> {
  constructor(private someService: SomeService) {}

  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Example> | Promise<Example> | Example {
    return this.someService.someFunc(+route.params['id']);
  }
}
```
> NOTA: come per le altre guardi va aggiunta ai providers

di seguito un esempio di utilizzo nell'array che definisce le Routes
```typescript
const routes: Routes = [
  { path: '/users/:id/:name', component: UsersComponent, resolve: {newVar1: ExampleResolver, newVar2: ExampleResolver2} }
];
```
A questo punto i dati sono leggibili tramite l'observable this.route.data del caso precedente<br>
```typescript
// Sono all'interno della subscribe di this.route.data
var1 = data['newVar1'];
var2 = data['newVar2'];
```
> NOTA: notare che contrariamente alle altre guard il resover ha un oggetto json con variabile e resolver associato
# Observables
Gli observables aiutano a gestire il codice asincrono. presenti nella libreria rxjs, vengono anche forniti molti operatori utili al trattamento dei dati (map, pipe, ecc...) che trasformano l'observable a seconda della necessità; in angular sono spesso utilizzati.<br>
**Observable** = data source (Events, Http requests, Triggered in code, ...)<br>
**Observer** (la subscribe) ha 3 modi (hooks, come ngOnit...) per gestire i dati ricevuti:
- Handle Data -> cosa fare quando ricevo i dati
- Handle Error -> cosa fare se ho un errore
- Handle Completion -> cosa fare se il ricevimento è concluso
```typescript
import { Observable } from 'rxjs/Observale';
// ...
// Observable
const myObservable = Observable.create((observer: Observer<string>) => {
  setTimeout(() => {
    observer.next('first');
  }, 1000);
  setTimeout(() => {
    observer.next('second');
  }, 2000);
  setTimeout(() => {
    observer.complete();
  }, 3000);
  setTimeout(() => { // Non verrà mai inviato, l'observer è completato
    observer.next('third');
  }, 4000);
});
// Observer con i 3 hooks
myObservable.subscribe(
  (data: string) => { console.log(data); },
  (error: string) => { console.log(error); },
  () => { console.log(data); },
)
```
### Operators
Di seguito un breve esempio di come usare gli operators. Sulla documentazione si trovano molti altri operators [https://www.learnrxjs.io/operators/](https://www.learnrxjs.io/operators/)
```typescript
import { Observable } from 'rxjs/Observale';
import 'rxjs/Rx'; //Importo gli operators

const myObs = Observable.interval(100).map((data: number) => {
  return data * 2;
});
```
NEWER WAY: rxjs 6 without rxjs-compat
```typescript
import { Observable } from 'rxjs/Observale';
import { map } from 'rxjs/operators'; //Importo solo gli operators necessari

const myObs = Observable.interval(100)
  .pipe(// gli operators ora vanno nella pipe
    map((data: number) => {
      return data * 2;
    })
);
```
## Unsubscribe
Se si ha un observable che non ha una fine, che sta sempre ad ascoltare, va fatta l'unsubscribe o rimarrà in ascolto anche DOPO la distruzione del component. (memory leak).<br>
Un esempio di unsubscribe (in quel caso non necessaria): [Fetching route parameters](#fetching-route-parameters)<br>
## Subject
un altro oggetto fornito da rxjs che permette di avere un **obervable** ed un **observer** nello stesso oggetto. (usato quindi per inviare e ricevere dati in modo asincrono).
```typescript
import { Subject } from 'rxjs/Subject';

exampleSub = new Subject<any>();

exampleSub.next('string is sent');// Observable

exampleSub.subscribe((data: any) => {// Observer
  console.log(data);
});
```
> NOTA: l'**unsubscribe** è ovviamente necessaria anche con questi oggetti.
# Foms
Angular forms offre 2 tipi di approcci:
- **Template driven** -> più veloce il setup, la form viene detectata dall'html
- **Reactive** -> la struttura della form va definita da typescript, offre più controllo
## Tempalate Driven (TD) approach
> importare FormsModule nel .module.ts
```typescript
import FormsModule from '@angular/forms'
// ...
imports: [
  // ...
  FormsModule
],
// ...
```
```html
<form (ngSubmit)="onSubmit(form)" #form="ngForm"><!--Tutto viene fatto nel template, quindi mi serve una label-->
<!--Potrei anche non passare la form come parametro ma accederci con un @ViewChild-->
<!--NOTA: anche in quel caso il ViewChild non sarebbe di tipo ElementRef ma ngForm-->
  <input ngModel name="email" required email #email="ngModel"><!--Label necessaria per riferirsi-->
  <!--Ho usato 2 validators (vedi sotto)-->
  <span *ngIf="!email.valid && email.touched">email non valida</span>
  <input [(ngModel)]="otherVar" name="password"><!--Nonostante il Two-way-binding uso 'password' come riferimento della form-->
  <select [ngModel]="2" name="choice"><!--Default value: 2-->
    <option value="1">1</option>
    <option value="2">2</option>
  </select>
  <button type="submit" [disabled]="!form.valid">submit</button>
</form>
```
Analizzando l'oggetto si nota che la form ha dei controls, nel TD se ad esempio un campo non è valido viene aggiunta in automatico la class .ng-invalid (discorso valido anche per l'approccio Reactive)
> NOTA: inspect sugli element html per vedere le altre classi aggiunte da Angular
```typescript
import { ngForm } from '@angular/forms';

onSubmit(form: ngForm) {
  // stuff
}
```
### Validation
I Validators nel TD vanno messi come semplici attributi html, lista dei validators: [https://angular.io/api/forms/Validators](https://angular.io/api/forms/Validators).<br>
Inoltre se si vuole usare i validators nativi di HTML5 va messo **ngNativeValidate** nel controllo interessato.
### Grouping
```html
<form (ngSubmit)="onSubmit(form)" #form="ngForm">
  <div ngModelGroup="userData" #userData="ngModelGroup">
    <input ngModel name="email" required email #email="ngModel">
    <span *ngIf="!email.valid && email.touched">email non valida</span>
    <input ngModel name="password" required>
  </div>
  <select [ngModel]="2" name="choice">
    <option value="1">1</option>
    <option value="2">2</option>
  </select>
  <span *ngIf="!userData.valid && userData.touched">dati utente non validi</span>
  <button type="submit" [disabled]="!form.valid">submit</button>
</form>
```
### Handling radio buttons
gestire i radio buttons con TD è uguale alla gestione di una normale input
```html
<!--...-->
  <input type="radio" name="radiotest" value="1" ngModel> 1
  <input type="radio" name="radiotest" value="2" ngModel required> 2
  <!--esempio in un ngFor-->
  <input *ngFor="let e of array" type="radio" name="radiotest" [value]="e" ngModel required> {{ e }}
<!--...-->
```
### Setting/patching and resetting form values
> Prima di settare i valori tramite i metodi forniti è necessario usare una **@VIewChild() form :NgForm**

Sono forniti 2 metodi per settare i valori:
- setValue -> setta i valori di tutti i controlli di tutta la form
- patchValue -> modifica solo i controli specificati
```typescript
this.signupForm.setValue({
  username: 'name',
  password: 'pass'
});

this.signupForm.form.patchValue({
  username: 'newName'
});
// Come accedere ai valori:
console.log(this.signupForm.value.username) // newName
this.signupForm.reset(); // Resetta tutti i valori e gli stati (touched ecc.)
```
> NOTA: posso anche usare setValue, patchValue e reset in modo specifico solo su di un control
## Reactive approach
> importare ReactiveFormsModule nel .module.ts
```typescript
import ReactiveFormsModule from '@angular/forms'
// ...
imports: [
  // ...
  ReactiveFormsModule
],
// ...
```
Esempio di Reactive form:
```typescript
import { FormGroup, Validators } from '@angular/forms'
// ...
  form: FormGroup;

  ngOninit() {
    this.form = new FormGroup({
      'username': new FormControl(null, [Validators.required, Validators.email]),
      'password': new FormControl(null, Validators.required),
      'gender': new FormControl('male'),
    });
  }
}
```
```html
<form [formGroup]="form" (ngSubmit)="submit()"><!--Non mi serve un ViewChild o un parametro, ho già l'oggetto-->
  <input formControlName="username">
  <input formControlName="password">
  <input
    formControlName="gender"
    *ngFor="let g of genders"
    type="radio"
    [value]="g"> {{ g }}
</form>
```
### Getting access to controls
Se dovessi ad esempio fare un messaggio d'errore se l'username è invalido:
```html
<span *ngIf="!signupForm.get('username').valid && !signupForm.get('username').touched">Invalid username</span>
<span *ngIf="!signupForm.valid && !signupForm.touched">Invalid form</span>
```
Posso anche fare un messaggio d'errore più specifico, grazie all'array errors del control
```html
<span *ngIf="!signupForm.get('username').errors['required']">The username is required</span>
```
### Grouping controls
Riunire i controlli dentro a delle nested forms, ovvero una form dentro la form. Utile per ordine mentale e per avere i .valid ecc. di quella sezione.<br>
Esempio di Reactive form con nested FormGroup:
```typescript
// ...
  ngOninit() {
    this.form = new FormGroup({
      'userData': new FormGroup({
        'username': new FormControl(null, [Validators.required, Validators.email]),
        'password': new FormControl(null, Validators.required),
      }),
      'gender': new FormControl('male'),
    });
  }
}
```
Getting access
```html
<div formGroupName="userData">
<span *ngIf="!signupForm.get('userData.username').valid && !signupForm.get('userData.username').touched">Invalid username</span>
<!--I path sono separati da dei .-->
</div>
```
## Arrays of Form Controls
Per fornire all'utente la possibilità di aggiungere campi in maniera dinamica
```typescript
import { FormArray, ... } from '@angular/forms'
// ...
  ngOninit() {
    this.form = new FormGroup({
      'name': new FormControl(null, Validators.required),
      'hobbies': new FormArray([])
    });
  }

  onAddHobby() {
    const control = new FormControl(null, Validators.required);
    // Senza cast esplicito ho un errore
    (<FormArray>this.signupForm.get('hobbies')).push(control);
  }
}
```
associare il ts al html
```html
<form [formGroup]="form" (ngSubmit)="submit()">
  <!--...-->
  <div formArrayName="hobbies">
  <button (click)="onAddHobby()">Add field</button>
  <input
    *ngFor="let hobbyControl of form.get('hobbies').controls; let i=index"
    [formControlName]="i">
  </div>
</form>
```
## Custom Validators
```typescript
  ngOninit() {
    this.form = new FormGroup({
      'name': new FormControl(null, [Validators.required, this.customValidator.bind(this)]),
      // mi serve bindare la funzione al FormControl
    });
  }

  customValidator(control: FormControl): {[s: string]: boolean } {
    if (this.forbiddenNames.indexOf(control.value) !== -1) {
      return {'nameIsForbidden': true}; // error code aggiunto nell'array errors
    }
    return null; // se è valid il validator deve ritornare null
  }
```
### Async custom validators
```typescript
  ngOninit() {
    this.form = new FormGroup({
      'name': new FormControl(null, Validators.required, this.customAsyncValidator),
      // mi serve bindare la funzione al FormControl
    });
  }

  customAsyncValidator(control: FormControl): Promise<any> | Observable<any> {
    const promise = new Promise<any>((resolve, reject) => {
      setTimeout(() => {
        if (control.value === 'value') {
          resolve({'nameIsForbidden': true});
        } else {
          resolve(null);
        }
      }, 2000);
    });
    return promise
  }
```
> L'html risultante nel tempo di attesa del validator asincrono avrà la classe **.ng-pending**
