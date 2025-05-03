# Colloquio Tecnico: Domande e Risposte per Front-End Junior (Angular, TypeScript, JS, HTML, CSS)

## Intervistatore: "Puoi spiegarmi cos'è Angular?"

**Candidato:** "Angular è un framework front-end open-source sviluppato da Google, usato principalmente per costruire applicazioni web moderne, in particolare Single Page Applications. La sua caratteristica distintiva è l'architettura basata su componenti, dove ogni parte dell'interfaccia utente è un componente riutilizzabile.

Angular usa TypeScript, che aggiunge la tipizzazione statica a JavaScript, aiutando a prevenire molti errori comuni durante lo sviluppo. Include varie funzionalità utili come:
- Un sistema di routing per la navigazione tra le pagine
- Servizi per gestire la logica e i dati condivisi
- Two-way data binding che sincronizza automaticamente i dati tra modello e vista
- Dependency injection che semplifica la gestione delle dipendenze
- Un sistema di detection dei cambiamenti che ottimizza le performance

È un framework completo che fornisce tutti gli strumenti necessari per sviluppare applicazioni robuste e scalabili."

## Intervistatore: "Cos'è un componente in Angular e come funziona?"

**Candidato:** "In Angular, un componente è il blocco di costruzione fondamentale dell'interfaccia utente. È essenzialmente una direttiva speciale che combina:

- Una classe TypeScript che gestisce la logica e i dati
- Un template HTML che definisce la struttura visiva
- Fogli di stile CSS/SCSS che definiscono l'aspetto

Ogni componente è decorato con `@Component()`, che fornisce metadati come:
- `selector`: il nome del tag HTML che useremo per inserire il componente (es. `<app-header>`)
- `templateUrl` o `template`: il codice HTML associato al componente
- `styleUrls` o `styles`: gli stili CSS/SCSS del componente

Ad esempio, un semplice componente potrebbe essere strutturato così:

```typescript
@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
export class HeaderComponent {
  title = 'La mia applicazione';
  
  onMenuClick() {
    // Logica per gestire il click sul menu
  }
}
```

Quello che rende i componenti così potenti è che sono incapsulati e riutilizzabili. Ogni componente gestisce la propria logica e vista, e può essere facilmente riutilizzato in diverse parti dell'applicazione."

## Intervistatore: "Qual è la differenza tra una direttiva e un componente in Angular?"

**Candidato:** "Entrambi sono elementi importanti in Angular, ma con scopi diversi. Un componente è in realtà un tipo speciale di direttiva.

Le direttive sono istruzioni nel DOM che dicono ad Angular come manipolare o trasformare un elemento. Ci sono tre tipi principali:

1. **Direttive strutturali**: modificano la struttura del DOM aggiungendo o rimuovendo elementi. Per esempio:
   - `*ngIf` per mostrare o nascondere elementi in base a una condizione
   - `*ngFor` per iterare e replicare elementi in base a una collezione

2. **Direttive di attributo**: cambiano l'aspetto o il comportamento di un elemento esistente. Per esempio:
   - `ngClass` per aggiungere classi CSS in modo condizionale
   - `ngStyle` per applicare stili dinamicamente

3. **Componenti**: sono direttive speciali che hanno un template HTML associato. A differenza delle altre direttive, creano una propria vista.

La principale differenza è che un componente è sempre associato a un template che definisce una vista, mentre le direttive standard manipolano elementi esistenti senza creare una struttura propria. I componenti sono usati per costruire pezzi dell'interfaccia utente, mentre le direttive per aggiungere comportamenti agli elementi."

## Intervistatore: "Puoi spiegarmi cosa sono le Pipe in Angular e come si usano?"

**Candidato:** "Le Pipe in Angular sono strumenti per trasformare i dati direttamente nel template HTML, prima di mostrarli all'utente. Sono molto utili perché permettono di formattare i dati senza modificare i valori originali.

Si usano nel template con il simbolo pipe (`|`). Per esempio, se voglio formattare una data, posso scrivere:

```html
<p>Data di creazione: {{ dataCreazione | date:'dd/MM/yyyy' }}</p>
```

Angular include diverse pipe predefinite:
- `date`: per formattare le date
- `uppercase` e `lowercase`: per convertire il testo in maiuscolo o minuscolo
- `currency`: per formattare numeri come valute
- `percent`: per convertire numeri in percentuali
- `async`: per gestire automaticamente le subscription agli Observable

Posso anche creare pipe personalizzate per esigenze specifiche. Per esempio, se volessi creare una pipe che tronca il testo, potrei scrivere:

```typescript
@Pipe({
  name: 'truncate'
})
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit: number = 20): string {
    if (!value) return '';
    return value.length > limit ? value.substring(0, limit) + '...' : value;
  }
}
```

E poi usarla così nel template:

```html
<p>{{ lungoTesto | truncate:30 }}</p>
```

Le pipe rendono il codice più pulito e la logica di presentazione resta nel template, dove dovrebbe essere."

## Intervistatore: "Sai spiegarmi la differenza tra una Pipe pura e una impura?"

**Candidato:** "Certo! La differenza principale riguarda quando e quanto spesso Angular esegue la trasformazione:

**Le Pipe pure** (impostazione predefinita) eseguono la trasformazione solo quando il valore di input cambia in modo rilevabile. Con 'rilevabile' intendo un nuovo oggetto o valore primitivo, non semplici modifiche interne a un oggetto. Angular ottimizza le prestazioni memorizzando i risultati per evitare di ricalcolare se l'input non cambia.

**Le Pipe impure** vengono eseguite durante ogni ciclo di change detection, anche se l'input non è cambiato. Si dichiarano impostando `pure: false` nei metadati del decoratore `@Pipe`.

Per esempio:
```typescript
@Pipe({
  name: 'filtroElementi',
  pure: false  // Questa è una pipe impura
})
```

In pratica:
- Le **pipe pure** sono adatte per trasformazioni semplici sui dati immutabili (come formattare date o numeri)
- Le **pipe impure** sono utili quando si lavora con dati che cambiano internamente (come array o oggetti che vengono modificati) o quando si dipende da stato esterno

Le pipe impure offrono maggiore flessibilità ma possono impattare sulle prestazioni, quindi vanno usate con attenzione. Un esempio comune di pipe impura è `async`, che si aggiorna quando l'Observable emette nuovi valori."

## Intervistatore: "Cosa puoi dirmi sulla Dependency Injection in Angular? Come funziona?"

**Candidato:** "La Dependency Injection in Angular è un pattern di design fondamentale che permette di fornire istanze di classi (dipendenze) a componenti, servizi o altre classi, senza che questi debbano crearle direttamente.

In pratica, invece di scrivere:
```typescript
class MioComponente {
  constructor() {
    this.servizio = new MioServizio();  // Creazione diretta, non ideale
  }
}
```

Con la DI, scrivo:
```typescript
class MioComponente {
  constructor(private servizio: MioServizio) {
    // Angular fornisce l'istanza di MioServizio
  }
}
```

Questo è vantaggioso perché:
1. Migliora la testabilità: posso facilmente sostituire le dipendenze con mock durante i test
2. Rende il codice più modulare e manutenibile
3. Permette di condividere istanze singleton tra vari componenti

Angular usa il decoratore `@Injectable()` per indicare che una classe può ricevere dipendenze:

```typescript
@Injectable({
  providedIn: 'root'  // Disponibile a livello di applicazione
})
export class MioServizio {
  constructor(private http: HttpClient) {}  // Anche MioServizio può ricevere dipendenze
}
```

Angular offre diversi modi per configurare come fornire le dipendenze attraverso i provider:

- **useClass**: fornisce un'istanza di una classe specifica
- **useValue**: fornisce un valore letterale
- **useFactory**: usa una funzione per creare l'istanza
- **useExisting**: fa riferimento a un token provider esistente

I provider possono essere configurati a vari livelli nell'applicazione:
- A livello globale con `providedIn: 'root'`
- A livello di modulo nell'array `providers`
- A livello di componente nei metadati del componente

La DI è uno dei punti di forza di Angular, rendendo il codice più pulito e facilmente manutenibile."

## Intervistatore: "Come funziona il data binding in Angular? Puoi spiegarmi i diversi tipi?"

**Candidato:** "Il data binding in Angular è il meccanismo che collega i dati del componente TypeScript con il template HTML. È fondamentale perché permette di sincronizzare automaticamente la vista con i dati. Angular offre quattro tipi principali di data binding:

1. **Interpolazione** (`{{ espressione }}`): è il modo più semplice per mostrare dati nel template.
   ```html
   <h1>{{ titolo }}</h1>
   <p>Ciao, {{ utente.nome }}</p>
   ```
   È unidirezionale: i dati fluiscono dal componente al template.

2. **Property Binding** (`[proprietà]="espressione"`): permette di impostare proprietà di elementi HTML o componenti con valori del componente.
   ```html
   <img [src]="immagineUrl">
   <button [disabled]="nonAttivo">Salva</button>
   ```
   Anche questo è unidirezionale, dal componente al template.

3. **Event Binding** (`(evento)="gestoreEvento()"`): permette di rispondere a eventi dell'utente chiamando metodi del componente.
   ```html
   <button (click)="onSalva()">Salva</button>
   <input (input)="onInput($event)">
   ```
   È unidirezionale, ma dal template al componente.

4. **Two-way Binding** (`[(ngModel)]="proprietà"`): combina property binding ed event binding, permettendo la sincronizzazione in entrambe le direzioni.
   ```html
   <input [(ngModel)]="nome">
   ```
   Qualsiasi modifica nel campo di input aggiorna la variabile `nome` nel componente e viceversa.

Il data binding è fondamentale anche per la comunicazione tra componenti:
- Dal genitore al figlio si usa `@Input()` con property binding
- Dal figlio al genitore si usa `@Output()` con EventEmitter e event binding

Questi meccanismi eliminano gran parte del codice boilerplate che avremmo dovuto scrivere manualmente per sincronizzare la vista con i dati, rendendo lo sviluppo più efficiente e il codice più pulito."

## Intervistatore: "Quali approcci offre Angular per la gestione dei form e quali sono le differenze?"

**Candidato:** "Angular offre due approcci principali per gestire i form: Template-driven Forms e Reactive Forms. Entrambi hanno i loro punti di forza ma seguono filosofie diverse.

**Template-driven Forms:**
- Si basano sul modulo `FormsModule`
- La logica del form è definita principalmente nel template HTML
- Usano la direttiva `ngModel` per il two-way binding
- Le validazioni sono dichiarate come attributi HTML standard (required, minlength, ecc.)
- Sono più semplici da implementare e ideali per form basilari
- Esempio:
  ```html
  <form #mioForm="ngForm" (ngSubmit)="onSubmit(mioForm)">
    <input name="email" [(ngModel)]="utente.email" required email>
    <button type="submit" [disabled]="!mioForm.valid">Invia</button>
  </form>
  ```

**Reactive Forms:**
- Si basano sul modulo `ReactiveFormsModule`
- La logica del form è definita nel codice TypeScript
- Usano classi come FormControl, FormGroup e FormArray
- Le validazioni sono definite programmaticamente
- Offrono maggior controllo e sono più testabili
- Permettono di gestire form dinamici e complessi
- Esempio:
  ```typescript
  // Nel componente
  this.form = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email])
  });
  ```
  ```html
  <form [formGroup]="form" (ngSubmit)="onSubmit()">
    <input formControlName="email">
    <button type="submit" [disabled]="form.invalid">Invia</button>
  </form>
  ```

**Quando usare l'uno o l'altro:**
- **Template-driven**: per form semplici con poca logica, dove la velocità di sviluppo è prioritaria
- **Reactive**: per form complessi con validazioni personalizzate o dipendenti da altri campi, form dinamici, o quando la testabilità è importante

Personalmente, anche se i template-driven sono più veloci da implementare inizialmente, preferisco i reactive forms per progetti di media o grande complessità perché offrono struttura e manutenibilità migliori nel lungo termine."

## Intervistatore: "Cosa sono i validator in Angular e come si implementano nei form?"

**Candidato:** "I validator in Angular sono funzioni che verificano se un input dell'utente rispetta determinati criteri. Sono essenziali per garantire che i dati inseriti siano validi prima di inviarli al server.

Angular fornisce diversi validator predefiniti:

- `required`: verifica che il campo sia compilato
- `minLength`/`maxLength`: controlla la lunghezza minima/massima
- `pattern`: verifica che il valore corrisponda a un pattern regex
- `email`: controlla che il valore sia un'email valida

**Implementazione nei Template-driven Forms:**

Nei form basati su template, i validator si aggiungono come attributi HTML:

```html
<form #mioForm="ngForm">
  <input type="email" 
         name="email" 
         [(ngModel)]="utente.email" 
         required 
         email 
         #emailControl="ngModel">
  
  <div *ngIf="emailControl.invalid && emailControl.touched">
    <span *ngIf="emailControl.errors?.required">L'email è obbligatoria</span>
    <span *ngIf="emailControl.errors?.email">Formato email non valido</span>
  </div>
</form>
```

**Implementazione nei Reactive Forms:**

Nei form reattivi, i validator sono definiti nel componente:

```typescript
import { FormGroup, FormControl, Validators } from '@angular/forms';

// Nel componente
this.form = new FormGroup({
  email: new FormControl('', [
    Validators.required,
    Validators.email
  ]),
  password: new FormControl('', [
    Validators.required,
    Validators.minLength(8)
  ])
});
```

```html
<form [formGroup]="form">
  <input type="email" formControlName="email">
  
  <div *ngIf="form.get('email')?.invalid && form.get('email')?.touched">
    <span *ngIf="form.get('email')?.errors?.required">L'email è obbligatoria</span>
    <span *ngIf="form.get('email')?.errors?.email">Formato email non valido</span>
  </div>
</form>
```

**Validator personalizzati:**

Posso anche creare validator personalizzati per esigenze specifiche. Ecco un esempio di validator che verifica che una password non contenga la parola 'password':

```typescript
function noPasswordValidator(control: AbstractControl): ValidationErrors | null {
  const value = control.value;
  if (value && value.toLowerCase().includes('password')) {
    return { insecurePassword: true };
  }
  return null;
}

// Utilizzo
this.form = new FormGroup({
  password: new FormControl('', [
    Validators.required,
    noPasswordValidator
  ])
});
```

I validator sono fondamentali per garantire una buona user experience, mostrando feedback immediato sugli errori di input prima dell'invio del form."

## Intervistatore: "Come gestisci i messaggi di errore nei form Angular?"

**Candidato:** "La gestione dei messaggi di errore nei form è fondamentale per fornire un feedback chiaro all'utente. Ecco come lo gestisco nei due tipi di form:

**Nei Template-driven Forms:**

Uso una variabile locale di riferimento per accedere allo stato del controllo:

```html
<input type="email" 
       name="email" 
       [(ngModel)]="utente.email" 
       required 
       email 
       #emailControl="ngModel">

<div class="error-messages" *ngIf="emailControl.invalid && (emailControl.touched || formSubmitted)">
  <small class="text-danger" *ngIf="emailControl.errors?.required">
    Per favore inserisci un'email
  </small>
  <small class="text-danger" *ngIf="emailControl.errors?.email">
    Per favore inserisci un'email valida
  </small>
</div>
```

**Nei Reactive Forms:**

Accedo alle proprietà del FormControl tramite il FormGroup:

```html
<input type="email" formControlName="email">

<div class="error-messages" *ngIf="form.get('email')?.invalid && (form.get('email')?.touched || formSubmitted)">
  <small class="text-danger" *ngIf="form.get('email')?.errors?.required">
    Per favore inserisci un'email
  </small>
  <small class="text-danger" *ngIf="form.get('email')?.errors?.email">
    Per favore inserisci un'email valida
  </small>
</div>
```

**Approcci più avanzati:**

Per evitare duplicazione e rendere il codice più mantenibile, uso spesso metodi helper:

```typescript
// Nel componente
isFieldInvalid(fieldName: string): boolean {
  const field = this.form.get(fieldName);
  return field ? field.invalid && (field.touched || this.formSubmitted) : false;
}

getErrorMessage(fieldName: string): string {
  const field = this.form.get(fieldName);
  if (!field || !field.errors) return '';
  
  if (field.errors.required) return 'Questo campo è obbligatorio';
  if (field.errors.email) return 'Inserisci un'email valida';
  if (field.errors.minlength) 
    return `Deve contenere almeno ${field.errors.minlength.requiredLength} caratteri`;
  
  // Altri errori...
  return 'Campo non valido';
}
```

```html
<input type="email" formControlName="email">

<div class="error-messages" *ngIf="isFieldInvalid('email')">
  <small class="text-danger">{{ getErrorMessage('email') }}</small>
</div>
```

**Considerazioni generali:**

1. Mostro gli errori solo dopo che l'utente ha interagito con il campo (touched) o dopo il submit
2. Uso colori e icone per aiutare l'utente a identificare rapidamente gli errori
3. Fornisco messaggi chiari che spiegano come correggere l'errore, non solo che c'è un errore
4. Per form complessi, valuto anche la possibilità di mostrare un riepilogo degli errori all'inizio del form

Questa strategia offre un feedback immediato e chiaro, migliorando l'esperienza utente durante la compilazione dei form."
