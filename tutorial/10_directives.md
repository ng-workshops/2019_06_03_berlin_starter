# Directives

> ng generate module shared --module app

> ng generate directive shared/directives/can-click --export

## src/app/shared/directives/can-click.directive.ts

```ts
import {
  Directive,
  ElementRef,
  EventEmitter,
  HostBinding,
  HostListener,
  OnInit,
  Output,
  Renderer2
} from '@angular/core';

@Directive({
  selector: '[appCanClick]'
})
export class CanClickDirective implements OnInit {
  @HostBinding('class.app-disabled') isDisabled = true;

  @Output() canClick = new EventEmitter();

  constructor(private element: ElementRef, private renderer: Renderer2) {}

  @HostListener('click', ['$event']) onClick(e: MouseEvent) {
    if (this.isDisabled) {
      e.preventDefault();
      e.stopPropagation();
      return;
    }

    this.canClick.emit(e);
  }

  ngOnInit() {
    this.isDisabled = true;
    this.renderer.setProperty(
      this.element.nativeElement,
      'title',
      'Im Demo-Modus nicht verfügbar'
    );
  }
}
```

## src/app/customers/customers.module.ts

```ts
@NgModule({
  imports: [
    ...
    SharedModule,
    ...
  ],
})
export class CustomersModule {}
```

## src/app/customers/customer/customer.component.html

```html
...

<div class="footer">
  ...
  <button appCanClick mat-icon-button (canClick)="delete(customer?.id)">
    <mat-icon>delete</mat-icon>
  </button>
</div>
```
