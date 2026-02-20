## 1. Problem 1: Fetch and Display Users

```
https://jsonplaceholder.typicode.com/users
```

```ts

//user interface

export interface User {
  id: number;
  name: string;
  email: string;
  phone: string;
  website: string;
}


//service

private apiUrl = 'https://jsonplaceholder.typicode.com/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl)
      .pipe(
        catchError(this.handleError)
      );
  }



//use the service

constructor(private userService: UserService) {}

  ngOnInit() {
    this.loadUsers();
  }

  loadUsers() {
    this.loading = true;
    this.error = '';

    this.userService.getUsers().subscribe({
      next: data => {
        this.users = data;
        this.loading = false;
      },
      error: err => {
        this.error = err.message;
        this.loading = false;
      }
    });
  }


//display user

<h1>User List</h1>

<!-- Loading -->
<div *ngIf="loading">
  Loading users...
</div>

<!-- Error -->
<div *ngIf="error" style="color:red;">
  {{ error }}
</div>

<!-- Users -->
<ul *ngIf="!loading && !error">
  <li *ngFor="let user of users">
    <strong>{{ user.name }}</strong><br>
    Email: {{ user.email }}<br>
    Phone: {{ user.phone }}<br>
    Website: {{ user.website }}
  </li>
</ul>


or

<div>
   <h2>Display Users</h2>
   @if(users){
      @for(user of users;track user.id){
         <div>
         <p>{{user.name}}</p>
      </div>
      } @empty{
         <h1>No user</h1>
      }
   }
</div>

```


