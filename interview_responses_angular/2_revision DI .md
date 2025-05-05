# 📦 Dependency Injection in Angular

## 🔍 Cos'è la Dependency Injection (DI)?

La **Dependency Injection** (DI) è un pattern di design fondamentale in Angular che consente di fornire dall'esterno le dipendenze necessarie a una classe, invece di costringerla a crearle internamente. Angular gestisce automaticamente la creazione e fornitura di queste dipendenze, rendendo il codice più **modulare**, **testabile** e **manutenibile**.

## 🎯 Vantaggi principali

- **Codice più pulito**: separazione delle responsabilità tra classi.
- **Miglior testabilità**: facilita il mock delle dipendenze nei test.
- **Maggiore riusabilità**: le classi non dipendono da implementazioni specifiche.
- **Migliore manutenibilità**: è più semplice sostituire o aggiornare dipendenze.

## ⚙️ Come funziona in Angular

### 1. **Definizione delle dipendenze**

Le classi da iniettare devono essere annotate con `@Injectable()`:

```ts
@Injectable({ providedIn: 'root' })
export class UserService {
  getUsers() {
    return [...];
  }
}
```

### 2. **Registrazione dei provider**

Angular può registrare i provider in diversi modi, specificando come creare le istanze.

### 3. **Iniezione delle dipendenze**

#### a. **Via costruttore** (classico)

```ts
@Component({...})
export class UserListComponent {
  constructor(private userService: UserService) {}
}
```

#### b. **Via `inject()`** (da Angular 14+, utile in standalone o ambienti non-classici)

```ts
@Component({...})
export class StandaloneComponent {
  userService = inject(UserService);

  constructor() {
    console.log(this.userService.getUsers());
  }
}
```

## 🔄 Livelli di registrazione delle dipendenze

| Livello              | Dettagli |
|----------------------|----------|
| **Applicazione**     | `providedIn: 'root'` → Istanza globale |
| **Modulo**           | In `@NgModule({ providers: [...] })` → Istanza per modulo |
| **Componente**       | In `@Component({ providers: [...] })` → Istanza unica per componente |

## 🧰 Tipi di provider: `useClass`, `useValue`, `useExisting`, `useFactory`

Angular offre diversi modi per controllare **come** viene creata una dipendenza.

### 🔹 `useClass`

```ts
class MyService {}

providers: [{ provide: 'Token', useClass: MyService }]
```

Iniezione:

```ts
constructor(@Inject('Token') service: MyService) {}
// oppure
const service = inject('Token');
```

### 🔹 `useValue`

```ts
const CONFIG = { apiUrl: 'https://api.example.com' };

providers: [{ provide: 'AppConfig', useValue: CONFIG }]
```

Iniezione:

```ts
constructor(@Inject('AppConfig') config: any) {}
// oppure
const config = inject('AppConfig');
```

### 🔹 `useExisting`

```ts
class AuthService {}

providers: [
  AuthService,
  { provide: 'Logger', useExisting: AuthService }
]
```

Iniezione:

```ts
constructor(@Inject('Logger') logger: AuthService) {}
// oppure
const logger = inject('Logger');
```

### 🔹 `useFactory`

```ts
function createLogger(isProd: boolean) {
  return isProd ? new ProdLogger() : new DevLogger();
}

providers: [
  { provide: LoggerService, useFactory: () => createLogger(environment.production) }
]
```

Iniezione:

```ts
constructor(logger: LoggerService) {}
// oppure
const logger = inject(LoggerService);
```

## 📌 Quando usare ogni provider?

| Provider      | Quando usarlo                                                      |
|---------------|--------------------------------------------------------------------|
| `useClass`    | Per fornire una classe standard o alternativa (mock, stub).        |
| `useValue`    | Per fornire valori statici (config, costanti, oggetti).            |
| `useExisting` | Per condividere un’istanza tra più token.                          |
| `useFactory`  | Per creare dinamicamente l'istanza in base a condizioni o ambienti.|

## 🧠 Decoratore `@Injectable()` in Angular

```ts
@Injectable({
  providedIn: 'root'
})
export class DataService {}
```

### 🔑 Ruoli di `@Injectable()`

1. Indica che la classe **può ricevere dipendenze** nel costruttore.
2. Permette ad Angular di **registrare** la classe nel sistema DI.

### ❓ Quando è necessario `@Injectable()`?

**È necessario quando:**
- La classe riceve **dipendenze nel costruttore**.
- La classe deve essere **fornita ad altri tramite DI**.

**NON è necessario quando:**
- La classe **non ha dipendenze** e non viene iniettata altrove.

### ⚠️ Attenzione: `@Injectable()` e componenti

`@Component`, `@Directive` e `@Pipe` **non necessitano** di `@Injectable()` perché il decoratore già lo implica.

```ts
@Component({...})
export class UserComponent {
  constructor(private service: UserService) {}
}
```

## 🌐 Opzioni di `providedIn` in `@Injectable()`

```ts
@Injectable({ providedIn: 'root' }) // istanza globale
@Injectable({ providedIn: 'any' })  // nuova istanza per modulo lazy
@Injectable({ providedIn: SomeModule }) // limitata a un modulo specifico
```

## 🧪 Testing con Dependency Injection

```ts
TestBed.configureTestingModule({
  providers: [
    { provide: RealService, useClass: MockService }
  ]
});
```

## 🧩 Componenti standalone e DI (Angular 15+)

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

## ✅ Conclusione (risposta breve da colloquio)

> *"La dependency injection in Angular consente di fornire automaticamente ai componenti e ai servizi le dipendenze di cui hanno bisogno. Possiamo controllare il modo in cui queste dipendenze vengono create o fornite tramite provider come `useClass`, `useValue`, `useExisting` e `useFactory`, in base al comportamento desiderato."*
