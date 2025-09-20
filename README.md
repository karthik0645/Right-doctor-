# Right-doctor

1. Project Setup
ng new people-app
cd people-app
ng serve -o
2. Generate Components
ng g c components/people-list
ng g c components/edit-person
ng g c components/delete-person
3. Create a Model (person.ts)
Inside src/app/models/person.ts:

export interface Person {
  id: number;
  name: string;
  age: number;
  email: string;
}
4. Create a Service (people.service.ts)
Inside src/app/services/people.service.ts:

import { Injectable } from '@angular/core';
import { Person } from '../models/person';

@Injectable({
  providedIn: 'root'
})
export class PeopleService {
  private people: Person[] = [
    { id: 1, name: 'John Doe', age: 25, email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', age: 30, email: 'jane@example.com' },
    { id: 3, name: 'Mark Wilson', age: 40, email: 'mark@example.com' }
  ];

  getPeople(): Person[] {
    return this.people;
  }

  getPerson(id: number): Person | undefined {
    return this.people.find(p => p.id === id);
  }

  updatePerson(updated: Person) {
    const index = this.people.findIndex(p => p.id === updated.id);
    if (index !== -1) this.people[index] = updated;
  }

  deletePerson(id: number) {
    this.people = this.people.filter(p => p.id !== id);
  }
}
5. Set up Routing (app-routing.module.ts)
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { PeopleListComponent } from './components/people-list/people-list.component';
import { EditPersonComponent } from './components/edit-person/edit-person.component';
import { DeletePersonComponent } from './components/delete-person/delete-person.component';

const routes: Routes = [
  { path: '', component: PeopleListComponent },
  { path: 'edit/:id', component: EditPersonComponent },
  { path: 'delete/:id', component: DeletePersonComponent },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
6. People List Component
people-list.component.ts

import { Component } from '@angular/core';
import { PeopleService } from '../../services/people.service';
import { Person } from '../../models/person';

@Component({
  selector: 'app-people-list',
  templateUrl: './people-list.component.html'
})
export class PeopleListComponent {
  people: Person[];

  constructor(private peopleService: PeopleService) {
    this.people = this.peopleService.getPeople();
  }
}
people-list.component.html

<h2>People List</h2>
<table border="1" cellpadding="5">
  <tr>
    <th>Name</th><th>Age</th><th>Email</th><th>Actions</th>
  </tr>
  <tr *ngFor="let person of people">
    <td>{{ person.name }}</td>
    <td>{{ person.age }}</td>
    <td>{{ person.email }}</td>
    <td>
      <a [routerLink]="['/edit', person.id]">Edit</a> | 
      <a [routerLink]="['/delete', person.id]">Delete</a>
    </td>
  </tr>
</table>
7. Edit Person Component
edit-person.component.ts

import { Component } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { PeopleService } from '../../services/people.service';
import { Person } from '../../models/person';

@Component({
  selector: 'app-edit-person',
  templateUrl: './edit-person.component.html'
})
export class EditPersonComponent {
  person: Person | undefined;

  constructor(
    private route: ActivatedRoute,
    private router: Router,
    private peopleService: PeopleService
  ) {
    const id = Number(this.route.snapshot.paramMap.get('id'));
    this.person = this.peopleService.getPerson(id);
  }

  save() {
    if (this.person) {
      this.peopleService.updatePerson(this.person);
      this.router.navigate(['/']);
    }
  }
}
edit-person.component.html

<h2>Edit Person</h2>
<div *ngIf="person">
  <form (ngSubmit)="save()">
    <label>Name: <input [(ngModel)]="person.name" name="name"></label><br>
    <label>Age: <input [(ngModel)]="person.age" name="age" type="number"></label><br>
    <label>Email: <input [(ngModel)]="person.email" name="email"></label><br>
    <button type="submit">Save</button>
  </form>
</div>
8. Delete Person Component
delete-person.component.ts

import { Component } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { PeopleService } from '../../services/people.service';
import { Person } from '../../models/person';

@Component({
  selector: 'app-delete-person',
  templateUrl: './delete-person.component.html'
})
export class DeletePersonComponent {
  person: Person | undefined;

  constructor(
    private route: ActivatedRoute,
    private router: Router,
    private peopleService: PeopleService
  ) {
    const id = Number(this.route.snapshot.paramMap.get('id'));
    this.person = this.peopleService.getPerson(id);
  }

  delete() {
    if (this.person) {
      this.peopleService.deletePerson(this.person.id);
      this.router.navigate(['/']);
    }
  }
}
delete-person.component.html

<h2>Delete Person</h2>
<div *ngIf="person">
  <p>Are you sure you want to delete <b>{{ person.name }}</b>?</p>
  <button (click)="delete()">Yes, Delete</button>
  <button (click)="router.navigate(['/'])">Cancel</button>
</div>
