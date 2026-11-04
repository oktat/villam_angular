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

### Backend beszerzése

Használjunk saját backendet:

* [https://github.com/oktatapi/shopping_list_laravel.git](https://github.com/oktatapi/shopping_list_laravel.git)
* [https://github.com/oktatapi/shopping_list_sipere.git](https://github.com/oktatapi/shopping_list_sipere.git)

### A backend beüzemelése

Töltsük le a backendet. Két fenti link közül válasszuk
ki az egyiket. Az egyik Laravel-ben lett írva, a másik Sipere keretrendszerrel készült.

```bash
git clone https://github.com/oktatapi/shopping_list_laravel.git
cd shopping_list_laravel
```

Letöltés után, nézzük meg a dokumentációt. Kövessük a
telepítései útmutatót, majd indítsuk el a szervert.

* doc/quick_installation.md

```bash
composer install
copy .env.example .env
php artisan key:generate
php artisan migrate
php artisan serve
```

### A backend ellenőrzése

Használjuk a Resen csomag res parancsát az ellenőrzéshez.

Telepítsük a Resen csomagot:

```bash
npm install --global resen
```

Ellenőrizzük magát a parancs meglétét:

```bash
res --version
```

A backend ellenőrzése:

```bash
res localhost:8000/api/items
```

### Projekt készítése

Készítsünk egy **client** nevű projektet.

```bash
ng new client
```

### A HttpClient regisztrálása

A Backend elérésére az Angular HttpClient osztályát
fogjuk használni.


Ahogy azt már láttuk vegyük fel a provideHttpClient()
függvényt az app.config.ts fájlban.


Az app.config.ts kezdeti tartalmai ehhez hasonló:

_src/app/app.config.ts_:

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

### Szolgáltatás készítése

```bash
ng generate service services/item-service
```

_src/app/services/item-service.ts_:

```typescript
import { HttpClient } from '@angular/common/http';
import { inject, Service } from '@angular/core';

@Service()
export class ItemService {
    http = inject(HttpClient)
    baseUrl = 'http://localhost:8000/api'

    getItems() {
        return this.http.get(`${this.baseUrl}/items`)
    }
}
```

### Komponens készítése

Készítsünk két komponenst:

```bash
ng generate component home-component
ng generate component shopping-list-component
```

### Útválasztás és navigáció készítése

_src/app/app.config.ts_:

```typescript
import { Routes } from '@angular/router';
import { ShoppingListComponent } from './shopping-list-component/shopping-list-component';
import { HomeComponent } from './home-component/home-component';

export const routes: Routes = [
    { path: '', redirectTo: 'shopping-list', pathMatch: 'full' },
    { path: 'shopping-list', component: ShoppingListComponent },
    { path: 'home', component: HomeComponent },
];
```

_src/app/app.html_:

```typescript
<nav>
  <a routerLink="home">Főoldal</a>
  <a routerLink="shopping-list">Bevásárló lista</a>
</nav>
<router-outlet></router-outlet>
```

### Adatok lekérése

_src/app/shopping-list-component/shopping-list-component.ts_:

```typescript
import { Component, inject, signal } from '@angular/core';
import { ItemService } from '../services/item-service';

@Component({
  imports: [],
  selector: 'app-shopping-list-component',
  styleUrl: './shopping-list-component.css',
  templateUrl: './shopping-list-component.html',
})
export class ShoppingListComponent {
  
  itemService = inject(ItemService)

  items = signal<any[]>([])

  ngOnInit() {
    this.getItems()
  }

  getItems() {
    return this.itemService.getItems().subscribe({
      next: (result: any) => {
        console.log(result)
        this.items.set(result.data)
      }
    })
  }
}
```

_src/app/shopping-list-component/shopping-list-component.html_:

```html
<h2>Bevásárló lista</h2>

<ul>
    @for(item of items(); track item.id) {
        <li>{{item.name}}</li>
    }
</ul>
```