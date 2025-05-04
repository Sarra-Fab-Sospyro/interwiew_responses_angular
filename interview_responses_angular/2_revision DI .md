# 📦 Dependency Injection in Angular

## 🔍 Cos'è la Dependency Injection (DI)?

La **Dependency Injection** (DI) è un pattern di design fondamentale in Angular che permette di fornire dall'esterno le dipendenze necessarie a una classe, invece di costringerla a crearle internamente. Angular si occupa di creare e fornire automaticamente queste dipendenze, rendendo il codice più modulare e testabile.

---

## 🎯 Vantaggi principali

- **Codice più pulito**: separazione delle responsabilità tra classi.
- **Miglior testabilità**: facilita il mock delle dipendenze nei test.
- **Maggiore riusabilità**: le classi non dipendono da implementazioni specifiche.
- **Migliore manutenibilità**: è più semplice sostituire o aggiornare dipendenze.

---

## ⚙️ Come funziona in Angular

1. **Definizione delle dipendenze**  
   Si creano classi o servizi annotati con `@Injectable()`.

2. **Registrazione dei provider**  
   Si specifica ad Angular come creare o fornire le istanze.

3. **Iniezione nel costruttore**  
   Le dipendenze vengono dichiarate nei parametri del costruttore.

```ts
// 1. Definizione di un servizio
@Injectable({
  providedIn: 'root'  // disponibile globalmente
})
export class UserService {
  getUsers() {
    return [...];
  }
}

// 2. Utilizzo in un componente
@Component({...})
export class UserListComponent {
  users: User[] = [];

  constructor(private userService: UserService) {
    this.users = this.userService.getUsers();
  }
}
```

---

## 🔄 Livelli di registrazione delle dipendenze

| Livello              | Dettagli |
|----------------------|----------|
| **Applicazione**     | `providedIn: 'root'` → Istanza condivisa a livello globale |
| **Modulo**           | In `@NgModule({ providers: [...] })` → Istanza condivisa all'interno del modulo |
| **Componente**       | In `@Component({ providers: [...] })` → Istanza unica per quel componente |

---

## 🧰 Tipi di provider: useClass, useValue, useExisting, useFactory

Angular offre diversi modi per controllare come viene creata un'istanza:

### 🔹 `useClass`

Utilizza una determinata classe per creare la dipendenza.

```ts
class MyService {}
providers: [{ provide: 'Token', useClass: MyService }]
```

> ✅ Crea un'istanza di `MyService` quando viene richiesto `'Token'`.

---

### 🔹 `useValue`

Fornisce un valore statico (oggetto, numero, stringa, ecc.).

```ts
const CONFIG = { apiUrl: 'https://api.example.com' };
providers: [{ provide: 'AppConfig', useValue: CONFIG }]
```

> ✅ Utile per configurazioni e costanti.

---

### 🔹 `useExisting`

Alias di un provider esistente, utile per evitare duplicazioni.

```ts
class AuthService {}
providers: [
  AuthService,
  { provide: 'Logger', useExisting: AuthService }
]
```

> ✅ `'Logger'` e `AuthService` condividono la stessa istanza.

---

### 🔹 `useFactory`

Utilizza una funzione per creare dinamicamente un'istanza.

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

> ✅ Utile per ambienti o configurazioni dinamiche.

---

## 📌 Quando usare ogni provider?

| Provider      | Quando usarlo                                                      |
|---------------|--------------------------------------------------------------------|
| `useClass`    | Per fornire una classe standard o alternativa (mock, stub).        |
| `useValue`    | Per fornire valori statici (config, costanti, oggetti).            |
| `useExisting` | Per condividere un’istanza tra più token.                          |
| `useFactory`  | Per creare dinamicamente l'istanza in base a condizioni o ambienti.|

---

## 🧠 Decoratore `@Injectable()` in Angular

Il decoratore `@Injectable()` è fondamentale nel sistema DI:

```ts
@Injectable({
  providedIn: 'root'
})
export class DataService {
  // ...
}
```

### 🔑 Ruoli di `@Injectable()`

1. Indica che la classe **può ricevere dipendenze** nel costruttore.
2. Registra la classe nel sistema DI di Angular con `providedIn`.

---

### ❓ Quando è necessario `@Injectable()`?

**È necessario** quando:
- La classe **riceve dipendenze** nel costruttore.
- Si vuole **fornire la classe come dipendenza** ad altri.

```ts
@Injectable()
export class ProductService {
  constructor(private http: HttpClient) {}
}
```

**NON è necessario** quando:
- La classe **non ha dipendenze** né viene iniettata altrove.

---

### ⚠️ Attenzione: `@Injectable()` e componenti

Componenti (`@Component`), direttive (`@Directive`) e pipe (`@Pipe`) **non richiedono** `@Injectable()` perché i loro decoratori già lo implicano.

```ts
// ✅ CORRETTO
@Component({
  selector: 'app-user',
  template: '...'
})
export class UserComponent {
  constructor(private userService: UserService) {}
}

// ❌ SBAGLIATO - Ridondante
@Injectable()
@Component({ ... })
export class UserComponent { ... }
```

---

### 🌐 Opzioni di `providedIn`

```ts
// Istanza globale (default)
@Injectable({
  providedIn: 'root'
})

// Nuova istanza per ogni modulo lazy-loaded
@Injectable({
  providedIn: 'any'
})

// Istanza solo nel modulo specificato
@Injectable({
  providedIn: UserModule
})
```

---

## 🧪 Testing con Dependency Injection

La DI permette facilmente di sostituire implementazioni reali con mock:

```ts
TestBed.configureTestingModule({
  providers: [
    { provide: RealService, useClass: MockService }
  ]
});
```

---

## ✅ Conclusione (risposta breve da colloquio)

> *"La dependency injection in Angular permette di fornire automaticamente ai componenti e ai servizi le dipendenze di cui hanno bisogno. Possiamo controllare il modo in cui queste dipendenze vengono create o fornite tramite provider come `useClass`, `useValue`, `useExisting` e `useFactory`, in base al comportamento desiderato."*