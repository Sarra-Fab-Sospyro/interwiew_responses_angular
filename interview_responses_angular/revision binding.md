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