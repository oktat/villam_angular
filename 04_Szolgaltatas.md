# Szolgáltatások

## Szolgáltatás és függőségbefecskendezés

Üzeleti logika és adatlekérést nem teszünk komponensbe. Ezeket különálló szolgáltatásokba
szervezzük. A komponens csak a képernyő kirajzolásáért feleljen. Ezt írja le az
egyfelelősség elve, angolul Single Responsibility Principle.

Vegyünk egy bevásárlólistát. A termékek listáját a szolgáltatásban tároljuk.

Készítsünk egy _shopping_list_ nevű projektet.

```bash
ng new shopping_list
```

Szolgáltatás létrehozása:

```bash
ng generate service services/shopping-service
```

A TypeScript fájl:

```typescript
private items = signal<string[]>(['Tej', 'Kenyér'])

getItems() {
    return this.items.asReadonly()
}

//Üzleti logika: elem hozzáadása
addItem(item: string) {
    if (item.trim()) {
        this.items.update(list => [...list, item])
    }
}
```

Komponens létrehozása:

```bash
ng generate component shopping-list-component
```

Injektálás a komponensbe:

```typescript
import { Component, inject } from '@angular/core';
//...

private shoppingService = inject(SoppingService)

items = this.shoppingService.getItems()

add(newItem: string) {
    this.shoppingService.addItem(newItem)
}
```

Sablon:

```html
<h2>Bevásárlólist</h2>

<input #itemInput type="text" placeholder="Új tétel... />

<button (click)="add(itemInput.value); itemInput.value = ''">
    Hozzáadás
</button>

<ul>
    @for (item of items(); track $index) {
        <li>{{ item }}</li>
    }
</ul>
```

## API hívások

Az API szerver felülete, ami hozzáférést biztosít az adatbázishoz. A frontend az API
hávásokon keresztül tudja kezelni az adatokat.

Az adatokat aszinkron módon kezeljük. Elküldjük a kérést. Nem számítunk azonnal
a válaszra, majd valamikor megjön. Olyan mint egy folyam, amin valamikor érkeznek
az adatok. A folyam figyelését a Observable típus biztosítja az RxJS modulból.

Egy helyettesítő szervert fogunk használni Backendnek:

* [https://jsonplaceholder.typicode.com/](https://jsonplaceholder.typicode.com/)

A JavaScript-ben elérhető a fetch() függvény az adatok elérésére. Az Angular
fejlesztői azonban saját megoldást hoztak létre: HttpClient.

Először konfigurálni kell az Angular projektet, hogy használhassuk.

Nyissuk meg a src/app/app.config.ts fájlt. A tartalma ehhez hasonló:

src/app/app.config.ts

```typescript
import { ApplicationConfig, provideBrowserGlobalErrorListeners } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideBrowserGlobalErrorListeners(),
    provideRouter(routes)
  ]
};
```

Vegyük fel a provideHttpClient() a providers tömbbe:

```typescript
//...
//Legyen importálva:
import { provideHttpClient } from '@angular/common/http';
//...
  providers: [
    provideBrowserGlobalErrorListeners(),
    provideRouter(routes),
    provideHttpClient()
  ]
```

A HttpClient használatához egy szolgáltatást hozunk létre.
Legyen a neve PostService. Hozzuk létre:

```bash
ng generate service services/post-service
```

## Backend

Használjunk saját backendet:

* [https://github.com/oktatapi/shopping_list_api.git](https://github.com/oktatapi/shopping_list_api.git)
* [https://github.com/oktatapi/shopping_list_sipere.git](https://github.com/oktatapi/shopping_list_sipere.git)
