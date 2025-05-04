
# HttpClient in Angular: Guida Completa

## Cos'è `HttpClient` in Angular?
`HttpClient` è il **servizio ufficiale di Angular per comunicare con server esterni** (come API RESTful). Fa parte del pacchetto `@angular/common/http` e consente di utilizzare tutti i verbi HTTP:

* **GET** → per leggere dati
* **POST** → per creare risorse
* **PUT** / **PATCH** → per aggiornare
* **DELETE** → per eliminare

Angular preferisce `HttpClient` rispetto a `fetch` nativo perché:
- Restituisce **Observable** (RxJS)
- Supporta tipizzazione forte tramite generics
- Si integra perfettamente con l'ecosistema Angular (Zone.js, gestione errori, interceptor, Dependency Injection)
- Offre funzionalità aggiuntive come il monitoraggio del progresso e la cancellazione delle richieste

## Configurazione di `HttpClient`

### 1. In app tradizionali con NgModules (`standalone: false`):

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [
    BrowserModule,
    HttpClientModule  // Importa il modulo qui
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 2. In app moderne con componenti standalone (`standalone: true`):

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient, withInterceptorsFromDi } from '@angular/common/http';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptorsFromDi()),  // Con supporto per interceptor
    provideRouter(routes)
  ]
};
```

```typescript
// main.ts
import { bootstrapApplication } from '@angular/platform-browser';
import { AppComponent } from './app/app.component';
import { appConfig } from './app/app.config';

bootstrapApplication(AppComponent, appConfig)
  .catch(err => console.error(err));
```

## Utilizzo di `HttpClient` in un componente

```typescript
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HttpClient } from '@angular/common/http';
import { catchError } from 'rxjs/operators';
import { Observable, of } from 'rxjs';

interface User {
  id: number;
  name: string;
  email: string;
}

@Component({
  selector: 'app-users',
  standalone: true,
  imports: [CommonModule],
  template: `
    <div *ngIf="users$ | async as users; else loading">
      <div *ngFor="let user of users">{{ user.name }} ({{ user.email }})</div>
    </div>
    <ng-template #loading>Caricamento utenti...</ng-template>
  `
})
export class UsersComponent implements OnInit {
  users$: Observable<User[]> = of([]);
  
  constructor(private http: HttpClient) {}
  // Oppure con inject API (Angular 14+):
  // private http = inject(HttpClient);

  ngOnInit(): void {
    this.users$ = this.http.get<User[]>('https://api.example.com/users')
      .pipe(
        catchError(error => {
          console.error('Errore nel recupero degli utenti:', error);
          return of([]);  // Fallback con array vuoto
        })
      );
  }
}
```

## Funzionalità avanzate

### Headers personalizzati
```typescript
import { HttpHeaders } from '@angular/common/http';

const headers = new HttpHeaders({
  'Content-Type': 'application/json',
  'Authorization': 'Bearer your-token-here'
});

this.http.get<User[]>('https://api.example.com/users', { headers });
```

### Parametri di query
```typescript
import { HttpParams } from '@angular/common/http';

const params = new HttpParams()
  .set('page', '1')
  .set('limit', '10');

this.http.get<User[]>('https://api.example.com/users', { params });
```

### Gestione degli interceptor
Aggiungi un interceptor per gestire token, logging o manipolazione delle richieste:

```typescript
// auth.interceptor.ts
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const token = localStorage.getItem('token');
    
    if (token) {
      const authReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${token}`)
      });
      return next.handle(authReq);
    }
    
    return next.handle(req);
  }
}
```

```typescript
// Registrazione in app.config.ts (standalone)
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { authInterceptor } from './auth.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor])),
    // ...altri provider
  ]
};
```

## Note per il colloquio
- `HttpClient` è fortemente tipizzato e supporta i generici TypeScript
- Restituisce sempre Observable, quindi richiede subscribe() o async pipe
- È possibile annullare le richieste con `AbortController` o `takeUntil`
- Le richieste sono "fredde" (non si avviano finché non fai subscribe)
- Supporta il monitoraggio del progresso per upload/download

### Differenze tra versioni di Angular
- In Angular 14+ puoi usare la funzione `inject()` invece del costruttore
- Da Angular 15+ i componenti standalone sono la configurazione consigliata
- In Angular 16+ è stato aggiunto `provideHttpClient()` per le app standalone
- In Angular 17+ sono stati introdotti miglioramenti alle performance HTTP

