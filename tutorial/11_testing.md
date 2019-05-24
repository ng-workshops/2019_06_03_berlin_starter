# Testing - Fix existing tests

## src/app/app.component.ts

```ts
...

 it(`should have showDetails set to false`, async(() => {
    const fixture = TestBed.createComponent(AppComponent);
    const app: AppComponent = fixture.debugElement.componentInstance;
    expect(app.showDetails).toBeFalsy();
  }));

...
```

## src/app/customers/customer-form/customer-form.component.spec.ts

```ts
import { HttpClientModule } from '@angular/common/http';
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { ReactiveFormsModule } from '@angular/forms';
import {
  MatInputModule,
  MatSnackBar,
  MatSnackBarModule
} from '@angular/material';
import { NoopAnimationsModule } from '@angular/platform-browser/animations';
import { ActivatedRoute, Router, RouterModule } from '@angular/router';
import { Subject } from 'rxjs';
import { CustomerService } from '../customer.service';
import { CustomerFormComponent } from './customer-form.component';

describe('CustomerFormComponent', () => {
  let component: CustomerFormComponent;
  let fixture: ComponentFixture<CustomerFormComponent>;
  let routeMock: any;
  const paramMapTestSubject = new Subject();

  beforeEach(async(() => {
    routeMock = {
      paramMap: paramMapTestSubject.asObservable()
    };

    TestBed.configureTestingModule({
      declarations: [CustomerFormComponent],
      imports: [
        MatInputModule,
        MatSnackBarModule,
        ReactiveFormsModule,
        NoopAnimationsModule,
        RouterModule,
        HttpClientModule
      ],
      providers: [
        { provide: ActivatedRoute, useValue: routeMock },
        { provide: Router, useValue: {} },
        { provide: CustomerService, useValue: {} },
        { provide: MatSnackBar, useValue: {} }
      ]
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(CustomerFormComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

## src/app/customers/customer-list/customer-list.component.spec.ts

```ts
...

let customerServiceMock: any;

customerServiceMock = {
  getAll: () => of([])
};

beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [CustomerListComponent],
    providers: [
      { provide: Router, useValue: {} },
      { provide: CustomerService, useValue: customerServiceMock }
    ],
    schemas: [NO_ERRORS_SCHEMA]
  }).compileComponents();
}));

...
```

## src/app/home/home.component.spec.ts

```ts
...

beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [HomeComponent],
    schemas: [NO_ERRORS_SCHEMA]
  }).compileComponents();
}));

...
```

## src/app/customers/customer.service.spec.ts

```ts
...

beforeEach(() =>
    TestBed.configureTestingModule({
      providers: [{ provide: HttpClient, useValue: {} }]
    })
  );

...
```

## src/app/customers/customer/customer.component.spec.ts

```ts
...

beforeEach(async(() => {
    TestBed.configureTestingModule({
      declarations: [CustomerComponent, CustomerStatusPipe],
      providers: [{ provide: Router, useValue: {} }],
      schemas: [NO_ERRORS_SCHEMA]
    }).compileComponents();
  }));

...
```
