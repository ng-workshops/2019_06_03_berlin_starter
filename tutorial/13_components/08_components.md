# 8 Component - 2 way binding

> ng generate component home/info-item

## src/app/home/info-item/info-item.component.html

```html
<p>
  <input type="text" [(ngModel)]="message" />
</p>
```

## src/app/home/info-item/info-item.component.ts

```ts
import { Component, OnInit, EventEmitter, Output, Input } from '@angular/core';

@Component({
  selector: 'app-info-item',
  templateUrl: './info-item.component.html',
  styleUrls: ['./info-item.component.scss']
})
export class InfoItemComponent implements OnInit {
  private _messageValue: string;

  @Output()
  messageChange = new EventEmitter<string>();

  @Input()
  get message() {
    return this._messageValue;
  }

  set message(val) {
    this._messageValue = val;
    this.messageChange.emit(this._messageValue);
  }

  constructor() {}

  ngOnInit() {}
}
```

## src/app/home/home.component.html

```html
<p>
  <app-info-item [(message)]="name"></app-info-item>
</p>
```
