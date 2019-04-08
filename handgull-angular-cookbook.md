## Angular cookbook by hangull
# CLI Commands
```bash
ng serve # Avvia il server locale (node, webpack ecc.)
ng g c optionalPath/newComponent # Genera un componente (opzione --spec=false per non avere il file .spec.ts)
ng g d optionalPath/newComponent # Genera directive
```
# PackageManager (di default npm)
```bash
# da eseguire nella cartella del progetto
npm install --save package # Installa il package E LO SALVA in package.json (i file dei package vanno in node_modules)
npm install # Installa tutti i package elencati in package.json
```
# Components
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
  <div class="cusom-content">
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
eseguito quando il component viene creato ed ogni volta che qualche bound input property cambia (proprietà con @Input()).<br>
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
# Databinding
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
## @Input() and @Output()
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
component.html
```html
<component
  [customProp1]="42"
  [optionalAlias1]="true"
  (customEvent1)="myFancyFunc($event)"
  (optionalAlias2)="myFancyFunc($event)"></component>
```
# Directives
Le Directives sono istruzioni nel DOM, il selector consigliato è by attribute<br>
**I Components sono Directives**
## [(ngModel)]="varname"
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
---
Cambiano il DOM, le structural directives hanno un * all'inizio che behind the scenes Angular trasforma in un property binding
```html
<div *ngIf="expression">text</div>
<!--Viene trasformato da Angular in: -->
<ng-template [ngIf]="expression">
  <div>text</div>
</ng-template>
```
> NOTA: non si può avere più di una structural directive sullo stesso elemento HTML
## *ngIf
Aggiunge o rimuove un elemento dal DOM in base alla condizione. Non è come un display: none da CSS
```html
<div *ngIf="expression; else elseTemplate">expression was true</div>
<ng-template #elseTemplate></ng-template>
```
## *ngFor
Ripete l'elemento ciclando su un array dato (purtroppo non si può ciclare su un numero)
```html
<div *ngFor="let e of array; let i=index">InnerText</div>
<!--La seconda parte è opzionale, e serve a sapere l'index attuale dell'array (tramite la variabile i)-->
```
## *ngSwitch
Visualizza il case corrispondente al value immesso
```html
<div [ngSwitch]="value">
  <div *ngSwitchCase="1">Value is 1</div>
  <div *ngSwitchCase="2">Value is 2</div>
  <div *ngSwitchCase="3">Value is 3</div>
  <div *ngSwitchDefault>Value is Default</div>
</div>
```
---
## Built-in Attribute directives
---
Cambiano solo l'elemento su cui sono piazzate
## ngStyle
> Nel seguente esempio uso il Property Binding per configurare la direttiva ngStyle
```html
<div [ngStyle]="{cssProperty: expression}">InnerText</div>
<div [style.cssProperty]="expression">InnerText</div><!--Alternativa elegante ad ngStyle-->
<!--expression deve ritornare una stringa valida per la proprietà CSS-->
```
## ngClass
```html
<div [ngClass]="{className: expression}">InnerText</div>
<div [class.className]="expression">InnerText</div><!--Alternativa elegante ad ngClass-->
<!--expression deve ritornare un boolean (true = l'elemento avrà la classe)-->
```
---
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
> NOTA: non è una good practice accedere così agli elementi del DOM, meglio usare il **renderer** o la property **@HostBinding()** per evitare problemi. (es. l'app gira su un service che non ha accesso al DOM)

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
> Angular prima controlla se la direttiva ha degli @Input() e dopo controlla il component, quindi le custom properties delle direttive hanno la precedenza.
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
# Models
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
# Debug
## Sourcemaps
Grazie alla compatibilità con sourcemaps, usando la console: **F12 > source** potremo vedere il codice, che verrà ritradotto in typescript e da lì sarà possibile aggiungere dei breakpoint **DIRETTAMENTE** da browser.
> HARD WAY<br>
> trovare lo spezzone di codice dal main<br>
> EASY WAY<br>
> aprire la cartella **webpack > src** e navigare tra i file .ts del progetto
## Augury
Augury è un estensione chrome che permette di analizzare app angular.
### Funzionalità
- Lista dei components
- Injector Graph (mostra le dipendenze tra i componenti ed i servizi)
- Aiuta a controllare il routing client side
- Mostra i moduli e le relative import
# General
## Local References (label)
Tramite queste label posso riferirmi ad un elemento html e passarlo come argomento<br>
> SOLO nel html le posso usare, non nel typescript a meno che non si usi @ViewChild() o @ContentChild()<br>
> ViewChild se è nella vista, ContentChild se è nel content (ng-content)
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
> un buon modo per capire di che tipo è la reference (di solito ElementRef con la proprietà nativeElement) ed analizzarne la struttura, è usare un console.log() per stampare l'elemento.

> NOTA<br>
> Tramite questa reference è possibile cambiare i valori del DOM (es. cambiare il .value di un elemento html), è consigliabile non farlo essendoci modi migliori forniti da Angular.