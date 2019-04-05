## Angular cookbook by hangull
# CLI Commands
```bash
ng serve # Avvia il server locale (node, webpack ecc.)
ng g c optionalPath/newComponent # Genera un componente (opzione --spec=false per non avere il file .spec.ts)
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
## Structural directives:
---
Cambiano il DOM
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
---
## Attribute directives
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
