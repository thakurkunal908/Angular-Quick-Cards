## Forms in Angular :-

##### Forms Module :-

- binds the form field to the Angular component class.
- encapsulates all the input fields into an object structure when the user submits the form.

- **BUILDING BLOCKS**
     - **FormControl** :
          - represents a single input field in an Angular form.
          - an object that encapsulates all the information related to the single input element.
          - used to set/get the value of the Form field, find the status of form field like (valid/invalid, pristine/dirty, touched/untouched ) etc & add validation rules to it.

```
let firstname = new FormControl(); //Creating a FormControl in a Reactive forms
firstname.value   //Returns the value of the first name field
firstname.errors      // returns the list of errors
firstname.dirty       // true if the value has changed (dirty)
firstname.touched     // true if input field is touched
firstname.valid       // true if the input value has passed all the validation

```

-    - **FormGroup :**
          - a collection of FormControls, where each FormControl is a property in a FormGroup, with the **control name as the key**.
          - can also contain other form groups

#### Template Driven forms :-

- we specify behaviors/validations using directives and attributes in our template

- **ngForm :-**
     - convert html form to the Template-driven form
     - create the top-level **FormGroup** control
     - Added automatically if not added
     - CreatesFormControl instance for each of child control, which has ngModel directive.
     - CreatesFormGroup instance for each of the NgModelGroup directive.
- **ngModel :-**
     - create the **FormControl** instance for each of the HTML form elements.
     - also provides the two-way data binding
     - ngModel will use the name attribute to create the FormControl instance for each of the Form field
- **ngModelGroup :-** Nested Form Group
     - `<div ngModelGroup="address"></div>`
- **ngSubmit :-** Handles form submit event

<br>

- **CODE :-**

```
@NgModule({
  imports: [
    ...
    FormsModule,   //Add in Imports Array of root / shared module
  ],
})

<form #contactForm="ngForm" (ngSubmit)="onSubmit(contactForm)">
    <input type="text" name="firstname" #fname="ngModel" ngModel>
    <input type="text" name="lastname" ngModel>
</form>
```

###### Set value in template-driven forms :-

- Two-way data binding

```
<input type="text" id="firstname" name="firstname" [(ngModel)]="contact.firstname">

// To set the initial or default value all you need to populate the contact model in the ngOnInit method

ngOnInit() {
    this.contact = {
      firstname: "Sachin",
    };
  }

contactForm.resetForm(); // Reset form
```

- Use the template reference variable

```
<form #contactForm="ngForm" (ngSubmit)="onSubmit(contactForm)">

@ViewChild('contactForm',null) contactForm: NgForm;

ngOnInit() {

   this.contact = {
      firstname: "Sachin",
    };

    setTimeout(() => {
      this.contactForm.setValue(this.contact);
    });
  }

 this.contactForm.controls["firstname"].setValue("Some Name");

 this.contactForm.control.patchValue(obj); // set values for some form controls
```

#### Reactive Forms :-

- Steps :-

     - Import ReactiveFormsModule
     - Create Form Model in component class using FormGroup FormControl & FormArrays
     - Create the HTML Form resembling the Form Model.
     - Bind the HTML Form to the Form Model

- Directives :-
     - **formGroup** -- Top Level Form Group
     - **formControlName** -- Form Control
     - **formGroupName** -- Nested form groups

```
contactForm = new FormGroup({
  firstname: new FormControl(),
  lastname: new FormControl(),
   address:new FormGroup({
    city: new FormControl(),
    street: new FormControl(),
  })
})

<form [formGroup]="contactForm" (ngSubmit)="onSubmit()">
  <input type="text" id="firstname" name="firstname" formControlName="firstname">
  <input type="text" id="lastname" name="lastname" formControlName="lastname">
</form>

<div formGroupName="address"></div>

onSubmit() {
  console.log(this.contactForm.value);
}
```

#### FormBuilder :-

- Helper API to build forms , provides shortcuts to create the instance of the FormControl, FormGroup or FormArray, reduces the code required to write the complex forms.

```
import { FormBuilder } from '@angular/forms'

// In Component Class
constructor(private formBuilder: FormBuilder) {}

this.contactForm = this.formBuilder.group({
  firstname: ['', [Validators.required, Validators.minLength(10)]],
  lastname: [''],
  address: this.formBuilder.group({
    city: [''],
  })
})

this.reactiveForm.setValue(contact);
this.reactiveForm.get("address").patchValue(address);
```

##### Form states :-

- **untouched :** The field has not been touched yet
- **touched :** The field has been touched
- **pristine :** The field has not been modified yet
- **dirty :** The field has been modified
- **invalid :** The field content is not valid
- **valid :** The field content is valid
