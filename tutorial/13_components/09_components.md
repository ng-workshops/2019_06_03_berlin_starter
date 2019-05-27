# 9 Component - Projection

## src/app/home/info-box/info-box.component.html

```html
<mat-card>
  <mat-card-header
    ><mat-card-title>Info Box</mat-card-title>
    <mat-card-subtitle
      ><ng-content select="[header]"></ng-content></mat-card-subtitle
  ></mat-card-header>
  <mat-card-content>
    <div>
      <p>@Input() Message: {{ message }}</p>
      <p>@Input() Name: {{ name }}</p>

      <p>
        <input type="text" #text />
        <button (click)="reply(text.value)">Message to parent</button>
      </p>
    </div>
  </mat-card-content>
  <mat-card-actions>
    <ng-content select="footer"></ng-content>
  </mat-card-actions>
</mat-card>
```

## src/app/home/home.component.html

```html
<p>
  <button (click)="changeChild()">Change Child data</button>
  <button (click)="child.name = 'Changed BY PARENT'">
    Change Child via Template Var
  </button>
  <button (click)="processReplyFromCode()">Change Child via ViewChild</button>
  <button (click)="sendMessage()">Send message via service</button>
</p>

<app-info-box
  #child
  [message]="message"
  [name]="name"
  (replyToParent)="processReply($event)"
>
  <footer>I am the footer</footer>
  <div header>I am the header</div>
</app-info-box>

<pre>Message from Child = {{ reply | json }}</pre>
```

## src/app/customers/customer/customer.component.html

```html
...

<div class="details" *ngIf="showDetails">
  <ng-content></ng-content>
</div>

...
```

## src/app/customers/customer-list/customer-list.component.html

```html
...

<div class="customer">
  <app-customer
    *ngFor="let customer of customers$ | async"
    [customer]="customer"
    (deleteCustomer)="deleteCustomer($event)"
  >
    <app-customer-details></app-customer-details>
  </app-customer>
</div>

...
```
