Cos’è Angular?
Angular è un framework frontend open-source sviluppato da Google, utilizzato per costruire applicazioni web scalabili e soprattutto SPA.
Le caratteristiche principali di Angular comprendono: un’architettura basata su componenti (ogni componente è riutilizzabile), è scritto in Typescript (superset di Javascript tipizzato per un maggior controllo e miglior debug), usa il routing per cambiare vista senza dover ricaricare l’applicazione (principio di SPA), usa la Dependency Injection per fornire le dipendenze a una classe tramite servizi (si usa il decoratore @Injectable per rendere una classe iniettabile), utilizza il data binding per sincronizzare i dati tra il template HTML e il file TS, gestione avanzata dei form tramite Reactive Form, Template Driven Form, e form Array.

Cos’è un Componente?
Un componente è la struttura base di Angular, è una direttiva speciale costituita da: una logica applicativa gestita in un file TS, una vista gestita in un template HTML, stile specifico (opzionale) gestito nel foglio CSS/SCSS.
Ogni componente è definito con un decoratore @Component che fornisce i metadati essenziali:
@Component({
	selector: ‘app-nome-componente’ ,  <- tag del componente da richiamare nel template
	templateUrl:  ‘./user.html’, <- percorso del template del componente
	styleUrls: [‘./user.css’ ] <- percprso del foglio di stile
})
Export class NomeComponente {
	Logica componente 
}
Questo approccio permette di riutilizzare i componenti in più elementi dell’applicazione, facilita la manutenibilità e il testing.

Cos’è una direttiva in Angular?
Le direttive sono classi che modificano gli elementi del DOM. Le direttive integrate di Angular gestiscono efficacemente moduli, elenchi, stili ed elementi dell'interfaccia utente.
Le direttive sono di tre tipi: i componenti sono direttive speciali con un proprio template associato, le direttive di attributo cambiano l’ aspetto o il comportamento di un elemento esistente (sono NgClass, ngStyle, NgModel), le direttive strutturali modificano la struttura del DOM e i relativi elementi(*ngIf, *ngFor, *ngSwitch).

Cosa si usa al posto delle direttive strutturali?
Si usa il Control Flow, un nuovo metodo più performante e di più facile lettura. Nel template si scrive @if( condizione ){ <template> } .

Cos’è una Pipe?
La pipe è un operatore speciale che permette la trasformazione dichiarativa (non altera il valore del dato ma solo come viene visualizzato) dei dati nel template. Definendo una funzione di trasformazione una volta sola, le pipe consentono il suo riutilizzo in diversi template. Le pipe di Angular utilizzano il carattere barra verticale ( | ).

Data Binding in Angular.
È il sistema con il quale avviene il passaggio di dati tra il template HTML (interfaccia utente) e il file TS (business logic), si possono passare dati anche tra componenti legati dalla relazione padre-figlio.
Angular consente di usare tre tipi di one-way data binding e il two-way data binding, caratteristica del framework. 
I one-way includono quanto segue: 
La string interpolation, mostra nel template il dato che arriva dal file TS. Sintassi: {{ dato }} , conosciuta come mustache syntax.
Poi c'è il property binding, in questo caso, si collega il dato dal TS direttamente alla proprietà di un elemento HTML nel template. Sintassi: [property]="value".
Infine, troviamo l'event binding. Questo tipo di binding serve per trasferire il dato dal template al file TS tramite una logica fatta scattare da un evento, come un click o la pressione di un tasto sulla tastiera. Sintassi: (event)="method()".
Il two-way, caratteristica di Angular, è gestito dalla direttiva ngModel che trasmette il dato in entrambe le direzioni, necessita di importare il modulo FormsModule. È utilizzato principalmente nei template driven form. Sintassi: [(ngModel)] =”value”, la sintassi combina i due metodi di one-way data binding (property e event).
Un altro tipo di binding è il component property binding, che permette di trasmettere dati tra componenti padre-figlio. Nel componente figlio si usa il decoratore @Input() per ricevere i dati dal componente padre tramite property binding ( <app-figlio [messaggio] =” testo”> ) scritto nel template del componente padre. Per emettere i dati dal figlio al padre si usa il decoratore @Output() associato ad un EventEmitter, e nel template del padre tramite event binding ( <app-figlio (inviaDati)=”riceviDati($event)”> ).

Dependency Injection 
La Dependency Injection è un design pattern che permette a una classe di utilizzare dipendenze esterne invece di crearle al proprio interno. Questo permette di avere un codice più pulito e mantenibile, di separare le responsabilità tra le classi e semplificare il debug e i test. 
Le classi che vanno iniettate usando il decoratore @Injectable(), all’interno si troverà il livello a cui è disponibile la classe attraverso il provideIn: , che potrà essere root (livello globale), modulo o componente.
Le dipendenze possono essere iniettate nella classe del componente all’interno del costruttore o tramite inject().
