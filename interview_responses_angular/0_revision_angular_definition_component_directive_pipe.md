# Domande e Risposte per Colloquio Front-End Junior (Angular, TypeScript, JS, HTML, CSS)

## Che cos'è Angular?

**Risposta:**  

Angular è un framework front-end open-source sviluppato da Google per costruire applicazioni web moderne e scalabili, in particolare Single Page Applications (SPA). Le sue caratteristiche principali includono:

- **Architettura a componenti**: ogni parte dell'interfaccia utente è un componente riutilizzabile
- **TypeScript**: usa questo superset di JavaScript per offrire tipizzazione statica, riducendo gli errori durante lo sviluppo
- **Routing**: sistema integrato per la navigazione tra le diverse viste dell'applicazione
- **Dependency Injection**: fornitura di oggetti tramite servizi e altri provider
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
---
