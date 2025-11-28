

## Angular OOP: Beginner to Intermediate Guide

  Part 1: Beginner Concepts
  1. Classes in TypeScript/Angular
  Angular is built with TypeScript, which fully supports OOP. 
  A class is a blueprint for creating objects.
```
  // Basic class
  export class User {
    name: string;
    age: number;

    constructor(name: string, age: number) {
      this.name = name;
      this.age = age;
    }

    greet(): string {
      return `Hello, I'm ${this.name}`;
    }
  }

  // Usage
  const user = new User('John', 25);
  console.log(user.greet()); // "Hello, I'm John" 
```

  2. Components as Classes

  Angular components are classes decorated with@Component:
```
  import { Component } from '@angular/core';

  @Component({
    selector: 'app-user',
    template: '<h1>{{ userName }}</h1>'
  })
  export class UserComponent {
    userName: string = 'John Doe';

    constructor() {
      console.log('Component created');
    }

    changeUser(): void {
      this.userName = 'Jane Smith';
    }
  }
  ```

3. Encapsulation (Access Modifiers)

  Control access to class members:
```
  export class BankAccount {
    public accountNumber: string;      // Accessible everywhere
    private balance: number;            // Only within this class
    protected accountType: string;      // This class and subclasses

    constructor(accountNumber: string, initialBalance: number) {
      this.accountNumber = accountNumber;
      this.balance = initialBalance;
      this.accountType = 'Savings';
    }

    public getBalance(): number {
      return this.balance;
    }

    private calculateInterest(): number {
      return this.balance * 0.05;
    }

    public deposit(amount: number): void {
      this.balance += amount;
    }
  }
```
  4. Services as Singleton Classes

  Angular services are reusable classes for business logic:
```
  import { Injectable } from '@angular/core';

  @Injectable({
    providedIn: 'root' // Singleton across the app
  })
  export class UserService {
    private users: User[] = [];

    addUser(user: User): void {
      this.users.push(user);
    }

    getUsers(): User[] {
      return this.users;
    }

    findUserById(id: number): User | undefined {
      return this.users.find(u => u.id === id);
    }
  }
```
  ---
  Part 2: Intermediate Concepts

  1. Inheritance

  Extend classes to reuse and specialize behavior:
```
  // Base class
  export class Animal {
    constructor(protected name: string) {}

    move(distance: number): void {
      console.log(`${this.name} moved ${distance}m`);
    }

    makeSound(): void {
      console.log('Some generic sound');
    }
  }

  // Derived classes
  export class Dog extends Animal {
    constructor(name: string) {
      super(name); // Call parent constructor
    }

    makeSound(): void {
      console.log(`${this.name} barks: Woof!`);
    }

    fetch(): void {
      console.log(`${this.name} fetches the ball`);
    }
  }

  export class Cat extends Animal {
    constructor(name: string, private lives: number = 9) {
      super(name);
    }

    makeSound(): void {
      console.log(`${this.name} meows: Meow!`);
    }
  }

  // Usage
  const dog = new Dog('Rex');
  dog.makeSound(); // "Rex barks: Woof!"
  dog.move(10);    // "Rex moved 10m"
  dog.fetch();     // "Rex fetches the ball"
```
  2. Polymorphism

  Same interface, different implementations:
```
  // Interface for shape
  interface Shape {
    calculateArea(): number;
    draw(): void;
  }

  export class Circle implements Shape {
    constructor(private radius: number) {}

    calculateArea(): number {
      return Math.PI * this.radius ** 2;
    }

    draw(): void {
      console.log('Drawing a circle');
    }
  }

  export class Rectangle implements Shape {
    constructor(private width: number, private height: number) {}

    calculateArea(): number {
      return this.width * this.height;
    }

    draw(): void {
      console.log('Drawing a rectangle');
    }
  }

  // Polymorphic usage
  export class DrawingService {
    drawShapes(shapes: Shape[]): void {
      shapes.forEach(shape => {
        shape.draw();
        console.log(`Area: ${shape.calculateArea()}`);
      });
    }
  }
```
  3. Abstract Classes

  Define common structure with some implementation:
```
  export abstract class BaseComponent {
    protected data: any[] = [];

    abstract loadData(): void;
    abstract renderData(): void;

    // Common implementation
    refresh(): void {
      console.log('Refreshing...');
      this.loadData();
      this.renderData();
    }
  }

  export class UserListComponent extends BaseComponent {
    loadData(): void {
      // Specific implementation
      this.data = [/* fetch users */];
    }

    renderData(): void {
      console.log('Rendering users:', this.data);
    }
  }

  export class ProductListComponent extends BaseComponent {
    loadData(): void {
      this.data = [/* fetch products */];
    }

    renderData(): void {
      console.log('Rendering products:', this.data);
    }
  }
```
  4. Interfaces in Angular

  Define contracts for classes:
```
  // Model interfaces
  export interface IUser {
    id: number;
    name: string;
    email: string;
    role: UserRole;
  }

  export enum UserRole {
    Admin = 'ADMIN',
    User = 'USER',
    Guest = 'GUEST'
  }

  // Repository pattern interface
  export interface IRepository<T> {
    getAll(): Observable<T[]>;
    getById(id: number): Observable<T>;
    create(item: T): Observable<T>;
    update(id: number, item: T): Observable<T>;
    delete(id: number): Observable<void>;
  }

  // Implementation
  @Injectable()
  export class UserRepository implements IRepository<IUser> {
    constructor(private http: HttpClient) {}

    getAll(): Observable<IUser[]> {
      return this.http.get<IUser[]>('/api/users');
    }

    getById(id: number): Observable<IUser> {
      return this.http.get<IUser>(`/api/users/${id}`);
    }

    create(user: IUser): Observable<IUser> {
      return this.http.post<IUser>('/api/users', user);
    }

    update(id: number, user: IUser): Observable<IUser> {
      return this.http.put<IUser>(`/api/users/${id}`, user);
    }

    delete(id: number): Observable<void> {
      return this.http.delete<void>(`/api/users/${id}`);
    }
  }
```
  5. Dependency Injection (DI)

  Angular's DI system follows OOP principles:
```
  // Service with dependencies
  @Injectable({
    providedIn: 'root'
  })
  export class OrderService {
    constructor(
      private http: HttpClient,
      private userService: UserService,
      private logger: LoggerService
    ) {}

    createOrder(order: Order): Observable<Order> {
      const user = this.userService.getCurrentUser();
      this.logger.log(`Creating order for ${user.name}`);
      return this.http.post<Order>('/api/orders', order);
    }
  }

  // Component using the service
  @Component({
    selector: 'app-order',
    template: '...'
  })
  export class OrderComponent {
    constructor(private orderService: OrderService) {}

    placeOrder(order: Order): void {
      this.orderService.createOrder(order).subscribe(
        result => console.log('Order placed:', result)
      );
    }
  }
```
  6. Design Patterns in Angular
```
  Singleton Pattern (via DI):
  @Injectable({ providedIn: 'root' })
  export class ConfigService {
    private config: AppConfig;
    // Single instance across app
  }

  Observer Pattern (RxJS):
  export class DataService {
    private dataSubject = new BehaviorSubject<Data[]>([]);
    public data$ = this.dataSubject.asObservable();

    updateData(newData: Data[]): void {
      this.dataSubject.next(newData);
    }
  }

  Factory Pattern:
  export abstract class Logger {
    abstract log(message: string): void;
  }

  export class ConsoleLogger extends Logger {
    log(message: string): void {
      console.log(message);
    }
  }

  export class FileLogger extends Logger {
    log(message: string): void {
      // Write to file
    }
  }

  @Injectable()
  export class LoggerFactory {
    createLogger(type: 'console' | 'file'): Logger {
      switch(type) {
        case 'console': return new ConsoleLogger();
        case 'file': return new FileLogger();
      }
    }
  }
```
  7. Generic Classes

  Create reusable components with type safety:
```
  export class DataStore<T> {
    private items: T[] = [];

    add(item: T): void {
      this.items.push(item);
    }

    getAll(): T[] {
      return this.items;
    }

    find(predicate: (item: T) => boolean): T | undefined {
      return this.items.find(predicate);
    }
  }

  // Usage
  const userStore = new DataStore<IUser>();
  userStore.add({ id: 1, name: 'John', email: 'john@example.com', role: UserRole.User });

  const productStore = new DataStore<Product>();
  productStore.add({ id: 1, name: 'Laptop', price: 999 });
  ```

 ---
  #### Key Principles to Remember

  1. Single Responsibility: Each class should have one reason to change
  2. Separation of Concerns: Components handle UI, Services handle logic
  3. DRY (Don't Repeat Yourself): Use inheritance and composition
  4. Composition over Inheritance: Favor injecting services over extending classes
  5. Type Safety: Use TypeScript's type system fully

---
#### Practice Exercise

Create a simple blog system with:
- Post class with properties (id, title, content, author)
- Author class with properties (id, name, bio)
- BlogService for CRUD operations
- PostComponent to display posts
- Use interfaces, inheritance, and DI

This covers the fundamentals and intermediate concepts of OOP in Angular. Would you like me to create example files in your directory to demonstrate any of these
  concepts, or would you like me to dive deeper into any specific topic?