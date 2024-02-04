## NgRx :

### 1. Actions :-

- Creating Action using Factory Method :-

```
import { createAction, props } from '@ngrx/store';

export const login = createAction(
  '[Login Page] Login',
  props<{ username: string; password: string }>()
);

//  Dispatching an action :

onSubmit(username: string, password: string) {
  store.dispatch(login({ username: username, password: password }));
}

// Returns

{ type: '[Login Page] Login', username: string; password: string; }
```

- Creating Action using classes :-

```
import { Action } from '@ngrx/store';

export class Login implements Action {
  readonly type = '[Login Page] Login';

  constructor(public payload: { username: string; password: string }) {}
}

// Disptaching an action :-
click(username: string, password: string) {
  store.dispatch(new Login({ username: username, password: password }));
}

// Returns
{ type: '[Login Page] Login', payload: { username: string, password:string; } }

```

### 2. Reducers :-

- Creating reducer using Factory Method

```
const myReducer = createReducer(
  initialState,

  // Define how the state should change for each action using the `on` function
  on(MyActions.someAction, (state, action) => {
    // Return the updated state based on the action
    return { ...state, /* updated properties */ };
  }),

  on(MyActions.anotherAction, (state, action) => {
    // Return the updated state based on the action
    return { ...state, /* updated properties */ };
  }),

  // Handle other actions or define default behavior
);
```

- Creating reducer using pure functions and switch statement

```

export function reducer(state: State = initialState, action: Action): State {
  switch (action.type) {
    case 'SOME_ACTION':
      // Handle state changes for 'SOME_ACTION' type
      return { ...state, /* updated properties */ };

    case 'ANOTHER_ACTION':
      // Handle state changes for 'ANOTHER_ACTION' type
      return { ...state, /* updated properties */ };

    default:
      // If action type is not recognized, return current state
      return state;
  }
}
```

- Register reducers inside the imports array of @NgModule, use the StoreModule.forRoot() method to register your reducers:

```
@NgModule({
  imports: [
    // Other imports...
    StoreModule.forRoot({
      feature1: reducer1,
      feature2: reducer2,
      // Add more reducers as needed
    }),
  ],
})
```

- For lazy loaded modules : If you have lazy-loaded feature modules with their own state slices, use StoreModule.forFeature() in the feature module's imports:

```
@NgModule({
  imports: [
    // Other imports...
    StoreModule.forFeature('featureName', featureReducer),
  ],
  // Other module configurations...
})
```

### 3. Selectors :-

- **Selectors are pure functions used for obtaining slices of store state.**

```
export const selectFeature = (state: AppState) => state.feature;

export const selectFeatureCount = createSelector(
  selectFeature,
  (state: FeatureState) => state.counter
);

this.data$ = this.store.pipe(select(selectData));

OR

this.counter = this.store.select(fromRoot.selectCount, params)
```

> The createSelector function can take up to 8 selector functions for more complete state selections.

### 5. Effects :-

> ng add @ngrx/effects

- Registering root effects :-

```
@NgModule({
  imports: [
    EffectsModule.forRoot([MovieEffects])
  ],
})
```

```
@Injectable()
export class MovieEffects {

  @Effect()
  loadMovies$ = this.actions$
    .pipe(
      ofType('[Movies Page] Load Movies'),
      mergeMap(() => this.moviesService.getAll()
        .pipe(
          map(movies => ({ type: '[Movies API] Movies Loaded Success', payload: movies })),
          catchError(() => of({ type: '[Movies API] Movies Loaded Error' }))
        ))
      )
    );

  constructor(
    private actions$: Actions,
    private moviesService: MoviesService
  ) {}
}
```
