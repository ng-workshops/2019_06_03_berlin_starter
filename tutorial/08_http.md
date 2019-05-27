# Http

## Add HttpClientModule to imports

## src/app/customers/customer.module.ts

```ts
...

imports: [
    CommonModule,
    BrowserAnimationsModule,
    HttpClientModule,
    CustomersRoutingModule,
    ReactiveFormsModule,
    MatButtonModule,
    MatIconModule,
    MatSnackBarModule,
    MatInputModule,
    MatFormFieldModule
  ]

  ...
```

## Add HttpClient

## src/app/customers/customer.service.ts

```ts
import { Injectable } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { environment } from '../../environments/environment';
import { Customer } from './customer';

@Injectable({
  providedIn: 'root'
})
export class CustomerService {
  private readonly endpoint = environment.endpoints.customers;

  constructor(private httpClient: HttpClient) {}

  getById(id: string) {
    return this.httpClient.get<Customer>(`${this.endpoint}/${id}`);
  }

  getAll(searchTerm = '') {
    // add search param
    const httpOptions = searchTerm
      ? { params: new HttpParams().set('search', searchTerm) }
      : {};

    return this.httpClient.get<Customer[]>(this.endpoint, httpOptions);
  }

  create(customer: Customer) {
    return this.httpClient.post<Customer>(this.endpoint, customer);
  }

  update(customer: Customer) {
    return this.httpClient.put<Customer>(this.endpoint, customer);
  }

  delete(id: number) {
    return this.httpClient.delete<Customer>(`${this.endpoint}/${id}`);
  }
}
```

# Update submit logic to use the customer service

## src/app/customers/cutomer-form/customer-form.component.ts

```ts
...

submit() {
    const data = this.form.getRawValue();
    const save$ = data.id
    ? this.customerService.update.bind(this.customerService)
    : this.customerService.create.bind(this.customerService);

    save$(data)
      .subscribe(() => {
        this.snackBar.open(
		      `Customer ${data.name} saved successfully.`,
          '',
          { duration: 2000 }
        );

        this.cancel();
      });
}
```

## Add search field

## src/app/customers/customer-list/customer-list.component.html

```html
<div class="customers-search">
  <mat-form-field>
    <input matInput [formControl]="searchTerm" placeholder="Search..." />
  </mat-form-field>

  <button mat-icon-button (click)="addNewCustomer()">
    <mat-icon>add</mat-icon>
  </button>
</div>

<div class="customer">
  <app-customer
    *ngFor="let customer of (customers$ | async)"
    [customer]="customer"
    (deleteCustomer)="deleteCustomer($event)"
  >
    <app-customer-details></app-customer-details>
  </app-customer>
</div>
```

## Add rxjs magic

## src/app/customers/customer-list/customer-list.component.ts

```ts
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { FormControl } from '@angular/forms';
import { Observable, Subject, merge } from 'rxjs';
import {
  debounceTime,
  distinctUntilChanged,
  switchMap,
  startWith,
  withLatestFrom,
  map
} from 'rxjs/operators';
import { Customer } from '../customer';
import { CustomerService } from '../customer.service';

@Component({
  selector: 'app-customer-list',
  templateUrl: './customer-list.component.html',
  styleUrls: ['./customer-list.component.scss']
})
export class CustomerListComponent implements OnInit {
  customers$: Observable<Customer[]>;
  searchTerm = new FormControl();

  private search$: Observable<string>;
  private reload$ = new Subject();

  constructor(
    private router: Router,
    private customerService: CustomerService
  ) {}

  ngOnInit() {
    this.search$ = this.searchTerm.valueChanges.pipe(
      debounceTime(400),
      distinctUntilChanged(),
      startWith('')
    );

    this.customers$ = merge(this.search$, this.reload$).pipe(
      withLatestFrom(this.search$),
      map(value => value[1]),
      switchMap(value => {
        return this.customerService.getAll(value);
      })
    );
  }

  addNewCustomer() {
    this.router.navigateByUrl('/customers/new');
  }

  deleteCustomer(id: number) {
    this.customerService.delete(id).subscribe(() => this.reload$.next());
  }
}
```

## Add delete method and Output

## src/app/customers/customer/customer.component.ts

```ts
...

@Output() deleteCustomer = new EventEmitter<number>();

delete(id: number) {
  this.deleteCustomer.emit(id);
}
```

## Add delete button in footer

## src/app/customers/customer/customer.component.html

```html
...

<!-- footer -->

<button mat-icon-button (click)="delete(customer?.id)">
  <mat-icon>delete</mat-icon>
</button>
```
