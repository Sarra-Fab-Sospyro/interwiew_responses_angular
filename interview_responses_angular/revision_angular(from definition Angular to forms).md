# Domande e Risposte per Colloquio Front-End Junior (Angular, TypeScript, JS, HTML, CSS)

## Che cos'è Angular?

**Risposta:**  

Angular è un framework front-end open-source sviluppato da Google per costruire applicazioni web moderne e scalabili, in particolare Single Page Applications (SPA). Le sue caratteristiche principali includono:

- **Architettura a componenti**: ogni parte dell'interfaccia utente è un componente riutilizzabile
- **TypeScript**: usa questo superset di JavaScript per offrire tipizzazione statica, riducendo gli errori durante lo sviluppo
- **Routing**: sistema integrato per la navigazione tra le diverse viste dell'applicazione
- **Gestione dello stato**: tramite servizi e dependency injection
- **Data binding**: bidirezionale (two-way) per sincronizzare automaticamente modello e vista
- **Change detection**: meccanismo efficiente per aggiornare il DOM solo quando necessario
- **Tooling completo**: CLI, testing framework e strumenti di sviluppo integrati

## Che cos'è un componente in Angular?

**Risposta:**

In Angular, un componente è il blocco fondamentale per costruire l'interfaccia utente. Tecnicamente, è una **direttiva speciale** con template proprio che incapsula:

* **Logica applicativa**: codice TypeScript che gestisce il comportamento
* **Vista HTML**: definisce come il componente appare sullo schermo
* **Stili specifici**: CSS/SCSS isolati che si applicano solo al componente

Ogni componente è definito da una classe TypeScript decorata con `@Component`, che fornisce i metadati essenziali:

```typescript
@Component({
  selector: 'app-user-profile',    // Tag HTML per inserire il componente
  templateUrl: './user.html',      // File HTML del componente
  styleUrls: ['./user.scss']       // Stili CSS/SCSS del componente
})
export class UserProfileComponent {
  // Logica del componente
}
```

Questo approccio modulare favorisce il riutilizzo del codice, la manutenibilità e il testing, permettendo di costruire interfacce complesse componendo elementi più semplici.

## Che cos'è una direttiva in Angular?

**Risposta:**

Le direttive in Angular sono istruzioni nel DOM che estendono o trasformano elementi HTML, modificandone comportamento, aspetto o layout. Mentre i componenti creano elementi HTML personalizzati, le direttive manipolano elementi esistenti.

Angular definisce tre tipi principali di direttive:

1. **Direttive strutturali**: Modificano la struttura del DOM aggiungendo, rimuovendo o manipolando elementi.
   - `*ngIf`: Aggiunge/rimuove elementi in base a condizioni
   - `*ngFor`: Crea elementi ripetuti per ogni elemento di una collezione
   - `*ngSwitch`: Mostra uno tra più elementi in base a una condizione

2. **Direttive di attributo**: Cambiano l'aspetto o il comportamento di un elemento esistente.
   - `[ngClass]`: Aggiunge/rimuove classi CSS dinamicamente
   - `[ngStyle]`: Applica stili inline dinamicamente
   - `[ngModel]`: Crea data binding bidirezionale per form elements

3. **Componenti**: Considerati come direttive speciali con template proprio.

**Esempio di direttiva personalizzata**:
```typescript
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
  @HostListener('mouseenter') onMouseEnter() {
    this.highlight('yellow');
  }
  
  @HostListener('mouseleave') onMouseLeave() {
    this.highlight(null);
  }
  
  constructor(private el: ElementRef) {}
  
  private highlight(color: string) {
    this.el.nativeElement.style.backgroundColor = color;
  }
}
```

Uso nel template: `<p appHighlight>Testo evidenziato al passaggio del mouse</p>`

## Che cosa sono le Pipe in Angular?

**Risposta:**

Le **Pipe** in Angular sono trasformatori di dati che modificano come un valore viene visualizzato nel template HTML senza alterare il valore originale nel componente. Funzionano come filtri di formattazione che intercettano i dati "in transito" verso la vista.

### Caratteristiche principali:

- **Sintassi**: Si utilizzano col simbolo `|` (pipe) seguito dal nome della pipe e dai parametri opzionali
- **Non modificano i dati originali**: Operano solo sulla presentazione
- **Concatenabili**: Si possono applicare più pipe in sequenza
- **Efficienti**: Le pipe pure vengono eseguite solo quando l'input cambia

### Esempio:
```html
<!-- Formatta data in italiano -->
{{ dataOdierna | date:'dd/MM/yyyy' }}

<!-- Concatenazione di pipe -->
{{ nomeUtente | lowercase | titlecase }}
```

### Pipe integrate più utili:

| Pipe | Funzione | Esempio | Output |
|------|----------|---------|--------|
| `date` | Formatta date | `{{ data | date:'short' }}` | 19/05/23, 15:30 |
| `uppercase` | Converte in maiuscolo | `{{ 'ciao' | uppercase }}` | CIAO |
| `lowercase` | Converte in minuscolo | `{{ 'MONDO' | lowercase }}` | mondo |
| `currency` | Formatta valute | `{{ 19.99 | currency:'EUR' }}` | €19.99 |
| `json` | Serializza in JSON | `{{ oggetto | json }}` | Oggetto formattato |
| `async` | Gestisce Observable/Promise | `{{ datiAsync | async }}` | Valore quando disponibile |
| `slice` | Taglia array/stringhe | `{{ [1,2,3,4] | slice:1:3 }}` | [2,3] |

### Creazione di pipe personalizzate:
```typescript
@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20): string {
    return value?.length > limit 
      ? value.substring(0, limit) + '...' 
      : value;
  }
}
```

## Che differenza c'è tra Pipe pura e impura?

**Risposta:**

In Angular, le pipe si differenziano in base alla strategia di rilevamento dei cambiamenti:

### 🟢 **Pipe pure** (default)

- Viene eseguita **solo quando l'input di riferimento cambia**
- Angular memorizza (memoiza) il risultato per evitare calcoli ripetuti
- Reagisce solo a modifiche dell'**oggetto di riferimento**, non dei suoi valori interni
- **Vantaggi**: Ottime prestazioni, previene esecuzioni inutili
- **Uso ideale**: Trasformazioni semplici di valori primitivi o oggetti immutabili

```typescript
@Pipe({
  name: 'capitalize',
  pure: true  // default, può essere omesso
})
```

### 🔴 **Pipe impure**

- Viene eseguita ad **ogni ciclo di change detection**
- Reagisce anche a modifiche interne dell'oggetto (mutazioni)
- **Svantaggio**: Impatto significativo sulle prestazioni
- **Uso ideale**: Quando i dati possono cambiare internamente senza cambiare riferimento

```typescript
@Pipe({
  name: 'filterActive',
  pure: false  // impura
})
```

### Esempio pratico

```typescript
// Caso problematico con pipe pure
users = [{name: 'Mario', active: true}, {name: 'Luigi', active: false}];

// La pipe pura NON rileverà questa modifica:
activateUser(index) {
  this.users[index].active = true;  // Modifica interna, riferimento array invariato
}

// Soluzione 1: Usare una pipe impura (costoso)
// Soluzione 2: Creare un nuovo array (preferibile)
activateUser(index) {
  this.users = [...this.users];  // Crea nuovo riferimento
  this.users[index].active = true;
}
```

⚠️ **Importante**: Le pipe impure vanno usate con cautela poiché vengono eseguite molto frequentemente e possono causare rallentamenti nelle applicazioni complesse.

## Come si crea una Pipe personalizzata in Angular?

**Risposta:**

La creazione di una Pipe personalizzata in Angular segue un processo in 3 passaggi:

### 1️⃣ Creare la classe della Pipe

Crea un file con nome descrittivo (es. `truncate.pipe.ts`):

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate',           // Nome utilizzato nei template
  pure: true                  // Opzionale: true per default
})
export class TruncatePipe implements PipeTransform {
  // Il primo parametro è il valore da trasformare
  // I parametri aggiuntivi sono quelli passati dopo i ":"
  transform(value: string, limit: number = 20, suffix: string = '…'): string {
    if (!value) return '';
    
    // Logica di trasformazione
    return value.length > limit 
      ? value.substring(0, limit) + suffix 
      : value;
  }
}
```

### 2️⃣ Registrare la Pipe nel modulo

Nel file del modulo (es. `app.module.ts`):

```typescript
import { NgModule } from '@angular/core';
import { TruncatePipe } from './pipes/truncate.pipe';

@NgModule({
  declarations: [
    AppComponent,
    TruncatePipe  // Registra la pipe qui
  ],
  // ...
})
export class AppModule { }
```

### 3️⃣ Utilizzare la Pipe nei template

```html
<!-- Uso con valore predefinito: limita a 20 caratteri -->
<p>{{ descrizione | truncate }}</p>

<!-- Uso con limite personalizzato: limita a 50 caratteri -->
<p>{{ descrizione | truncate:50 }}</p>

<!-- Uso con più parametri: limite e suffisso personalizzati -->
<p>{{ descrizione | truncate:30:'... (continua)' }}</p>
```

### 🔍 Generazione con Angular CLI

Puoi anche generare automaticamente una pipe usando:

```bash
ng generate pipe truncate
# o più brevemente
ng g p truncate
```

Questo comando crea il file `.ts` della pipe, lo aggiunge alle dichiarazioni del modulo e crea un file di test unitario.

## Cos'è la Dependency Injection in Angular?

**Risposta:**

La **Dependency Injection** (DI) è un pattern di design fondamentale in Angular che permette di fornire a classi le loro dipendenze dall'esterno invece di costringerle a crearle internamente.

### 🎯 Vantaggi principali:

- **Codice più pulito**: Separazione delle responsabilità
- **Miglior testabilità**: Facilita il mock delle dipendenze durante i test
- **Maggiore riusabilità**: Le classi non sono vincolate a implementazioni specifiche
- **Migliore manutenibilità**: Le dipendenze possono essere sostituite senza modificare il consumatore

### ⚙️ Come funziona in Angular:

1. **Definizione delle dipendenze**: Si creano servizi o altre classi con il decoratore `@Injectable()`
2. **Registrazione dei provider**: Si specifica come Angular deve creare o fornire le istanze
3. **Uso nel costruttore**: Le dipendenze vengono dichiarate nei parametri del costruttore

```typescript
// 1. Definiamo un servizio con @Injectable()
@Injectable({
  providedIn: 'root'  // Lo rende disponibile a livello globale
})
export class UserService {
  getUsers() { return [...]; }
}

// 2. Lo iniettiamo in un componente
@Component({...})
export class UserListComponent {
  users: User[] = [];
  
  // Angular inietta automaticamente l'istanza
  constructor(private userService: UserService) {
    this.users = this.userService.getUsers();
  }
}
```

### 🔄 Livelli di registrazione:

- **A livello di applicazione**: `providedIn: 'root'` → Singola istanza condivisa
- **A livello di modulo**: `@NgModule({ providers: [Service] })` → Istanza condivisa nel modulo
- **A livello di componente**: `@Component({ providers: [Service] })` → Istanza per componente

### 🛠️ Configurazioni avanzate con provider:

| Provider | Descrizione | Uso tipico |
|----------|-------------|------------|
| `useClass` | Specifica quale classe istanziare | Fornire implementazioni alternative o mock |
| `useValue` | Fornisce un valore concreto | Costanti, configurazioni, oggetti preesistenti |
| `useFactory` | Usa una funzione per creare l'istanza | Creazione condizionale basata su configurazione |
| `useExisting` | Alias per un provider esistente | Quando più token devono puntare alla stessa istanza |

```typescript
// Esempio di useFactory
{ 
  provide: LoggerService,
  useFactory: (config) => {
    return config.production 
      ? new ProductionLogger() 
      : new DevelopmentLogger();
  },
  deps: [ConfigService]  // Dipendenze della factory
}
```

La DI è il cuore dell'architettura di Angular e ne facilita la modularità, la testabilità e la manutenzione.

## 🔧 Cos'è la Dependency Injection (DI) in Angular?

La **Dependency Injection** è un meccanismo con cui Angular **fornisce automaticamente** le dipendenze (come servizi, oggetti o funzioni) a componenti, direttive o altri servizi, **senza che debbano crearle da soli**.

Questo avviene tramite i **provider**, che dicono ad Angular **come creare o recuperare** un'istanza di una dipendenza.

## 🧰 I principali provider: useClass, useValue, useExisting, useFactory

Angular permette di personalizzare il comportamento della DI con **provider specifici**:

### 🔹 `useClass` (default)

Dichiara che la dipendenza deve essere creata **usando una determinata classe**.

```ts
class MyService {}
providers: [{ provide: 'Token', useClass: MyService }]
```

> ✅ Angular creerà un'istanza di `MyService` quando qualcuno richiede `'Token'`.

### 🔹 `useValue`

Fornisce **un valore letterale o costante** (oggetto, stringa, numero, ecc.).

```ts
const CONFIG = { apiUrl: 'https://api.example.com' };
providers: [{ provide: 'AppConfig', useValue: CONFIG }]
```

> ✅ Utile per configurazioni, token, costanti.

### 🔹 `useExisting`

Fornisce **un alias di un provider già esistente**. In pratica, due token condividono la stessa istanza.

```ts
class AuthService {}
providers: [
  AuthService,
  { provide: 'Logger', useExisting: AuthService }
]
```

> ✅ Se qualcuno richiede `'Logger'`, riceve l'istanza di `AuthService`.

### 🔹 `useFactory`

Usa una **funzione personalizzata (factory)** per creare il valore da iniettare.

```ts
function createLoggerService(isProd: boolean) {
  return isProd ? new ProdLogger() : new DevLogger();
}

providers: [
  {
    provide: LoggerService,
    useFactory: () => createLoggerService(environment.production)
  }
]
```

> ✅ Utile quando l'istanza deve essere creata in base a condizioni dinamiche (configurazioni, ambiente, ecc.).

## 🎓 Quando si usano?

| Provider      | Quando usarlo                                                      |
| ------------- | ------------------------------------------------------------------ |
| `useClass`    | Per fornire una classe standard o una alternativa (mock, stub).    |
| `useValue`    | Per fornire valori statici (config, costanti, oggetti).            |
| `useExisting` | Quando vuoi evitare duplicazione e condividere un'istanza.         |
| `useFactory`  | Quando hai bisogno di creare dinamicamente l'oggetto da iniettare. |

## ✅ Conclusione

Per un colloquio **junior**, è sufficiente dire:

> *"La dependency injection in Angular permette di fornire in automatico le dipendenze necessarie ai componenti o servizi. Possiamo controllare il modo in cui Angular crea o recupera queste dipendenze usando provider come `useClass`, `useValue`, `useExisting` e `useFactory`, a seconda del tipo di oggetto o del comportamento che vogliamo ottenere."*

## Decoratore @Injectable() in Angular

`@Injectable()` è un decoratore cruciale nel sistema di Dependency Injection di Angular che può generare confusione nei colloqui junior. Vediamo nel dettaglio cosa fa e come si utilizza.

### 🔑 Significato di @Injectable()

```typescript
@Injectable({
  providedIn: 'root'
})
export class DataService {
  // ...
}
```

Il decoratore `@Injectable()` ha due ruoli fondamentali:

1. **Indica che la classe può ricevere dipendenze**: Permette alla classe di dichiarare dipendenze nel costruttore che Angular dovrà fornire
2. **Registra la classe nel sistema DI**: Con `providedIn` specifica dove la classe è disponibile come dipendenza

### ❓ Quando è necessario usarlo?

**È NECESSARIO** quando:
- Una classe deve **ricevere dipendenze** tramite il costruttore
- Si vuole **fornire una classe** come dipendenza per altre classi

```typescript
@Injectable()
export class ProductService {
  // Questo servizio RICEVE HttpClient come dipendenza
  constructor(private http: HttpClient) {}
}
```

**NON è necessario** quando:
- Una classe non riceve dipendenze né viene iniettata altrove

### ⚠️ Errore comune: Componenti e @Injectable

I componenti (`@Component`), le direttive (`@Directive`) e le pipe (`@Pipe`) **non richiedono** `@Injectable()` perché i rispettivi decoratori già includono questa funzionalità.

```typescript
// CORRETTO - Non serve @Injectable
@Component({
  selector: 'app-user',
  template: '...'
})
export class UserComponent {
  constructor(private userService: UserService) {}
}

// SBAGLIATO - Ridondante e inutile
@Injectable()
@Component({ ... })
export class UserComponent { ... }
```

### 🌐 Opzioni di providedIn

```typescript
// Singola istanza per tutta l'applicazione
@Injectable({
  providedIn: 'root'
})

// Una nuova istanza per ogni modulo lazy-loaded
@Injectable({
  providedIn: 'any'
})

// Disponibile solo in un modulo specifico
@Injectable({
  providedIn: UserModule
})
```

### 🧪 Importanza per il testing

`@Injectable()` facilita enormemente il testing perché permette di sostituire le dipendenze reali con mock:

```typescript
TestBed.configureTestingModule({
  providers: [
    { provide: RealService, useClass: MockService }
  ]
});
```

## Come viene gestito il data binding in Angular?

**Risposta:**

> "In Angular, il data binding è il modo in cui colleghiamo i dati tra la logica scritta in TypeScript (nel componente) e il template HTML. Angular gestisce il binding in quattro modalità principali:

### 1. **Interpolazione (`{{ }}`)**

Serve per mostrare un valore nel template, leggendo direttamente dal componente.
**Esempio:** `{{ titolo }}` → stampa il valore della variabile `titolo` definita nel file `.ts`.

### 2. **Property Binding (`[property]="valore"`)**

Collega un valore del componente a una proprietà di un elemento HTML o di un componente figlio.
**Esempio:** `[src]="immagineUrl"` → imposta dinamicamente l'attributo `src` di un'immagine.

### 3. **Event Binding (`(evento)="funzione()"`)**

Permette al template di reagire a eventi (come click, input, submit), chiamando una funzione nel file `.ts`.
**Esempio:** `(click)="salva()"` → chiama la funzione `salva()` quando si clicca su un bottone.

### 4. **Two-Way Binding (`[(ngModel)]`)**

Collega una variabile in entrambe le direzioni: ogni modifica nel template aggiorna il componente, e viceversa.
Per usarlo serve importare il `FormsModule`.
**Esempio:** `[(ngModel)]="nome"` → sincronizza un campo di input con la variabile `nome`.

## 📦 Comunicazione tra componenti

Il binding viene usato anche per far comunicare componenti:

* **Dal padre al figlio**:
  Si usa il property binding con il decoratore `@Input()` nel figlio.

  ```ts
  // Padre (template)
  <app-figlio [messaggio]="testo">

  // Figlio (TS)
  @Input() messaggio: string;
  ```

* **Dal figlio al padre**:
  Si usa l'event binding con `@Output()` e `EventEmitter`.

  ```ts
  // Figlio
  @Output() inviaDati = new EventEmitter<string>();
  inviaDati.emit('ciao');

  // Padre
  <app-figlio (inviaDati)="riceviDati($event)">
  ```

### ✅ In sintesi:

"Il data binding in Angular permette lo scambio di informazioni tra la logica dell'app e l'interfaccia utente, in modo semplice ed efficace. È uno dei concetti chiave del framework."

### ✅ Se vuoi **mostrare** un numero (o qualsiasi valore) nell'HTML:

Usa **interpolazione**:

```html
<p>{{ numero }}</p>
```

Nel file TypeScript:

```ts
numero = 42;
```

Questo è il modo **più diretto e leggibile** per mostrare un valore nel template.

### 🧠 Ma se parliamo **specificamente di property binding**:

Il **property binding** serve per collegare proprietà di elementi HTML o componenti (come `value`, `src`, `disabled`, ecc.) a valori del componente.

Esempio con un `input` che mostra un numero:

```html
<input [value]="numero" readonly />
```

Qui non lo "stampi" direttamente nel DOM, ma assegni il numero alla proprietà `value` dell'input.

### In sintesi:

* **Per mostrare valori nel template** → usa `{{ numero }}`
* **Per passare dati a proprietà di elementi HTML** → usa `[property]="valore"`

## Come si gestiscono i form in Angular?

**Risposta:**

In Angular i form possono essere gestiti in due modi: **Template-driven Forms** e **Reactive Forms**. Entrambi servono per raccogliere e validare input dell'utente, ma seguono approcci diversi.

### 🟠 **Template-driven Forms**

* Usano il modulo `FormsModule`.
* La logica del form è scritta **principalmente nel template HTML**.
* Si basa su **`[(ngModel)]`** per collegare i dati tra input e componente.
* Le **validazioni** (come `required`, `minlength`, `pattern`, ecc.) sono dichiarate direttamente negli elementi HTML.
* Angular crea automaticamente un oggetto `NgForm` che rappresenta lo stato del form.
* Ideali per **form semplici** e **applicazioni più piccole**.

**Esempio:**

```html
<input [(ngModel)]="user.name" name="name" required />
```

### 🟢 **Reactive Forms**

* Usano il modulo `ReactiveFormsModule`.
* La logica del form è **scritta nel componente TypeScript**, quindi è più strutturata e programmabile.
* Si usano classi come `FormControl`, `FormGroup` e `FormArray` per costruire il form.
* Le validazioni (come `Validators.required`, `Validators.minLength`) sono definite **nel codice**, non nel template.
* Permettono una **gestione dinamica**, ad esempio: aggiungere o rimuovere campi in base a certe condizioni.
* Offrono **maggiore controllo**, sono più **testabili** e adatti a **form complessi**.

**Esempio:**

```ts
form = new FormGroup({
  name: new FormControl('', Validators.required)
});
```

```html
<input [formControl]="form.get('name')" />
```

### 🧠 **In sintesi:**

| Caratteristica  | Template-driven | Reactive Forms               |
| --------------- | --------------- | ---------------------------- |
| Logica          | Nel template    | In TypeScript (componente)   |
| Validazioni     | HTML            | Codice                       |
| Binding         | `[(ngModel)]`   | `[formControl]`, `formGroup` |
| Dinamicità      | Limitata        | Avanzata (con `FormArray`)   |
| Testabilità     | Più difficile   | Più facile                   |
| Uso consigliato | Form semplici   | Form complessi/dinamici      |
.
---

## 🧩 **Elementi base dei Reactive Forms**

### 🔹 `FormControl`

Un **singolo campo di input** (come un `<input>`, `<select>`, ecc.).

#### ✅ Cosa fa:

* Tiene traccia del **valore** e dello **stato di validazione** del campo.
* Si può collegare direttamente a un input.

#### 🧪 Esempio:

```ts
nameControl = new FormControl('', Validators.required);
```

```html
<input [formControl]="nameControl" placeholder="Nome" />
```
.
---

### 🔹 `FormGroup`

Un **gruppo di `FormControl`** (o anche altri `FormGroup`) che rappresenta un'intera **sezione del form**.

#### ✅ Cosa fa:

* Organizza i campi per nome (come un oggetto).
* Può avere **validazioni a livello di gruppo**.

#### 🧪 Esempio:

```ts
form = new FormGroup({
  name: new FormControl('', Validators.required),
  email: new FormControl('', [Validators.required, Validators.email])
});
```

```html
<form [formGroup]="form">
  <input formControlName="name" placeholder="Nome" />
  <input formControlName="email" placeholder="Email" />
</form>
```
.
---

### 🔹 `FormArray`

Una **lista dinamica di controlli**, utile quando vuoi aggiungere o rimuovere **più elementi dello stesso tipo** (come una lista di hobby, email, o figli).

#### ✅ Cosa fa:

* Può contenere `FormControl`, `FormGroup` o anche altri `FormArray`.
* Ideale per **campi ripetuti o dinamici**.

#### 🧪 Esempio:

```ts
form = new FormGroup({
  hobbies: new FormArray([
    new FormControl('Lettura'),
    new FormControl('Musica')
  ])
});

get hobbies() {
  return this.form.get('hobbies') as FormArray;
}

addHobby() {
  this.hobbies.push(new FormControl(''));
}
```

```html
<div formArrayName="hobbies">
  <div *ngFor="let hobby of hobbies.controls; let i = index">
    <input [formControlName]="i" placeholder="Hobby" />
  </div>
  <button (click)="addHobby()">Aggiungi Hobby</button>
</div>
```
.
---

## 🧠 Riepilogo veloce

| Classe        | Descrizione                                   | Esempio d’uso                              |
| ------------- | --------------------------------------------- | ------------------------------------------ |
| `FormControl` | Un singolo campo (es. nome, email)            | `new FormControl('', Validators.required)` |
| `FormGroup`   | Gruppo di controlli organizzati per chiave    | `new FormGroup({ name: ..., email: ... })` |
| `FormArray`   | Lista dinamica di controlli dello stesso tipo | `new FormArray([ new FormControl('') ])`   |
.
---



## Cosa sono i Validator in Angular?

I **validatori** sono **funzioni** che Angular usa per controllare se un campo di input è **valido** o meno, in base a una regola (es. obbligatorio, minimo numero di caratteri, pattern, ecc.). Servono a **verificare i dati inseriti dall'utente**.

Angular offre **validatori predefiniti (built-in)** come:

* `required` → il campo è obbligatorio
* `minLength(n)` / `maxLength(n)` → numero minimo/massimo di caratteri
* `pattern('regex')` → il valore deve rispettare una RegEx
* `email` → valida un'email corretta

## 🧰 **Come si usano?**

### 🔸 *Nei Template-driven Forms*

Nel **template HTML**, aggiungendo gli attributi direttamente all'input:

```html
<input name="email" [(ngModel)]="email" required email />
```

Angular applicherà le regole e aggiornerà lo stato del form (`valid`, `invalid`, ecc.).

### 🔹 *Nei Reactive Forms*

Nel **TypeScript**, dentro `FormControl`, si usa la funzione `Validators`:

```ts
form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email])
});
```

Oppure per un solo validator:

```ts
new FormControl('', Validators.required)
```

## 🛠️ **Come creare un Validator Custom**

Un **custom validator** è una funzione che **ritorna un oggetto di errore** se non è valido, oppure `null` se è valido.

### 🔹 Per Reactive Forms

```ts
import { AbstractControl, ValidationErrors } from '@angular/forms';

export function noNumberValidator(control: AbstractControl): ValidationErrors | null {
  const hasNumber = /\d/.test(control.value);
  return hasNumber ? { noNumber: true } : null;
}
```

E si usa così:

```ts
name = new FormControl('', [noNumberValidator]);
```

### 🔸 Per Template-driven Forms

Serve un **direttiva custom validator** con il decoratore `@Directive`. È più complesso per un profilo junior, ma si può fare:

```ts
@Directive({
  selector: '[noNumber]',
  providers: [{
    provide: NG_VALIDATORS,
    useExisting: forwardRef(() => NoNumberDirective),
    multi: true
  }]
})
export class NoNumberDirective implements Validator {
  validate(control: AbstractControl): ValidationErrors | null {
    const hasNumber = /\d/.test(control.value);
    return hasNumber ? { noNumber: true } : null;
  }
}
```

Nel template si usa come attributo:

```html
<input name="name" ngModel noNumber />
```

## ✅ **In sintesi:**

* I **validatori** servono per controllare la validità degli input.
* Si possono usare in entrambi i tipi di form.
* Si possono **combinare**, **riutilizzare** e **creare su misura (custom)**.
* Sono importanti per gestire l'esperienza utente e prevenire errori.

## Come mostrare messaggi di errore nei form Angular

Per mostrare messaggi di errore agli utenti in base alla validazione dei campi nei form Angular, puoi seguire approcci specifici sia per i **Template-driven Forms** che per i **Reactive Forms**.

### 🟠 Template-driven Forms

Nei Template-driven Forms, la logica di validazione è gestita direttamente nel template HTML. Puoi utilizzare le direttive Angular come `required`, `minlength`, `pattern`, ecc., per applicare le regole di validazione. Per mostrare messaggi di errore:

1. **Aggiungi una variabile di riferimento locale** al controllo del form:

   ```html
   <input name="email" [(ngModel)]="email" required email #emailRef="ngModel" />
   ```

2. **Visualizza il messaggio di errore** quando il controllo è stato toccato (`touched`) e non è valido (`invalid`):

   ```html
   <div *ngIf="emailRef.invalid && emailRef.touched">
     <small class="text-danger" *ngIf="emailRef.errors?.required">L'email è obbligatoria.</small>
     <small class="text-danger" *ngIf="emailRef.errors?.email">Formato email non valido.</small>
   </div>
   ```

Questo approccio assicura che i messaggi di errore siano mostrati solo dopo che l'utente ha interagito con il campo.

### 🟢 Reactive Forms

Nei Reactive Forms, la logica di validazione è definita nel componente TypeScript. Per mostrare messaggi di errore nel template:

1. **Definisci il form e i suoi controlli** nel componente:

   ```typescript
   this.form = new FormGroup({
     email: new FormControl('', [Validators.required, Validators.email])
   });
   ```

2. **Nel template HTML**, utilizza `formControlName` per collegare il controllo e mostra i messaggi di errore in base allo stato del controllo:

   ```html
   <input type="email" formControlName="email" />
   <div *ngIf="form.get('email')?.invalid && form.get('email')?.touched">
     <small class="text-danger" *ngIf="form.get('email')?.errors?.required">L'email è obbligatoria.</small>
     <small class="text-danger" *ngIf="form.get('email')?.errors?.email">Formato email non valido.</small>
   </div>
   ```

In questo modo, i messaggi di errore saranno visibili solo dopo che l'utente ha interagito con il campo e se ci sono errori di validazione.

### ✅ Suggerimenti Generali

* **Mostra i messaggi di errore solo dopo l'interazione dell'utente** con il campo, utilizzando le proprietà `touched` o `dirty`, per evitare di mostrare errori prematuramente.
* **Personalizza i messaggi di errore** per fornire indicazioni chiare su come correggere l'input.
* **Utilizza classi CSS** per evidenziare visivamente i campi non validi, ad esempio cambiando il colore del bordo o dello sfondo.