# 1 Component interaction - Parent to Child

> ng generate component home/info-box

## src/app/home/info-box/info-box.component.html

```html
<mat-card>
  <mat-card-content>
    <div>
      <p>@Input() Message: {{ message }}</p>
    </div>
  </mat-card-content>
</mat-card>
```

## src/app/home/info-box/info-box.component.ts

```ts
import { Component, OnInit, Input } from '@angular/core';

@Component({
  selector: 'app-info-box',
  templateUrl: './info-box.component.html',
  styleUrls: ['./info-box.component.scss']
})
export class InfoBoxComponent implements OnInit {
  @Input()
  message: string;

  constructor() {}

  ngOnInit() {}
}
```

## src/app/home/home.component.html

```html
<p>
  <button (click)="changeChild()">Change Child data</button>
</p>

<app-info-box [message]="message"></app-info-box>
```

## src/app/home/home.component.ts

```ts
export class HomeComponent {
  message = 'INIT';

  changeChild() {
    this.message = new Date().toISOString();
  }
}
```

## src/app/home/home.module.ts

```ts
imports: [CommonModule, FormsModule, MatCardModule];
```
