# Szolgáltatások

## Szolgáltatás és függőségbefecskendezés

Üzeleti logika és adatlekérést nem teszünk komponensbe. Ezeket különálló szolgáltatásokba
szervezzük. A komponens csak a képernyő kirajzolásáért feleljen. Ezt írja le az
egyfelelősség elve, angolul Single Responsibility Principle.

Vegyünk egy bevásárlólistát. A termékek listáját a szolgáltatásban tároljuk.

Szolgáltatás létrehozása:

```bash
ng generate service shared/shopping
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

## Backend
