# 5 Component interaction - Local template variable

## src/app/home/home.component.html

```html
<p>
  <button (click)="changeChild()">Change Child data</button>
  <button (click)="child.name = 'Changed BY PARENT'">
    Change Child via Template Var
  </button>
</p>

<app-info-box
  #child
  [message]="message"
  [name]="name"
  (replyToParent)="processReply($event)"
></app-info-box>

<pre>Message from Child = {{ reply | json }}</pre>
```
