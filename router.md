### Routing in Angular :-

- @angular/router -- Build it router Module in Angular.

##### Steps for implementing routes in Angular :-

1. Define Routes :-

```
const routes: Routes = [
  { path: '', redirectTo: '/home', pathMatch: 'full' },
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: '**', component: NotFoundComponent },
  // Add more routes as needed
];
```

2. Declare routes in RouterModule :-

```
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
exports class AppRoutingModule
```

3. Add AppRoutingModule in the imports of root module.

4. Set up Router Outlet:

```
{ path: 'main', component: MainComponent, outlet: 'sidebar' }

<router-outlet></router-outlet> <!-- Primary outlet -->
// In case for multiple outlets
<router-outlet name="sidebar"></router-outlet>
```

5. Link to Routes :-

```
<a routerLink="" routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">Home</a>
<a routerLink="/home">Home</a> -- Absolute path / added before route
<a routerLink="about">About</a> -- Relative path will be added to the end of current path
<a routerLink="./users">About</a> -- ./ or ../ etc are relative path
```

##### Navigating Programatically :-

- Using navigate -- takes url in array containing all segements
     - ['books','authors'] for '/books/authors'
     - By default absolute path
- takes navigateByUrl -- takes url as an string

```
import { Router } from '@angular/router';

constructor(private router: Router) { }

this.router.navigate(['path']); // Absolute navigation

// this.route is an instance of ActivatedRoute

this.router.navigate(['../', { relativeTo: this.route }]); // Relative navigation

```

##### Route parameters :-

```
{ path: 'products/:id', component: ProductDetailComponent },

constructor(private route: ActivatedRoute) { }

this.route.params.subscribe(params => {
    const productId = params['id']; // Do something with the productId
});

OR

const productId = this.route.snapshot.paramMap.get('id');

//Cannot load new changes if the id is changed from the  component assigned to products/:id, that is ProductDetailComponent here.
```

##### Query parameters :-

```
constructor(private router: Router) { }

this.router.navigate(['/product-details'], { queryParams: { id:productId } });

OR

<a [routerLink]="['/product-details']" [queryParams]="{ id: product.id }">View Details</a>

// Fetching data passed :

constructor(private route: ActivatedRoute) { }

this.route.queryParams.subscribe(params => {
    const productId = params['id'];
    // Do something with productId...
});

OR

const productId = this.route.snapshot.queryParamMap.get('id');

// { path: 'products', component: ProductsComponent },
// Cannot load new changes if the params are changed from the  component assigned to products route, that is ProductsComponent here.

```

> Route parameters are required to match the route , query parameters are not

##### Passing Data in routes :

1. **Static Data** : use the **data** property in the route configuration.

```
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent, data: { title: 'About Page', description: 'This is the about page.' } }
];

// In About component :

constructor(private route: ActivatedRoute) { }

ngOnInit() {
  this.data = this.route.snapshot.data;

  OR

  this.route.data.subscribe((data)=>{
    this.data = data;
  })
}
```

2. **Dynamic Data** :

```
<a [routerLink]="['/destination-route']" [state]="{ key: 'value' }">Go to Destination</a>

OR

constructor(private router: Router) {}

this.router.navigate(['/destination-route'], { state: data });

GET DATA

constructor(private route: ActivatedRoute) {}

ngOnInit() {
    this.route.paramMap.subscribe(params => {
        const stateData = window.history.state;
    });
}

OR

constructor(private router: Router) {
    const data = this.router.getCurrentNavigation().extras.state;
  }

```

##### Child routes :

```
const routes: Routes = [
  {
    path: 'parent',
    component: ParentComponent,
    children: [
      { path: 'child1', component: Child1Component },
      { path: 'child2', component: Child2Component }
    ]
  }
];

// Parent Component Template :

template: `
    <h2>Parent Component</h2>
    <p>This is the parent component.</p>
    <router-outlet></router-outlet>
    <!-- This is where child components will be rendered -->
  `
```

##### Lazy Loading :-

```
const routes: Routes = [
  // Other routes
  {
    path: 'lazyload',
    loadChildren: () => import('./path/to/lazyLoad.module').then(m => m.LazyLoadModule)
  }
];

```

#### Route Guards :-

1. **canActivate** :

- canActivate guards run before a route is activated.
- They can return true to allow access, false to deny access, or a UrlTree to redirect to another route.

* **Class Based Approach :-**

```
export class AuthGuard implements CanActivate {
  canActivate(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): boolean | UrlTree {
    // Perform authentication logic here
    if (authenticated()) {
      return true; // Allow access
    } else {
      return this.router.createUrlTree(['/login']); // Redirect to login
    }
  }
}
```

- Functional Based Approach :-

```
export function authGuard(router: Router) {
  return canActivate((route: ActivatedRouteSnapshot, state: RouterStateSnapshot) => {
    // Authentication logic here
    if (authenticated()) {
      return true;
    } else {
      return router.createUrlTree(['/login']);
    }
  });
}

OR

export const CanActivate = () => {
    const authService = inject(AuthService);
    const router = inject(Router);

    if(authService.IsAuthenticated()){
        return true;
    }else{
        router.navigate(['/Login']);
        return false;
    }
}
```

```
const routes: Routes = [
  {
    path: 'protected',
    component: ProtectedComponent,
    canActivate: [AuthGuard], // Use the guard here
  },
];
```

2. **canActivateChild** : Controls access to child routes of a parent route, parent route can still be accessed.

```
const routes: Routes = [
  {
    path: 'admin',
    component: AdminComponent,
    canActivateChild: [AuthGuard],
    children: [
      { path: 'dashboard', component: DashboardComponent },
      { path: 'settings', component: SettingsComponent }
    ]
  }
];
```

3. **canDeactivate :-**

- It's a route guard that protects components from accidental navigation away.
- It's triggered when a user tries to leave a route, giving you a chance to intervene.

```
export interface IDeactivateComponent{
    canExit: () => boolean | Observable<boolean> | Promise<boolean>;
}

export class CanDeactivateGuard implements {
  canDeactivate(component: IDeactivateComponent,
                currentRoute: ActivatedRouteSnapshot,
                currentState: RouterStateSnapshot,
                nextState: RouterStateSnapshot)
    {
        return component.canExit();
    }
}

export class MyComponent implements IDeactivateComponent {
  canExit(){
    // Returns true false
  }
}

const routes: Routes = [
  {
    path: 'my-component',
    component: MyComponent,
    canDeactivate: [CanDeactivateGuard]
  }
];
```

4. **Resolve :-**

- intercepts navigation to a route and waits for data to be resolved before activating the corresponding component.
- used to pre-fetch data required by a component before it's rendered, ensuring a smoother user experience and avoiding initial loading delays.

```
// Resolver Service :-

export class HeroResolver implements Resolve<any> {
  constructor(/* Inject any necessary services here */) {}

  resolve(route: ActivatedRouteSnapshot): any {
    // Fetch data, often using a service
    const heroId = route.paramMap.get('id');
    return this.heroService.getHero(heroId);
  }
}

// Route Defination

const routes: Routes = [
  {
    path: 'hero/:id',
    component: HeroComponent,
    resolve: {
      hero: HeroResolver
    }
  }
]

// Fetching data in HeroComponent :

constructor(private route: ActivatedRoute) {}

ngOnInit() {
  this.hero = this.route.snapshot.data['hero'];
}

```

```
// Functional Approach :

constructor(private route: ActivatedRoute) {}

export const resolveHero = () =>{
  const heroService = inject(HeroService);
  return heroService.getAllHeros();
}

const routes: Routes = [
  {
    path: 'hero/:id',
    component: HeroComponent,
    resolve: {
      hero: resolveHero
    }
  }
]
```
