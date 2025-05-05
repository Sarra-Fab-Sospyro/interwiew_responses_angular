# 📦 Dependency Injection in Angular

## 🔍 Cos'è la Dependency Injection (DI)?

La **Dependency Injection** (DI) è un pattern fondamentale in Angular che permette a una classe di ottenere dall’esterno le sue dipendenze, invece di crearle da sola. Questo approccio consente ad Angular di gestire **automaticamente** la creazione, la condivisione e l'eliminazione di queste dipendenze, favorendo uno sviluppo più **modulare**, **manutenibile** e **testabile**.
.
---

## 🎯 Vantaggi principali

* ✅ **Codice più pulito**: separazione delle responsabilità tra le classi.
* 🧪 **Testabilità migliorata**: più semplice sostituire le dipendenze con mock nei test.
* ♻️ **Riusabilità aumentata**: le classi non dipendono da implementazioni concrete.
* 🔧 **Manutenibilità facilitata**: è facile aggiornare o sostituire una dipendenza.
.
---

## ⚙️ Come funziona la DI in Angular

### 1. 📥 Definizione delle dipendenze

Le classi che devono essere iniettate vanno annotate con `@Injectable()`:

```ts
@Injectable({ providedIn: 'root' }) // Registrazione automatica a livello globale
export class UserService {
  getUsers() {
    return [...];
  }
}
```
.
---

### 2. 🚚 Registrazione dei provider

Angular può registrare provider a diversi livelli e in vari modi:

| Livello          | Comportamento                                                             |
| ---------------- | ------------------------------------------------------------------------- |
| **Applicazione** | `providedIn: 'root'` → una singola istanza globale                        |
| **Modulo**       | In `@NgModule({ providers: [...] })` → istanza per modulo                 |
| **Componente**   | In `@Component({ providers: [...] })` → istanza isolata per il componente |
.
---

### 3. 💉 Iniezione delle dipendenze

#### a. **Tramite costruttore** (approccio classico)

```ts
@Component({...})
export class UserListComponent {
  constructor(private userService: UserService) {}
}
```

#### b. **Tramite `inject()`** (da Angular 14+)

Utile in componenti standalone o ambienti non-classici:

```ts
@Component({...})
export class StandaloneComponent {
  userService = inject(UserService);

  constructor() {
    console.log(this.userService.getUsers());
  }
}
```
.
---

## 🧰 Tipi di provider in Angular: `useClass`, `useValue`, `useExisting`, `useFactory`

I provider specificano *come* Angular deve creare o fornire una dipendenza. Comprenderli bene è fondamentale per sfruttare al massimo la flessibilità della DI.
---

### 🔹 `useClass`: fornisce un'istanza di una classe

#### ✔️ Cosa fa

Crea una nuova istanza di una classe **specifica** per un token.

#### 📌 Quando usarlo

* Quando vuoi fornire una **classe concreta** (es. `Logger → RealLogger`).
* Quando vuoi **sostituire** una classe (es. con una mock nei test).

#### 🧠 Esempio

```ts
class RealLogger {
  log(msg: string) { console.log(msg); }
}

class MockLogger {
  log(msg: string) { /* non fa nulla */ }
}

providers: [
  { provide: Logger, useClass: RealLogger } // oppure MockLogger
]
```
.
---

### 🔹 `useValue`: fornisce un valore statico

#### ✔️ Cosa fa

Fornisce direttamente un oggetto, valore o costante. Angular **non crea nulla**, usa il valore così com'è.

#### 📌 Quando usarlo

* Per **configurazioni statiche**, costanti o dati predefiniti.
* Quando il valore **non ha logica di costruzione**.

#### 🧠 Esempio

```ts
const CONFIG = { apiUrl: 'https://api.example.com' };

providers: [
  { provide: 'AppConfig', useValue: CONFIG }
]
```
.
---

### 🔹 `useExisting`: alias verso un'altra istanza già creata

#### ✔️ Cosa fa

Permette di usare **la stessa istanza** già registrata, ma sotto un altro token.

#### 📌 Quando usarlo

* Per creare **alias** tra token diversi.
* Per garantire **retrocompatibilità** o evitare duplicazioni.

#### 🧠 Esempio

```ts
class AuthService {
  login() { /* ... */ }
}

providers: [
  AuthService,
  { provide: 'Logger', useExisting: AuthService }
]
```

Ora sia `AuthService` che `'Logger'` condividono la **stessa istanza**.
.
---

### 🔹 `useFactory`: crea dinamicamente una dipendenza

#### ✔️ Cosa fa

Usa una funzione factory per **generare l’istanza** a runtime, anche in base a condizioni o dipendenze.

#### 📌 Quando usarlo

* Quando la dipendenza dipende da **parametri o ambiente**.
* Quando serve una logica condizionale per creare l’oggetto.
* Quando vuoi **ritardare** la creazione fino al momento dell’uso.

#### 🧠 Esempio semplice

```ts
function createLogger() {
  return environment.production ? new ProdLogger() : new DevLogger();
}

providers: [
  { provide: Logger, useFactory: createLogger }
]
```

#### 🧩 Esempio con dipendenze

```ts
function myFactory(config: AppConfig) {
  return new ApiService(config.apiUrl);
}

providers: [
  { provide: ApiService, useFactory: myFactory, deps: [AppConfig] }
]
```

Angular inietterà `AppConfig` nella factory.
.
---

### 📌 Riepilogo concettuale

| Provider      | Cosa fa                                                   | Quando usarlo                                                   |
| ------------- | --------------------------------------------------------- | --------------------------------------------------------------- |
| `useClass`    | Crea una nuova istanza da una classe                      | Per servizi standard o versioni alternative (mock, stub)        |
| `useValue`    | Fornisce un valore costante                               | Per configurazioni, costanti, dati statici                      |
| `useExisting` | Reindirizza a un provider esistente                       | Per alias, retrocompatibilità, evitare duplicazioni             |
| `useFactory`  | Usa una funzione per generare dinamicamente la dipendenza | Per logica condizionale, ambienti diversi, dipendenze dinamiche |
.
---

## 🧠 Decoratore `@Injectable()` in Angular

```ts
@Injectable({ providedIn: 'root' })
export class DataService {}
```

### 🔑 Scopi principali:

1. Indica che la classe **può ricevere dipendenze** nel costruttore.
2. Permette ad Angular di **registrare automaticamente** la classe nel sistema DI.

#### ❓ Quando serve `@Injectable()`?

✅ È necessario se:

* La classe ha **dipendenze nel costruttore**.
* Deve essere **iniettata da altri**.

❌ Non serve se:

* Non ha dipendenze e non viene iniettata (es. classi helper semplici).
* La classe è un componente, direttiva o pipe: questi decoratori già implicano la DI.
.
---

## 🌐 Altre opzioni di `providedIn`

```ts
@Injectable({ providedIn: 'root' })    // Istanza globale
@Injectable({ providedIn: 'any' })     // Nuova istanza per ogni modulo lazy
@Injectable({ providedIn: SomeModule }) // Limitata al modulo specificato
```
.
---

## 🧪 Testing con Dependency Injection

Nel testing, puoi sostituire una dipendenza reale con una mockata:

```ts
TestBed.configureTestingModule({
  providers: [
    { provide: RealService, useClass: MockService }
  ]
});
```
.
---

## 🧩 Componenti Standalone e DI (Angular 15+)

Con i componenti standalone è possibile dichiarare provider direttamente nel decoratore:

```ts
@Component({
  standalone: true,
  selector: 'app-root',
  template: `<h1>Hello</h1>`,
  providers: [
    { provide: 'Token', useClass: MyService }
  ]
})
export class AppComponent {
  service = inject('Token');
}
```
.
---

## ✅ Conclusione (risposta pronta per un colloquio)

> *"La dependency injection in Angular è un sistema integrato che permette di fornire automaticamente ai componenti e ai servizi le dipendenze di cui hanno bisogno. Angular offre vari provider (`useClass`, `useValue`, `useExisting`, `useFactory`) per gestire in modo flessibile come queste dipendenze vengono create o condivise. Questo rende l'applicazione modulare, testabile e facilmente estendibile."*
.
---
