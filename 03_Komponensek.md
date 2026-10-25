# Komponensek

## Tartalomjegyzék

* [Tartalomjegyzék](#tartalomjegyzék)
* [Komponensek készítése](#komponensek-készítése)
* [Útválasztás](#útválasztás)
* [Navigáció](#navigáció)
* [Dinamikus útvonalak](#dinamikus-útvonalak)

## Komponensek készítése

```bash
ng generate component home-component
```

Rövidítve:

```bash
ng g c home-component
```

## Útválasztás

Az útválasztáshoz komponensek kellenek, amiket betöltünk, hivatkozás esetén.

Vegyünk fel komponenseket, dolgozói nyilvántartáshoz.

* home
* products
* product-detail
* login

```bash
ng g c home-component
ng g c products-component
ng g c product-detail-component
ng g c login-component
```

_src/app/app.routes.ts_:

```typescript
const routes: Routes = [
    { path: 'products', component: ProductsComponent },
    { path: 'home', component: HomeComponent },
    { path: 'login', component: LoginComponent }
]
```

_src/app/app.routes.ts_:

```typescript
const routes: Routes = [
    { path: 'products', component: ProductsComponent },
    { path: 'home', component: HomeComponent }
]
```

## Navigáció

```html
<nav class="nav">
    <a routerLink="home" class="nav-link">Főoldal</a>
    <a routerLink="products" class="nav-link">Termékek</a>
</nav>
```

## Dinamikus útvonalak

```bash
ng g c home-component
ng g c products-component
ng g c product-detail-component
ng g c login-component
```

```typescript
const routes: Routes = [
    { path: 'home', component: HomeComponent },
    { path: 'products', component: ProductsComponent },
    { path: 'product/:id', component: ProductDetailComponent },
    { path: 'login', component: LoginComponent },
]
```
