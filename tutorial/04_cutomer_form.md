# Customer form

> ng generate class customers/customer

## src/app/customers/customer.ts

```ts
import { FormBuilder, Validators } from '@angular/forms';

export class Customer {
  id: number;
  name: string;

  firstname?: string;
  hobbies?: string[];
  numberOfOrders?: number;

  static toFormGroup(customer = new Customer()) {
    const formBuilder = new FormBuilder();

    return formBuilder.group({
      id: formBuilder.control(customer.id),
      name: formBuilder.control(customer.name, Validators.required),
      firstname: formBuilder.control(customer.firstname),
      numberOfOrders: formBuilder.control(
        customer.numberOfOrders || 0,
        Validators.min(0)
      )
    });
  }
}
```

> ng generate component customers/customer-form

## src/app/app.component.html

```html
<app-customer-form></app-customer-form>
```

## src/app/customers/customers.module.ts

```ts
@NgModule({
  declarations: [
    CustomerDetailsComponent,
    CustomerComponent,
    CustomerFormComponent
  ],
  exports: [CustomerComponent, CustomerFormComponent],
  imports: [CommonModule, MatButtonModule, MatIconModule]
})
```

## src/app/customers/customer-form/customer-form.component.ts

```ts
import { Component, OnInit } from '@angular/core';
import { MatSnackBar } from '@angular/material';
import { FormGroup } from '@angular/forms';
import { Customer } from '../customer';

@Component({
  selector: 'app-customer-form',
  templateUrl: './customer-form.component.html',
  styleUrls: ['./customer-form.component.scss']
})
export class CustomerFormComponent implements OnInit {
  form: FormGroup;

  constructor(private snackBar: MatSnackBar) {}

  ngOnInit() {
    this.form = Customer.toFormGroup();
  }

  submit() {
    const data = this.form.getRawValue();

    console.table(data);

    this.snackBar.open(`Customer ${data.name} saved successfully.`, '', {
      duration: 2000
    });
  }

  cancel() {}
}
```

## src/app/customers/customer-form/customer-form.component.html

```html
<h1>{{ form.get('id').value ? 'Edit' : 'Add' }} customer</h1>

<form novalidate [formGroup]="form" (ngSubmit)="submit()">
  <div class="form-row">
    <mat-form-field>
      <input type="text" matInput placeholder="Name" formControlName="name" />
      <mat-error *ngIf="form.get('name').hasError('required')">
        REQUIRED
      </mat-error>
    </mat-form-field>
  </div>

  <div class="form-row">
    <mat-form-field>
      <input
        type="text"
        matInput
        placeholder="Firstname"
        formControlName="firstname"
      />
    </mat-form-field>
  </div>

  <div class="form-row">
    <mat-form-field>
      <input
        type="number"
        matInput
        placeholder="Number of orders"
        formControlName="numberOfOrders"
      />
    </mat-form-field>
  </div>

  <div class="form-row">
    <button
      mat-raised-button
      color="primary"
      type="submit"
      [disabled]="this.form.invalid"
    >
      Save
    </button>
    <button mat-raised-button type="button" (click)="cancel()">Cancel</button>
  </div>
</form>

<pre>form.getRawValue() = <br/>{{ form.getRawValue() | json }}</pre>
```

## src/app/customers/customers.module.ts

```ts
@NgModule({
  declarations: [
    CustomerDetailsComponent,
    CustomerComponent,
    CustomerFormComponent
  ],
  exports: [CustomerComponent, CustomerFormComponent],
  imports: [
    CommonModule,
    BrowserAnimationsModule,
    ReactiveFormsModule,
    MatButtonModule,
    MatIconModule,
    MatSnackBarModule,
    MatInputModule,
    MatFormFieldModule
  ]
})
export class CustomersModule {}
```
