# Cos’è Angular?

**Angular** è un framework frontend open-source sviluppato da Google, utilizzato per costruire applicazioni web scalabili e soprattutto SPA.

Le caratteristiche principali di Angular comprendono: un’architettura basata su componenti (ogni componente è riutilizzabile), è scritto in **TypeScript** (superset di JavaScript tipizzato per un maggior controllo e miglior debug), usa il **routing** per cambiare vista senza dover ricaricare l’applicazione (principio di SPA), usa la **Dependency Injection** per fornire le dipendenze a una classe tramite servizi (si usa il decoratore `@Injectable` per rendere una classe iniettabile), utilizza il **data binding** per sincronizzare i dati tra il template HTML e il file TS, gestione avanzata dei form tramite **Reactive Form**, **Template Driven Form**, e **Form Array**.

---

# Cos’è un Componente?

Un **componente** è la struttura base di Angular. È una direttiva speciale costituita da: una logica applicativa gestita in un file `.ts`, una vista gestita in un template HTML, e uno stile specifico (opzionale) gestito nel foglio CSS o SCSS.

Ogni componente è definito con un decoratore `@Component` che fornisce i metadati essenziali:

```typescript
@Component({
  selector: 'app-nome-componente',
  templateUrl: './user.html',
  styleUrls: ['./user.css']
})
export class NomeComponente {
  // Logica del componente
}
```

Questo approccio permette di riutilizzare i componenti in più elementi dell’applicazione, facilita la manutenibilità e il testing.

---

# Cos’è una Direttiva in Angular?

Le **direttive** sono classi che modificano gli elementi del DOM. Le direttive integrate di Angular gestiscono efficacemente moduli, elenchi, stili ed elementi dell'interfaccia utente.

Le direttive si dividono in tre tipi:

- **Componenti**: direttive speciali con un proprio template associato.
- **Direttive di attributo**: cambiano l’aspetto o il comportamento di un elemento esistente (es. `NgClass`, `ngStyle`, `NgModel`).
- **Direttive strutturali**: modificano la struttura del DOM e i relativi elementi (`*ngIf`, `*ngFor`, `*ngSwitch`).

---

# Cosa si usa al posto delle direttive strutturali?

Si usa il **Control Flow**, un nuovo metodo più performante e di più facile lettura. Nel template si scrive:

```html
@if (condizione) {
  <template>
}
```

---

# Cos’è una Pipe?

Una **pipe** è un operatore speciale che permette la trasformazione dichiarativa (non altera il valore del dato ma solo come viene visualizzato) dei dati nel template. Le pipe consentono il riutilizzo della logica in diversi template.

Le pipe di Angular utilizzano il carattere `|`.

---

# Data Binding in Angular

È il sistema con il quale avviene il passaggio di dati tra il template HTML (interfaccia utente) e il file TS (business logic).

### One-way data binding:

- **String interpolation**: `{{ dato }}`
- **Property binding**: `[property]="value"`
- **Event binding**: `(event)="method()"`

### Two-way data binding:

Necessita di importare `FormsModule` e si usa con `[(ngModel)]="value"`.

### Component property binding:

Padre → Figlio: `[messaggio]="testo"` con `@Input()`  
Figlio → Padre: `(inviaDati)="riceviDati($event)"` con `@Output()`

---

# Dependency Injection

La **Dependency Injection** è un design pattern che consente a una classe di ricevere le sue dipendenze dall'esterno.

Le classi usano `@Injectable()` e definiscono `providedIn` (`'root'`, modulo, o componente).

L’iniezione può avvenire nel **costruttore** o con `inject()`.
