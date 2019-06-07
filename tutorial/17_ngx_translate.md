# Internationalization - ngxTranslate

> ng generate component home/impressum2

## src/app/app-routing.module.ts

```ts
...

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'impressum', component: ImpressumComponent },
  { path: 'impressum2', component: ImpressumComponent },
  { path: '**', component: HomeComponent }
];

...
```

## src/app/app.component.html

```html
<nav>
  <a routerLink="home" routerLinkActive="active">Home</a>
  <a routerLink="customers" routerLinkActive="active">Customers</a>
  <a routerLink="customers/new" routerLinkActive="active">Add customer</a>
  <a routerLink="impressum" routerLinkActive="active">Impressum</a>
  <a routerLink="impressum2" routerLinkActive="active">Impressum2</a>
</nav>

<router-outlet></router-outlet>
```

## src/app/app.module.ts

```ts
// import ngx-translate and the http loader
import { TranslateLoader, TranslateModule, TranslateCompiler } from '@ngx-translate/core';
import { TranslateHttpLoader } from '@ngx-translate/http-loader';
import { TranslateMessageFormatCompiler } from 'ngx-translate-messageformat-compiler';

@NgModule({
  imports: [
    ...
    TranslateModule.forRoot({
      loader: {
        provide: TranslateLoader,
        useFactory: HttpLoaderFactory,
        deps: [HttpClient]
      },
      // compiler configuration
      compiler: {
        provide: TranslateCompiler,
        useClass: TranslateMessageFormatCompiler
      }
    })
  ],
  ...
})
export class AppModule {}

// required for AOT compilation
export function HttpLoaderFactory(http: HttpClient) {
  return new TranslateHttpLoader(http);
}
```

## src/app/home/home.module.ts

```ts
@NgModule({
  ...
  imports: [CommonModule, FormsModule, MatCardModule, TranslateModule]
})
export class HomeModule {}
```

## src/app/home/impressum2/impressum2.component.html

```html
<button (click)="useLanguage('en')">In english</button>
<button (click)="useLanguage('de')">Auf Deutsch</button>

<h1>{{ 'impressum.title' | translate }}</h1>

<h3>{{ 'impressum.greeting' | translate: user }}</h3>

<p translate>impressum.content</p>

<div [innerHTML]="'impressum.info' | translate"></div>

<div>This is the title loaded from Code --> {{ fromCode$ | async }}</div>

<footer>
  <a [translate]="'impressum.sitemap'"></a> |
  <a translate>impressum.career</a> |
  <a translate>impressum.home</a>

  <br />
  <br />
  <br />

  <div>
    <span translate>general.lastupdatedAt</span
    ><span>{{ lastUpdatedAt | date: 'short':undefined:locale }}</span>
    <p translate [translateParams]="{ days: days }">
      That is {days, plural, =0 {just now} =1 {yesterday} other {{{days
      | number:'1.0-0'}} days ago}}
    </p>
    <ng-container>{{ 'general.copyright' | translate }}</ng-container>
  </div>

  <ul>
    <li translate [translateParams]="{ gender: 'female', name: 'Sarah' }">
      impressum.developers
    </li>
    <li translate [translateParams]="{ gender: 'male', name: 'Peter' }">
      impressum.developers
    </li>
    <li>
      {{ 'impressum.developers' | translate: "{ gender: 'male', name: 'Peter' }"
      }}
    </li>
    <li>
      {{ 'impressum.developers' | translate: "{ name: 'Sarah + Peter' }" }}
    </li>
  </ul>
</footer>
```

## src/app/home/impressum2/impressum2.component.ts

```ts
import { Component, OnInit } from '@angular/core';
import { TranslateService } from '@ngx-translate/core';
import { switchMap, tap } from 'rxjs/operators';

@Component({
  selector: 'app-impressum2',
  templateUrl: './impressum2.component.html',
  styleUrls: ['./impressum2.component.scss']
})
export class Impressum2Component implements OnInit {
  private today = new Date();
  locale: string;

  lastUpdatedAt = new Date(2019, 5, 3, 10, 10, 10);
  days =
    (this.today.getTime() - this.lastUpdatedAt.getTime()) /
    (1000 * 60 * 60 * 24);
  user = {
    name: 'Homer Simpsons'
  };

  fromCode$ = this.translate.onLangChange.pipe(
    tap(event => (this.locale = event.lang)),
    switchMap(() => this.translate.get('impressum.title'))
  );

  constructor(private translate: TranslateService) {}

  ngOnInit() {
    this.locale = this.translate.currentLang;
  }

  useLanguage(language: string) {
    this.translate.use(language);
  }
}
```

## src/app/app.component.ts

```ts
...

constructor(
    hostElementService: HostElementService,
    hostElement: ViewContainerRef,
    translate: TranslateService
  ) {
    hostElementService.setHost(hostElement);
    translate.setDefaultLang('en');
    translate.use('de')
  }

...
```
