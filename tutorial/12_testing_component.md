# Testing - components

## src/app/customers/customer-list/customer-list.component.spec.ts

```ts
import { NO_ERRORS_SCHEMA } from '@angular/core';
import { async, ComponentFixture, TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { of, Subscription } from 'rxjs';
import { CustomerService } from '../customer.service';
import { CustomerListComponent } from './customer-list.component';

describe('CustomerListComponent', () => {
  let component: CustomerListComponent;
  let fixture: ComponentFixture<CustomerListComponent>;
  let routerMock: any;
  let customerServiceMock: any;

  beforeEach(async(() => {
    routerMock = {
      navigateByUrl: jest.fn()
    };

    customerServiceMock = {
      getAll: jest.fn().mockReturnValue(of([])),
      delete: jest.fn().mockReturnValue(of({}))
    };

    TestBed.configureTestingModule({
      declarations: [CustomerListComponent],
      providers: [
        { provide: Router, useValue: routerMock },
        { provide: CustomerService, useValue: customerServiceMock }
      ],
      schemas: [NO_ERRORS_SCHEMA]
    }).compileComponents();
  }));

  beforeEach(() => {
    fixture = TestBed.createComponent(CustomerListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should navigate to the customer form when the add button is clicked', () => {
    component.addNewCustomer();

    expect(routerMock.navigateByUrl).toHaveBeenCalledWith('/customers/new');
  });

  it('should call the delete function and reload the data when the delete button is clicked', () => {
    component.deleteCustomer(1);

    expect(customerServiceMock.delete).toHaveBeenCalledWith(1);
    expect(customerServiceMock.getAll).toHaveBeenCalledWith('');
  });
});

describe('CustomerListComponent without TestBed', () => {
  let component: CustomerListComponent;
  let routerMock: any;
  let customerServiceMock: any;

  beforeEach(() => {
    routerMock = {
      navigateByUrl: jest.fn()
    };

    customerServiceMock = {
      getAll: jest.fn().mockReturnValue(of([])),
      delete: jest.fn().mockReturnValue(of({}))
    };

    component = new CustomerListComponent(routerMock, customerServiceMock);
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  describe('GIVEN the component is initialized', () => {
    let customerSubscription: Subscription;

    beforeEach(() => {
      component.ngOnInit();
      customerSubscription = component.customers$.subscribe();
    });

    afterEach(() => {
      customerSubscription.unsubscribe();
    });

    it('should navigate to the customer form when the add button is clicked', () => {
      component.addNewCustomer();

      expect(routerMock.navigateByUrl).toHaveBeenCalledWith('/customers/new');
    });

    it('should call the delete function and reload the data when the delete button is clicked', () => {
      component.deleteCustomer(1);

      expect(customerServiceMock.delete).toHaveBeenCalledWith(1);
      expect(customerServiceMock.getAll).toHaveBeenCalledWith('');
    });
  });
});
```
