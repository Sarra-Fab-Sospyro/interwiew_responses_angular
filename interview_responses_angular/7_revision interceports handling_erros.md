# Interceptor e Gestione Errori in Angular

## Cos'è un Interceptor in Angular?

Un **Interceptor** è una funzionalità potente offerta da Angular per **intercettare e modificare** tutte le **richieste HTTP in uscita** e/o le **risposte in entrata**. È fondamentalmente un **middleware** che si posiziona tra l'applicazione e il server, permettendo di manipolare il flusso di dati HTTP.

Gli interceptor implementano l'interfaccia `HttpInterceptor` che richiede un solo metodo:

```typescript
intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>>
```

## A cosa servono gli Interceptor?

Gli interceptor sono strumenti versatili che vengono utilizzati per:

* **Aggiungere headers** comuni a tutte le richieste (es. token JWT per autenticazione)
* **Gestire centralmente gli errori HTTP** (es. 401, 404, 500...)
* **Loggare le richieste e risposte** per debugging
* **Mostrare automaticamente loader/spinner** durante le chiamate
* **Modificare o trasformare risposte**
* **Implementare caching**
* **Gestire sessioni e refresh token**

## Implementazione degli Interceptor

### 1. Interceptor come Classe (Approccio tradizionale)

```typescript
import { Injectable } from '@angular/core';
import {
  HttpInterceptor,
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpErrorResponse
} from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Passa la richiesta al prossimo handler nella catena
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        let errorMessage = 'Si è verificato un errore sconosciuto!';
        
        if (error.error instanceof ErrorEvent) {
          // Errore lato client (rete, parsing JSON, ecc.)
          errorMessage = `Errore client: ${error.error.message}`;
        } else {
          // Errore lato server (status HTTP)
          switch (error.status) {
            case 401:
              errorMessage = 'Non sei autorizzato. Effettua nuovamente il login.';
              // Qui potresti reindirizzare alla pagina di login
              break;
            case 403:
              errorMessage = 'Accesso negato. Non hai i permessi necessari.';
              break;
            case 404:
              errorMessage = 'Risorsa non trovata!';
              break;
            case 500:
              errorMessage = 'Errore interno del server!';
              break;
            default:
              errorMessage = `Errore: ${error.status} - ${error.message}`;
              break;
          }
        }
        
        // Puoi loggare l'errore o mostrare un messaggio user-friendly
        console.error(errorMessage);
        
        // Notifica servizi di stato globale dell'app
        // this.notificationService.showError(errorMessage);
        
        // Ritorna l'errore come observable per gestirlo ulteriormente nei componenti
        return throwError(() => new Error(errorMessage));
      })
    );
  }
}
```

### 2. Interceptor come Funzione (Angular 15+)

Dal rilascio di Angular 15, è possibile creare interceptor funzionali, più leggeri e particolarmente adatti per applicazioni standalone:

```typescript
import { HttpEvent, HttpHandler, HttpRequest, HttpErrorResponse } from '@angular/common/http';
import { Observable, throwError } from 'rxjs';
import { catchError } from 'rxjs/operators';

export function errorInterceptor(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
  return next.handle(req).pipe(
    catchError((error: HttpErrorResponse) => {
      let errorMessage = 'Si è verificato un errore sconosciuto!';
      
      if (error.error instanceof ErrorEvent) {
        errorMessage = `Errore client: ${error.error.message}`;
      } else if (error.status === 401) {
        errorMessage = 'Non sei autorizzato. Effettua il login.';
      } else if (error.status === 404) {
        errorMessage = 'Risorsa non trovata!';
      } else if (error.status === 500) {
        errorMessage = 'Errore interno del server!';
      }
      
      console.error(errorMessage);
      return throwError(() => new Error(errorMessage));
    })
  );
}
```

## Registrazione degli Interceptor

### In applicazioni con NgModules (AppModule)

```typescript
import { NgModule } from '@angular/core';
import { HTTP_INTERCEPTORS, HttpClientModule } from '@angular/common/http';
import { ErrorInterceptor } from './error.interceptor';

@NgModule({
  imports: [
    HttpClientModule
  ],
  providers: [
    { 
      provide: HTTP_INTERCEPTORS, 
      useClass: ErrorInterceptor, 
      multi: true // Fondamentale per consentire più interceptor
    }
  ]
})
export class AppModule {}
```

### In applicazioni Standalone (Angular 15+)

Nel file `app.config.ts`:

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { errorInterceptor } from './error.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([errorInterceptor])
    )
  ]
};
```

## Gestione Avanzata degli Errori HTTP

### 1. Gestione completa con notifiche all'utente

```typescript
@Injectable()
export class ErrorHandlingInterceptor implements HttpInterceptor {
  constructor(private notifier: NotificationService, private router: Router) {}
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        // Gestione specializzata per ogni tipo di errore
        if (error.status === 0) {
          this.notifier.error('Impossibile raggiungere il server. Controlla la connessione.');
        } else if (error.status === 401) {
          this.notifier.warning('Sessione scaduta, accedi nuovamente.');
          this.router.navigate(['/login']);
        } else if (error.status === 403) {
          this.notifier.error('Non hai i permessi per accedere a questa risorsa.');
        } else if (error.status >= 500) {
          this.notifier.error('Si è verificato un problema con il server. Riprova più tardi.');
        } else {
          this.notifier.error(`Errore: ${error.message}`);
        }
        
        // Registrazione dell'errore
        this.logError(req, error);
        
        return throwError(() => error);
      })
    );
  }
  
  private logError(request: HttpRequest<any>, error: HttpErrorResponse): void {
    // Logica per registrare l'errore (analytics, logging service, ecc.)
    console.group('Errore HTTP');
    console.error('URL:', request.url);
    console.error('Metodo:', request.method);
    console.error('Status:', error.status);
    console.error('Messaggio:', error.message);
    console.groupEnd();
  }
}
```

### 2. Retry automatico delle richieste fallite

```typescript
import { retry, catchError } from 'rxjs/operators';

@Injectable()
export class RetryInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Non ritentare per richieste POST o richieste speciali
    if (req.method === 'POST' || req.headers.has('No-Retry')) {
      return next.handle(req);
    }
    
    // Ritenta fino a 3 volte per le richieste GET
    return next.handle(req).pipe(
      retry(3),
      catchError(error => {
        console.error('La richiesta è fallita dopo 3 tentativi');
        return throwError(() => error);
      })
    );
  }
}
```

### 3. Retry con backoff esponenziale

```typescript
import { Observable, throwError, timer } from 'rxjs';
import { mergeMap, retryWhen, scan } from 'rxjs/operators';

@Injectable()
export class ExponentialBackoffInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      retryWhen(errors => 
        errors.pipe(
          // Contatore per tracciare il numero di tentativi
          scan((count, error) => {
            // Smetti di riprovare dopo 3 tentativi
            if (count >= 3) {
              throw error;
            }
            console.log(`Tentativo #${count + 1} fallito, riprovo...`);
            return count + 1;
          }, 0),
          // Ritarda ogni nuovo tentativo: 1s, poi 2s, poi 4s
          mergeMap(count => timer(Math.pow(2, count) * 1000))
        )
      ),
      catchError(error => {
        console.error('Tutti i tentativi di connessione falliti');
        return throwError(() => error);
      })
    );
  }
}
```

## Catena di Interceptor

Gli interceptor vengono eseguiti in ordine di registrazione:

```typescript
// app.config.ts per applicazioni standalone
export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      withInterceptors([
        loggingInterceptor,  // Eseguito per primo sulla richiesta
        authInterceptor,     // Eseguito per secondo sulla richiesta
        errorInterceptor     // Eseguito per ultimo sulla richiesta
                            // MA per primo sulla risposta/errore
      ])
    )
  ]
};
```

È importante notare che:
1. L'ordine di esecuzione per la richiesta è quello di registrazione
2. L'ordine per la risposta è invertito (l'ultimo interceptor della catena è il primo a elaborare la risposta)

## Domande comuni durante i colloqui tecnici

1. **Qual è la differenza tra un interceptor e un service?**
   - Un interceptor intercetta automaticamente tutte le richieste HTTP, mentre un service deve essere iniettato ed esplicitamente chiamato.

2. **Come gestiresti una sessione scaduta (token JWT scaduto) con un interceptor?**
   - Intercettando errori 401, tentando un refresh del token e riprovando la richiesta originale.

3. **Come puoi testare un HTTP interceptor?**
   - Utilizzando `HttpClientTestingModule` per simulare le richieste e verificare che l'interceptor modifichi correttamente le richieste/risposte.

4. **Perché è necessario clonare la richiesta HTTP prima di modificarla?**
   - Le richieste HTTP in Angular sono immutabili per garantire l'integrità; è necessario clonare l'oggetto per apportare modifiche.

5. **Come implementeresti un sistema di caching HTTP con gli interceptor?**
   - Intercettando le richieste GET, salvando le risposte in una Map e restituendole direttamente per richieste identiche successive.

6. **Quali sono i vantaggi degli interceptor funzionali rispetto a quelli basati su classe?**
   - Sono più leggeri, tree-shakable, e integrati nativamente con l'approccio standalone di Angular moderno.

7. **Come gestiresti le differenze tra errori di rete e errori del server?**
   - `error.error instanceof ErrorEvent` indica errori client-side, mentre `error instanceof HttpErrorResponse` con status code indica errori server.

8. **Come implementeresti un meccanismo di rate limiting lato client utilizzando gli interceptor?**
   - Creando una coda di richieste e utilizzando operatori RxJS per limitare il numero di chiamate simultanee.

## Esempi pratici di interceptor

### 1. Interceptor di autenticazione

```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Non aggiungere token alle chiamate di autenticazione o risorse pubbliche
    if (req.url.includes('/auth/') || req.url.includes('/public/')) {
      return next.handle(req);
    }
    
    const token = this.authService.getToken();
    if (!token) {
      return next.handle(req);
    }
    
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${token}`)
    });
    
    return next.handle(authReq);
  }
}
```

### 2. Interceptor per aggiungere headers di lingua e versione

```typescript
export function apiHeadersInterceptor(req: HttpRequest<unknown>, next: HttpHandler) {
  // Aggiungi solo alle chiamate al tuo API
  if (req.url.includes('/api/')) {
    const modifiedReq = req.clone({
      setHeaders: {
        'Accept-Language': localStorage.getItem('lang') || 'it-IT',
        'X-App-Version': '1.2.3',
        'Content-Type': 'application/json'
      }
    });
    return next.handle(modifiedReq);
  }
  
  return next.handle(req);
}
```

### 3. Interceptor per loader globale

```typescript
@Injectable()
export class LoadingInterceptor implements HttpInterceptor {
  private activeRequests = 0;
  
  constructor(private loadingService: LoadingService) {}
  
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    // Ignora le richieste che non devono mostrare il loader
    if (req.headers.has('x-no-loader')) {
      return next.handle(req);
    }
    
    this.activeRequests++;
    this.loadingService.show();
    
    return next.handle(req).pipe(
      finalize(() => {
        this.activeRequests--;
        if (this.activeRequests === 0) {
          this.loadingService.hide();
        }
      })
    );
  }
}
```

## Best Practices per l'uso degli Interceptor

1. **Fai una cosa sola** - Segui il principio di responsabilità singola
2. **Gestisci gli errori** - Assicurati che l'interceptor non generi eccezioni non gestite
3. **Limita le modifiche** - Modifica solo ciò che è necessario per mantenere la leggibilità
4. **Usa l'operatore finalize** - Per garantire che le risorse vengano rilasciate 
5. **Filtra le richieste** - Non processare tutte le richieste se non necessario
6. **Attenzione all'ordine** - La sequenza degli interceptor è fondamentale
7. **Usa header speciali** per controllare il comportamento degli interceptor
8. **Preserva i parametri** originali della richiesta quando li modifichi
9. **Non bloccare l'UI** durante elaborazioni lunghe negli interceptor

## Conclusione

Gli interceptor sono uno strumento potente in Angular per centralizzare e standardizzare la gestione delle richieste HTTP. Combinati con operatori RxJS, forniscono un modo elegante per implementare funzionalità trasversali come autenticazione, gestione errori, logging e caching, mantenendo il codice DRY e coeso.