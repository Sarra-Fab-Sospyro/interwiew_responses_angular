# Come si gestiscono i form in Angular?

In Angular i form possono essere gestiti in due modi principali:

- **Template-driven Forms**
- **Reactive Forms**

Entrambi servono per raccogliere e validare input dell'utente, ma seguono approcci diversi.

---

## 🟠 Template-driven Forms

- Richiedono l'importazione del modulo `FormsModule`.
- La logica del form è scritta **principalmente nel template HTML**.
- Si utilizza la sintassi **`[(ngModel)]`** per il two-way data binding.
- Le **validazioni** (es. `required`, `minlength`, `pattern`, ecc.) sono dichiarate direttamente sugli elementi HTML.
- Angular crea automaticamente un oggetto `NgForm` che rappresenta lo stato del form.
- Sono ideali per **form semplici** e applicazioni di **piccola scala**.

### 📌 Esempio:

```html
<input [(ngModel)]="user.name" name="name" required />
```

---

## 🟢 Reactive Forms

- Richiedono l'importazione del modulo `ReactiveFormsModule`.
- La logica del form è scritta **nel componente TypeScript**, in modo più strutturato.
- Si utilizzano classi come `FormControl`, `FormGroup` e `FormArray`.
- Le validazioni (es. `Validators.required`, `Validators.minLength`) sono definite **nel codice**, non nel template.
- Consentono una **gestione dinamica** dei campi.
- Offrono **maggiore controllo**, sono più **testabili** e adatti a form **complessi**.

### 📌 Esempio:

```ts
form = new FormGroup({
  name: new FormControl('', Validators.required)
});
```

```html
<input [formControl]="form.get('name')" />
```

---

## 🧠 In sintesi

| Caratteristica  | Template-driven         | Reactive Forms                    |
|-----------------|-------------------------|-----------------------------------|
| Logica          | Nel template            | In TypeScript (componente)       |
| Validazioni     | HTML                    | Codice                           |
| Binding         | `[(ngModel)]`           | `[formControl]`, `formGroup`     |
| Dinamicità      | Limitata                | Elevata (es. con `FormArray`)    |
| Testabilità     | Più difficile           | Più facile                       |
| Uso consigliato | Form semplici           | Form complessi e dinamici        |

---

# 🧩 Elementi base dei Reactive Forms

## 🔹 `FormControl`

Rappresenta un **singolo campo di input** (es. `<input>`, `<select>`, ecc.).

### ✅ Cosa fa:

- Tiene traccia del **valore** e dello **stato di validazione**.
- Si collega direttamente agli elementi del template.

### 📌 Esempio:

```ts
nameControl = new FormControl('', Validators.required);
```

```html
<input [formControl]="nameControl" placeholder="Nome" />
```

---

## 🔹 `FormGroup`

Rappresenta un **gruppo di `FormControl`** (o altri `FormGroup`), utile per sezioni del form.

### ✅ Cosa fa:

- Organizza i controlli per chiave (come un oggetto).
- Supporta **validazioni a livello di gruppo**.

### 📌 Esempio:

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

---

## 🔹 `FormArray`

Una **lista dinamica di controlli**, adatta per campi ripetuti (es. hobby, email, figli...).

### ✅ Cosa fa:

- Può contenere `FormControl`, `FormGroup` o altri `FormArray`.
- Ideale per gestire elementi **dinamici**.

### 📌 Esempio:

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

---

## 🧠 Riepilogo veloce

| Classe        | Descrizione                                   | Esempio                                 |
|---------------|-----------------------------------------------|------------------------------------------|
| `FormControl` | Singolo campo (es. nome, email)               | `new FormControl('', Validators.required)` |
| `FormGroup`   | Gruppo di controlli organizzati per chiave    | `new FormGroup({ name: ..., email: ... })` |
| `FormArray`   | Lista dinamica di controlli dello stesso tipo | `new FormArray([ new FormControl('') ])`   |

---

# Cosa sono i Validator in Angular?

I **validatori** sono **funzioni** che Angular usa per verificare se un campo di input è **valido** secondo determinate regole (es. obbligatorio, lunghezza minima, pattern...).

### ✅ Validator predefiniti (built-in):

- `required` – campo obbligatorio
- `minLength(n)` / `maxLength(n)` – numero minimo/massimo di caratteri
- `pattern('regex')` – valore deve rispettare una RegEx
- `email` – verifica formato email

---

## 🧰 Come si usano?

### 🔸 Template-driven Forms

Si usano direttamente come **attributi HTML**:

```html
<input name="email" [(ngModel)]="email" required email />
```

Angular aggiorna lo stato (`valid`, `invalid`, ecc.) automaticamente.

---

### 🔹 Reactive Forms

Si definiscono nel **TypeScript**, usando `Validators`:

```ts
form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email])
});
```

Oppure con un solo validator:

```ts
new FormControl('', Validators.required)
```

---

## 🛠️ Come creare un Validator Custom

Un **custom validator** è una funzione che restituisce un oggetto errore se il campo è **non valido**, oppure `null` se è valido.

### 🔹 Per Reactive Forms

```ts
import { AbstractControl, ValidationErrors } from '@angular/forms';

export function noNumberValidator(control: AbstractControl): ValidationErrors | null {
  const hasNumber = /\d/.test(control.value);
  return hasNumber ? { noNumber: true } : null;
}
```

**Uso:**

```ts
name = new FormControl('', [noNumberValidator]);
```

---

### 🔸 Per Template-driven Forms

Richiede una **direttiva custom** con `@Directive`:

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

**Uso nel template:**

```html
<input name="name" ngModel noNumber />
```

---

## ✅ In sintesi:

- I **validatori** controllano la validità degli input.
- Possono essere usati in entrambi i tipi di form.
- Si possono **combinare**, **riutilizzare** e anche **creare custom**.
- Sono fondamentali per **migliorare l'esperienza utente** e prevenire errori.

---

# Come mostrare messaggi di errore nei form Angular

## 🟠 Template-driven Forms

1. **Aggiungi una variabile di riferimento locale** al controllo:

```html
<input name="email" [(ngModel)]="email" required email #emailRef="ngModel" />
```

2. **Mostra il messaggio solo quando il campo è toccato e invalido**:

```html
<div *ngIf="emailRef.invalid && emailRef.touched">
  <small class="text-danger" *ngIf="emailRef.errors?.required">L'email è obbligatoria.</small>
  <small class="text-danger" *ngIf="emailRef.errors?.email">Formato email non valido.</small>
</div>
```

---

## 🟢 Reactive Forms

1. **Definisci il form e le validazioni nel componente**:

```ts
this.form = new FormGroup({
  email: new FormControl('', [Validators.required, Validators.email])
});
```

2. **Nel template HTML** mostra il messaggio in base allo stato:

```html
<input type="email" formControlName="email" />
<div *ngIf="form.get('email')?.invalid && form.get('email')?.touched">
  <small class="text-danger" *ngIf="form.get('email')?.errors?.required">L'email è obbligatoria.</small>
  <small class="text-danger" *ngIf="form.get('email')?.errors?.email">Formato email non valido.</small>
</div>
```
