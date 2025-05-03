# Domande e Risposte per Colloquio Front-End Junior (Angular, TypeScript, JS, HTML, CSS)

---
### Che cos'è Angular?

**Risposta:**  
Angular è un framework front-end open-source, sviluppato da Google, che permette di costruire applicazioni web moderne e scalabili, in particolare Single Page Applications (SPA). È basato su un'architettura a componenti, dove ogni parte dell'interfaccia utente è rappresentata da un componente. Utilizza TypeScript, che è un superset di JavaScript, per offrire tipizzazione statica, il che aiuta a prevenire errori a tempo di compilazione e migliora la manutenzione del codice. Angular include anche un sistema di routing per la navigazione, un modulo di gestione dello stato tramite servizi, e un potente sistema di dependency injection. Inoltre, sfrutta tecniche avanzate come il two-way data binding, che sincronizza automaticamente il modello con la vista, e il change detection, per ottimizzare le performance.
---

### ❓ **Che cos’è un componente in Angular?**

**Risposta:**
"In Angular, un componente è una parte fondamentale dell'applicazione che collega la logica di business al template dell'interfaccia utente.
Ogni componente è una **direttiva speciale** che include un template HTML, ed è definito da una classe TypeScript decorata con `@Component`.

Il decoratore `@Component` fornisce informazioni su come il componente deve comportarsi. Questi metadati includono:

* **`selector`**: il nome con cui inseriamo il componente nell'HTML (es. `<app-header></app-header>`),
* **`template` o `templateUrl`**: il codice HTML del componente, scritto inline o in un file esterno,
* **`styles` o `styleUrls`**: gli stili del componente, inline o in file esterni CSS/SASS/SCSS.

In breve, un componente unisce logica, interfaccia e stile in una singola unità modulare e riutilizzabile, ideale per costruire applicazioni Single Page."
---




### Che cos'è una direttiva in Angular?

**Risposta:**  
In Angular, una direttiva è una classe che può modificare il comportamento o l’aspetto degli elementi nel DOM. Si applica direttamente sugli elementi HTML tramite attributi.  
Esistono diversi tipi di direttive:

1. **Direttive strutturali:** modificano la struttura del DOM, ad esempio `*ngIf` (condizionale) o `*ngFor` (ciclo).
2. **Direttive di attributo:** cambiano l’aspetto o il comportamento di un elemento, ad esempio `ngClass`, `ngStyle`.
3. **Componenti:** sono considerate direttive speciali, che hanno un template associato.
4. **Direttive personalizzate:** create dallo sviluppatore per aggiungere un comportamento personalizzato agli elementi del DOM.

Le direttive non hanno template propri (a parte i componenti) e vengono usate per estendere il comportamento degli elementi HTML in modo dichiarativo.
---

### ❓ **Che cosa sono le Pipe in Angular?**

**Risposta:**
Le *Pipe* in Angular sono strumenti che permettono di trasformare il modo in cui un dato viene visualizzato nel **template**, senza modificare il dato originale nel **componente**.
Vengono utilizzate nel template con il simbolo pipe (`|`), per esempio:

```html
{{ today | date:'dd/MM/yyyy' }}
```

Una Pipe è una classe TypeScript che implementa l’interfaccia `PipeTransform` e definisce un metodo `transform()`, dove avviene la logica di trasformazione.

🔧 **Esempi di pipe già presenti in Angular (built-in):**

* `date`: formatta una data (`{{ today | date:'shortDate' }}`)
* `uppercase` / `lowercase`: trasforma il testo in maiuscolo o minuscolo
* `titlecase`: rende maiuscola la prima lettera di ogni parola
* `currency`: formatta un numero come valuta (`{{ amount | currency:'EUR' }}`)
* `percent`: trasforma un numero in percentuale

🔁 **Pipe personalizzate:**
È possibile creare pipe proprie per esigenze specifiche, ad esempio per accorciare un testo troppo lungo o per tradurre codici.
---

### ❓ **Che differenza c’è tra Pipe pura e impura?**

* Una **Pipe pura** (impostazione predefinita) esegue `transform()` solo quando cambia l’input. Angular memorizza il risultato (memoization) per evitare ricalcoli inutili. Ottima per dati "stabili".

* Una **Pipe impura** (pure: false) viene rieseguita ad ogni ciclo di *change detection*, anche se il valore non è cambiato. È utile con dati che cambiano frequentemente (es. array modificati internamente, Observable non gestiti con `async`).

⚠️ Le pipe impure sono più costose in termini di prestazioni e vanno usate solo quando necessario.
---


### ❓ **Come si crea una Pipe personalizzata in Angular?**

**Risposta:**
Per creare una Pipe personalizzata, si usa il decoratore `@Pipe` e si implementa l’interfaccia `PipeTransform`.
Ad esempio, una pipe che accorcia un testo troppo lungo e aggiunge "…" alla fine:

#### 📦 Esempio: `truncate.pipe.ts`

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20): string {
    if (!value) return '';
    return value.length > limit ? value.substring(0, limit) + '…' : value;
  }
}
```

#### 📄 Utilizzo nel template:

```html
<p>{{ longText | truncate:30 }}</p>
```

✅ **Importante:** la pipe deve essere dichiarata nel `declarations` del tuo modulo.
---

Certo! La tua risposta è già un buon punto di partenza, ma possiamo renderla più chiara e più concisa per un colloquio. Ecco una versione migliorata della risposta su **cos'è la Dependency Injection in Angular**:
---

### ❓ **Cos’è la Dependency Injection in Angular?**

**Risposta:**
"La **Dependency Injection** (DI) è un pattern di progettazione che permette di iniettare le dipendenze (oggetti necessari) all'interno di una classe, anziché farle creare direttamente dalla classe stessa. In questo modo, si migliora la testabilità, la manutenibilità e la modularità del codice.

In Angular, la DI è gestita dal **framework attraverso i provider**. Quando un componente, un servizio o un altro oggetto ha bisogno di una dipendenza, Angular la fornisce automaticamente. Le dipendenze vengono dichiarate tramite il decoratore `@Injectable()` e vengono poi configurate nei **provider**.

**I principali metodi di configurazione dei provider** sono:

* **`useClass`**: inietta una classe specifica come dipendenza,
* **`useFactory`**: usa una funzione per creare la dipendenza,
* **`useValue`**: fornisce un valore diretto come dipendenza,
* **`useExisting`**: inietta una dipendenza già esistente.

I provider possono essere configurati a vari livelli:

* **`providedIn: 'root'`**: rende il servizio globale per tutta l’applicazione,
* **`providedIn: 'any'`**: crea una nuova istanza per ogni modulo lazy-loaded,
* **`providers` a livello di componente**: specificano un servizio solo per quel componente o modulo.

In sintesi, la DI in Angular consente di gestire le dipendenze in modo dichiarativo, migliorando l'organizzazione e la flessibilità dell'applicazione."
---

### Punti chiave:

* **Pattern DI**: consente di iniettare dipendenze anziché crearle manualmente.
* **Provider**: Angular gestisce la DI tramite provider configurabili a vari livelli.
* **Testabilità e manutenibilità**: migliorano grazie alla separazione delle preoccupazioni.
.
---

## 🔧 Cos'è la Dependency Injection (DI) in Angular?

La **Dependency Injection** è un meccanismo con cui Angular **fornisce automaticamente** le dipendenze (come servizi, oggetti o funzioni) a componenti, direttive o altri servizi, **senza che debbano crearle da soli**.

Questo avviene tramite i **provider**, che dicono ad Angular **come creare o recuperare** un’istanza di una dipendenza.

---

## 🧰 I principali provider: useClass, useValue, useExisting, useFactory

Angular permette di personalizzare il comportamento della DI con **provider specifici**:
.
---

### 🔹 `useClass` (default)

Dichiara che la dipendenza deve essere creata **usando una determinata classe**.

```ts
class MyService {}
providers: [{ provide: 'Token', useClass: MyService }]
```

> ✅ Angular creerà un'istanza di `MyService` quando qualcuno richiede `'Token'`.
.
---

### 🔹 `useValue`

Fornisce **un valore letterale o costante** (oggetto, stringa, numero, ecc.).

```ts
const CONFIG = { apiUrl: 'https://api.example.com' };
providers: [{ provide: 'AppConfig', useValue: CONFIG }]
```

> ✅ Utile per configurazioni, token, costanti.

---

### 🔹 `useExisting`

Fornisce **un alias di un provider già esistente**. In pratica, due token condividono la stessa istanza.

```ts
class AuthService {}
providers: [
  AuthService,
  { provide: 'Logger', useExisting: AuthService }
]
```

> ✅ Se qualcuno richiede `'Logger'`, riceve l’istanza di `AuthService`.
.
---

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

> ✅ Utile quando l’istanza deve essere creata in base a condizioni dinamiche (configurazioni, ambiente, ecc.).

---

## 🎓 Quando si usano?

| Provider      | Quando usarlo                                                      |
| ------------- | ------------------------------------------------------------------ |
| `useClass`    | Per fornire una classe standard o una alternativa (mock, stub).    |
| `useValue`    | Per fornire valori statici (config, costanti, oggetti).            |
| `useExisting` | Quando vuoi evitare duplicazione e condividere un'istanza.         |
| `useFactory`  | Quando hai bisogno di creare dinamicamente l'oggetto da iniettare. |

---

## ✅ Conclusione

Per un colloquio **junior**, è sufficiente dire:

> *"La dependency injection in Angular permette di fornire in automatico le dipendenze necessarie ai componenti o servizi. Possiamo controllare il modo in cui Angular crea o recupera queste dipendenze usando provider come `useClass`, `useValue`, `useExisting` e `useFactory`, a seconda del tipo di oggetto o del comportamento che vogliamo ottenere."*
.
---
### Decoratore @Injectable():

Il decoratore `@Injectable()` è comunemente associato ai servizi in Angular, ma non è esclusivo di essi. Questo decoratore è utilizzato per dichiarare una classe come disponibile per l'iniezione delle sue dipendenze tramite il sistema di Dependency Injection (DI) di Angular.([HowDev][1])

### ✅ **Quando utilizzare `@Injectable()`**

* **Servizi**: È fondamentale utilizzare `@Injectable()` quando una classe ha delle dipendenze da iniettare. Ad esempio, se un servizio dipende da `HttpClient`, è necessario dichiararlo come `@Injectable()` per consentire ad Angular di risolvere correttamente le sue dipendenze. ([iq.js.org][2])

* **Componenti**: Anche se meno comune, è possibile utilizzare `@Injectable()` su un componente se questo ha delle dipendenze da iniettare e si desidera che Angular gestisca la loro creazione. Tuttavia, questa pratica non è standard e potrebbe non essere ideale in tutti i casi.

### 🧠 **Perché è importante `@Injectable()`**

Il decoratore `@Injectable()` fornisce metadati che permettono ad Angular di sapere come creare e gestire le istanze delle classi con dipendenze. Senza di esso, Angular potrebbe non essere in grado di risolvere correttamente le dipendenze, portando a errori durante l'esecuzione.&#x20;

### 📌 **Conclusione**

Sebbene `@Injectable()` sia comunemente utilizzato nei servizi, può essere applicato a qualsiasi classe che necessita di avere le proprie dipendenze iniettate da Angular. La sua applicazione non è limitata ai servizi, ma è uno strumento fondamentale per abilitare la Dependency Injection in Angular.
---
Certo! Ecco una **versione migliorata, fluida e adatta a un profilo junior** per rispondere alla domanda: **"Come viene gestito il data binding in Angular?"**. L’obiettivo è trasmettere **chiarezza**, usare i **termini giusti senza complicare troppo**, ed essere pronto anche a **eventuali domande di approfondimento**.

---

### ❓ Come viene gestito il data binding in Angular?

**Risposta:**

> "In Angular, il data binding è il modo in cui colleghiamo i dati tra la logica scritta in TypeScript (nel componente) e il template HTML. Angular gestisce il binding in quattro modalità principali:

---

#### 1. **Interpolazione (`{{ }}`)**

Serve per mostrare un valore nel template, leggendo direttamente dal componente.
**Esempio:** `{{ titolo }}` → stampa il valore della variabile `titolo` definita nel file `.ts`.

---

#### 2. **Property Binding (`[property]="valore"`)**

Collega un valore del componente a una proprietà di un elemento HTML o di un componente figlio.
**Esempio:** `[src]="immagineUrl"` → imposta dinamicamente l’attributo `src` di un’immagine.

---

#### 3. **Event Binding (`(evento)="funzione()"`)**

Permette al template di reagire a eventi (come click, input, submit), chiamando una funzione nel file `.ts`.
**Esempio:** `(click)="salva()"` → chiama la funzione `salva()` quando si clicca su un bottone.

---

#### 4. **Two-Way Binding (`[(ngModel)]`)**

Collega una variabile in entrambe le direzioni: ogni modifica nel template aggiorna il componente, e viceversa.
Per usarlo serve importare il `FormsModule`.
**Esempio:** `[(ngModel)]="nome"` → sincronizza un campo di input con la variabile `nome`.
.
---

### 📦 Comunicazione tra componenti

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
  Si usa l’event binding con `@Output()` e `EventEmitter`.

  ```ts
  // Figlio
  @Output() inviaDati = new EventEmitter<string>();
  inviaDati.emit('ciao');

  // Padre
  <app-figlio (inviaDati)="riceviDati($event)">
  ```

---

### ✅ In sintesi:

"Il data binding in Angular permette lo scambio di informazioni tra la logica dell'app e l'interfaccia utente, in modo semplice ed efficace. È uno dei concetti chiave del framework."
---

### ✅ Se vuoi **mostrare** un numero (o qualsiasi valore) nell’HTML:

Usa **interpolazione**:

```html
<p>{{ numero }}</p>
```

Nel file TypeScript:

```ts
numero = 42;
```

Questo è il modo **più diretto e leggibile** per mostrare un valore nel template.
---

### 🧠 Ma se parliamo **specificamente di property binding**:

Il **property binding** serve per collegare proprietà di elementi HTML o componenti (come `value`, `src`, `disabled`, ecc.) a valori del componente.

Esempio con un `input` che mostra un numero:

```html
<input [value]="numero" readonly />
```

Qui non lo "stampi" direttamente nel DOM, ma assegni il numero alla proprietà `value` dell’input.
---

### In sintesi:

* **Per mostrare valori nel template** → usa `{{ numero }}`
* **Per passare dati a proprietà di elementi HTML** → usa `[property]="valore"`
.
---
Certo! Ecco una **risposta migliorata, chiara, completa ma adatta a un colloquio da front-end junior** alla domanda:

---

### ❓ **Come si gestiscono i form in Angular?**

**Risposta:**

In Angular i form possono essere gestiti in due modi: **Template-driven Forms** e **Reactive Forms**. Entrambi servono per raccogliere e validare input dell’utente, ma seguono approcci diversi.
---

#### 🟠 **Template-driven Forms**

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
.
---

#### 🟢 **Reactive Forms**

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
.
---

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
Certo! Ecco una spiegazione completa ma adatta a un **profilo front-end junior**, che include cosa sono i **validatori**, come crearli **custom**, e come si usano nei due approcci di Angular: **template-driven forms** e **reactive forms**.

---

## ❓ **Cosa sono i Validator in Angular?**

I **validatori** sono **funzioni** che Angular usa per controllare se un campo di input è **valido** o meno, in base a una regola (es. obbligatorio, minimo numero di caratteri, pattern, ecc.). Servono a **verificare i dati inseriti dall’utente**.

Angular offre **validatori predefiniti (built-in)** come:

* `required` → il campo è obbligatorio
* `minLength(n)` / `maxLength(n)` → numero minimo/massimo di caratteri
* `pattern('regex')` → il valore deve rispettare una RegEx
* `email` → valida un'email corretta

---

## 🧰 **Come si usano?**

### 🔸 *Nei Template-driven Forms*

Nel **template HTML**, aggiungendo gli attributi direttamente all’input:

```html
<input name="email" [(ngModel)]="email" required email />
```

Angular applicherà le regole e aggiornerà lo stato del form (`valid`, `invalid`, ecc.).

---

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
.
---

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
.
---

## ✅ **In sintesi:**

* I **validatori** servono per controllare la validità degli input.
* Si possono usare in entrambi i tipi di form.
* Si possono **combinare**, **riutilizzare** e **creare su misura (custom)**.
* Sono importanti per gestire l’esperienza utente e prevenire errori.
.
---

Per mostrare messaggi di errore agli utenti in base alla validazione dei campi nei form Angular, puoi seguire approcci specifici sia per i **Template-driven Forms** che per i **Reactive Forms**.
---

## 🟠 Template-driven Forms

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
---

## 🟢 Reactive Forms

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
---

## ✅ Suggerimenti Generali

* **Mostra i messaggi di errore solo dopo l'interazione dell'utente** con il campo, utilizzando le proprietà `touched` o `dirty`, per evitare di mostrare errori prematuramente.
* **Personalizza i messaggi di errore** per fornire indicazioni chiare su come correggere l'input.
* **Utilizza classi CSS** per evidenziare visivamente i campi non validi, ad esempio cambiando il colore del bordo o dello sfondo.
.
---
