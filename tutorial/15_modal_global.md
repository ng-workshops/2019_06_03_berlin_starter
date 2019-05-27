# 15 Dynamic Global Modal

> ng generate service shared/modal/host/host-element --module shared

## shared/modal/host/host-element.service.ts

```ts
import { Injectable, ViewContainerRef } from '@angular/core';
import { ReplaySubject } from 'rxjs';

Injectable({
  providedIn: 'root'
});
export class HostElementService {
  private _hostElement = new ReplaySubject<ViewContainerRef>();
  hostElement$ = this._hostElement.asObservable();

  setHost(hostElement: ViewContainerRef): void {
    this._hostElement.next(hostElement);
  }
}
```

## app.component.ts

```ts
import {
  Component,
  HostBinding,
  OnInit,
  ViewContainerRef
} from '@angular/core';
import { HostElementService } from './shared/modal/host/host-element.service';

export class AppComponent implements OnInit {
  constructor(
    hostElementService: HostElementService,
    hostElement: ViewContainerRef
  ) {
    hostElementService.setHost(hostElement);
  }
}
```

## shared/modal/modal.service.ts

```ts
import {
  ComponentFactoryResolver,
  Injectable,
  ViewContainerRef
} from '@angular/core';

import { ModalComponent } from './modal.component';
import { ModalData } from './modal.model';
import { HostElementService } from './host/host-element.service';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

@Injectable({ providedIn: 'root' })
export class ModalService {
  constructor(
    private componentFactoryResolver: ComponentFactoryResolver,
    private hostElementService: HostElementService
  ) {}

  open(data: ModalData, host: ViewContainerRef): ModalComponent {
    data.type = data.type || 'primary';
    return this.createModal(data, host);
  }

  openGlobal(data: ModalData): Observable<ModalComponent> {
    return this.hostElementService.hostElement$.pipe(
      map(host => this.createModal(data, host))
    );
  }

  private createModal(data: ModalData, host: ViewContainerRef): ModalComponent {
    host.clear();

    const modalFactory = this.componentFactoryResolver.resolveComponentFactory(
      ModalComponent
    );
    const modal = host.createComponent(modalFactory);

    modal.instance.modal = data;
    modal.instance.close.subscribe(() => modal.destroy());
    modal.instance.cancel.subscribe(() => modal.destroy());

    return modal.instance;
  }
}
```

## shared/module.ts

Add HostElementService to providers
providers: [HostElementService],

## home/home.component.html

```html
<button (click)="openModalGlobal()">Open modal globally</button>
```

## home/home.component.ts

```ts
openModalGlobal() {
    this.modal
      .openGlobal({
        title: 'Global Error',
        message: 'Please contact the support',
        type: 'warn'
      })
      .subscribe(modal => {
        modal.close.subscribe(() => console.log('Global MODAL closed'));
      });
  }
```
