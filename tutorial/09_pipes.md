# Pipes

> ng generate pipe customers/customer-status

## src/app/customers/customer-status.pipe.ts

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'customerStatus' })
export class CustomerStatusPipe implements PipeTransform {
  transform(value: number): string {
    if (!value) {
      return 'thumb_down';
    }

    return value > 50 ? 'star_rate' : 'thumb_up';
  }
}
```

## src/app/customers/customer/customer.component.html

```html
...

<!-- header -->

<span><mat-icon>{{customer?.numberOfOrders | customerStatus}}</mat-icon></span>
```
