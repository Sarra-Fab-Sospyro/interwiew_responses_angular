# Cosa gestisce il routing in Angular?

In Angular, il **routing** permette di gestire la **navigazione tra diverse viste** in un'applicazione **Single Page Application (SPA)**. Questo consente di caricare dinamicamente i componenti, **senza ricaricare l'intera pagina**, migliorando l'esperienza utente e le performance.

## Cosa sono le "viste" in un'applicazione SPA?

In un'applicazione SPA (Single Page Application), le "viste" rappresentano le diverse interfacce o schermate che l'utente visualizza durante la navigazione. A differenza delle applicazioni web tradizionali, dove ogni cambio di pagina comporta il caricamento completo di una nuova pagina HTML dal server, in una SPA:

- L'applicazione carica inizialmente un'unica pagina HTML
- Le diverse "viste" sono porzioni dell'interfaccia utente che vengono dinamicamente sostituite
- Ogni vista è tipicamente rappresentata da un componente Angular
- Il cambio di vista avviene modificando il DOM della pagina esistente, non caricando nuove pagine

Questo approccio crea un'esperienza utente più fluida e reattiva, simile a quella di un'applicazione desktop o mobile, poiché elimina i tempi di caricamento tra le pagine.

Il routing è gestito principalmente tramite il modulo `RouterModule` e un sistema di **definizione delle rotte**, in cui ogni rotta specifica quale componente mostrare a un determinato URL.

## ✅ Configurazione con moduli (standalone: false)

Nel caso classico (modulare), le rotte vengono definite in un file chiamato `app-routing.module.ts`. Qui si crea un array di oggetti `Route`, ognuno con almeno due proprietà:
* `path`: rappresenta la porzione dell'URL.
* `component`: specifica quale componente caricare.

```typescript
const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: '', redirectTo: '/home', pathMatch: 'full' } // Redirect per la rotta radice
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

Il modulo viene poi importato nel `AppModule`.

## ✅ Configurazione con componenti standalone (standalone: true)

A partire da Angular 14+, è possibile usare i **componenti standalone** (potenziati ulteriormente in Angular 15-19+), e configurare le rotte in un file come `app.routes.ts` (senza usare NgModule):

```typescript
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { 
    path: 'dashboard', 
    loadComponent: () => import('./dashboard/dashboard.component').then(c => c.DashboardComponent)
  }
];
```

In Angular 19, la configurazione dell'applicazione avviene in `app.config.ts`:

```typescript
import { ApplicationConfig } from '@angular/core';
import { provideRouter, withComponentInputBinding, withViewTransitions } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(
      routes,
      withComponentInputBinding(), // Abilita il binding automatico dei parametri di rotta
      withViewTransitions()        // Abilita le animazioni di transizione tra viste
    )
  ]
};
```

Questo approccio è più leggero e modulare, adatto a progetti moderni.

## 📍 Dove vengono caricati i componenti?

I componenti associati alle rotte vengono visualizzati nel template del componente principale (spesso `AppComponent`) tramite il tag `<router-outlet>`, che funziona da **contenitore dinamico** per il contenuto delle rotte.

```html
<!-- app.component.html -->
<header>
  <!-- Navigazione fissa -->
  <nav>
    <a routerLink="/home" routerLinkActive="active">Home</a>
    <a routerLink="/about" routerLinkActive="active">About</a>
  </nav>
</header>

<router-outlet></router-outlet>

<footer>
  <!-- Footer fisso -->
</footer>
```

## 🔄 Funzionalità aggiuntive del routing Angular

* **Lazy Loading**: Angular supporta il caricamento **differito** di moduli o componenti, utile per migliorare la performance iniziale dell'applicazione. Si configura con:
  * `loadChildren` per moduli: `{ path: 'admin', loadChildren: () => import('./admin/admin.module').then(m => m.AdminModule) }`
  * `loadComponent` per componenti standalone: `{ path: 'profile', loadComponent: () => import('./profile/profile.component').then(c => c.ProfileComponent) }`

* **Rotte figlie e nidificate**: Angular permette di definire rotte **annidate**, per creare viste gerarchiche o layout complessi:
  ```typescript
  {
    path: 'products',
    component: ProductsComponent,
    children: [
      { path: '', component: ProductListComponent },
      { path: ':id', component: ProductDetailComponent }
    ]
  }
  ```

* **Route Guards**: Proteggono l'accesso alle rotte in base a condizioni specifiche:
  * `CanActivate`: controlla se un utente può accedere a una rotta (es. verifica autenticazione)
  * `CanActivateChild`: controlla l'accesso alle rotte figlie
  * `CanDeactivate`: verifica se un utente può abbandonare una rotta (es. conferma modifiche non salvate)
  * `CanMatch`: determina se una rotta può essere abbinata durante la navigazione
  * `Resolve`: precarga dati prima di attivare una rotta

  **Approccio classico con classi** (pre-Angular 14):
  ```typescript
  @Injectable({
    providedIn: 'root'
  })
  export class AuthGuard implements CanActivate {
    constructor(private authService: AuthService, private router: Router) {}
    
    canActivate(
      route: ActivatedRouteSnapshot,
      state: RouterStateSnapshot
    ): boolean | UrlTree {
      if (this.authService.isLoggedIn()) {
        return true;
      }
      
      // Reindirizza alla pagina di login memorizzando l'URL richiesto
      return this.router.createUrlTree(['/login'], { 
        queryParams: { returnUrl: state.url } 
      });
    }
  }
  ```

  **Approccio moderno con funzioni** (Angular 14+ e raccomandato in Angular 19):
  
  1. **Approccio diretto** - tutta la logica nella funzione guardia:
  ```typescript
  export const authGuard: CanActivateFn = (
    route: ActivatedRouteSnapshot, 
    state: RouterStateSnapshot
  ) => {
    const authService = inject(AuthService);
    const router = inject(Router);
    
    if (authService.isLoggedIn()) {
      return true;
    }
    
    return router.createUrlTree(['/login'], {
      queryParams: { returnUrl: state.url }
    });
  };
  ```
  
  2. **Approccio ibrido raccomandato** - logica nel servizio, guardia come delegato:
  ```typescript
  // Servizio che contiene la logica di autorizzazione
  @Injectable({
    providedIn: 'root'
  })
  class AuthService {
    // Controlla se l'utente può accedere a una rotta
    canActivate(userId: string): boolean {
      // Logica di autorizzazione complessa
      return this.isLoggedIn() && this.hasAccess(userId);
    }
    
    private isLoggedIn(): boolean {
      // Implementazione...
      return true;
    }
    
    private hasAccess(userId: string): boolean {
      // Implementazione...
      return true;
    }
  }
  
  // Funzione guardia che delega al servizio
  export const authGuard: CanActivateFn = (
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ) => {
    const authService = inject(AuthService);
    const router = inject(Router);
    
    if (authService.canActivate(route.params['id'])) {
      return true;
    }
    
    return router.createUrlTree(['/login']);
  };
  ```
  
  3. Esempio di guardia per modifiche non salvate:
  ```typescript
  export const unsavedChangesGuard: CanDeactivateFn<HasUnsavedChanges> = (
    component: HasUnsavedChanges,
    currentRoute: ActivatedRouteSnapshot,
    currentState: RouterStateSnapshot,
    nextState: RouterStateSnapshot
  ) => {
    if (component.hasUnsavedChanges()) {
      return confirm('Hai modifiche non salvate. Vuoi davvero abbandonare questa pagina?');
    }
    return true;
  };
  ```

  Utilizzo delle guard basate su funzioni nelle rotte:
  ```typescript
  const routes: Routes = [
    { path: 'admin', component: AdminComponent, canActivate: [authGuard] },
    { 
      path: 'settings', 
      component: SettingsComponent, 
      canActivate: [authGuard],
      canDeactivate: [unsavedChangesGuard]
    }
  ];
  ```

* **Passaggio di parametri**: È possibile passare dati tra rotte con:
  * Parametri di percorso: `/products/:id`
  * Query params: `/products?category=electronics`
  * Fragment: `/products#section1`

* **Rotte di fallback**: È possibile gestire rotte non trovate (404) con:
  ```typescript
  { path: '**', component: PageNotFoundComponent }
  ```

* **Router Events**: Angular offre un sistema di eventi per monitorare le attività di navigazione:
  ```typescript
  router.events.subscribe(event => {
    if (event instanceof NavigationEnd) {
      // Codice da eseguire dopo la navigazione
    }
  });
  ```