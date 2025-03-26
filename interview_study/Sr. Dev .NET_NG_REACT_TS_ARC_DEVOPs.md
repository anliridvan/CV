# Technical .NET / TypeScript / Angular / React Developer Interview Prep

## Time: 10-Hour Study Plan (Tailored for 10+ Years Experience)

---

### 👤 Profile-Based Focus:

**Experienced and highly skilled .NET, SQL, TS, and JS developer with over 10 years of expertise in secure coding, refactoring, and model design. Industry exposure: Public Safety, Insurance, Travel, Crypto.**

---

## 1. .NET Core / C# (2 Hours)

### In-Depth Interview Questions + Answers

**Q1: Explain the differences between .NET Core, .NET 5/6/7, and .NET Framework.**\
A: .NET Framework is Windows-only and designed for legacy desktop and web apps. .NET Core is cross-platform and modular. .NET 5/6/7 unifies all .NET platforms, adding performance, APIs, and cloud-native features.

**Q2: What are the benefits of using Dependency Injection in .NET Core?**\
A: It allows loose coupling, easier testing, and better maintainability. Services are registered via `IServiceCollection` and injected via constructors.

**Q3: How does middleware work in the ASP.NET Core pipeline?**\
A: Middleware are delegates that handle HTTP requests/responses in sequence. Each middleware can modify the request and response or pass it on.

**Q4: Describe SOLID principles with real .NET implementation examples.**\
A:

- S: Single Responsibility - one class = one reason to change
- O: Open/Closed - extend behavior without modifying code
- L: Liskov Substitution - derived classes work where base classes do
- I: Interface Segregation - smaller, focused interfaces
- D: Dependency Inversion - use abstractions not concrete classes

**Q5: How do you design a clean architecture in a .NET Core API?**\
A: Separate layers: Presentation (API), Application, Domain, Infrastructure. Dependency injection is used to enforce boundaries. Domain contains core logic.

**Q6: How do you secure a .NET Core Web API (JWT, CORS, Rate Limiting)?**\
A: Use JWT for authentication (`AddAuthentication`, `UseAuthentication`). Enable CORS using middleware. Use middleware or packages for rate limiting.

**Q7: Compare synchronous vs asynchronous patterns in .NET Core.**\
A: Async frees threads for scalability (e.g., with async I/O). Sync blocks threads and can cause performance bottlenecks in high-load apps.

**Q8: What is MediatR, and why would you use it over direct service calls?**\
A: MediatR decouples request handling via the mediator pattern. Improves separation of concerns in CQRS or layered systems.

### Hands-On Tasks:

- ✅ Build a RESTful .NET Core API
- ✅ Register services and middleware
- ✅ Use EF Core with Repository pattern
- ✅ Add Swagger and JWT auth
- ✅ Create and consume custom middleware



# 🧪 Hands-On Tasks: .NET Core / C#

This section walks through practical tasks to reinforce concepts for a .NET Core Web API project.

## 🔹 Task 1: Build a RESTful .NET Core API

1. **Initialize Project**
   ```bash
   dotnet new webapi -n ProductCatalogApi
   cd ProductCatalogApi
   ```

2. **Create a Model** (`Models/Product.cs`)
   ```csharp
   public class Product {
       public int Id { get; set; }
       public string Name { get; set; }
       public decimal Price { get; set; }
   }
   ```

3. **Create a Controller** (`Controllers/ProductController.cs`)
   ```csharp
   [ApiController]
   [Route("api/[controller]")]
   public class ProductController : ControllerBase {
       private static List<Product> products = new();

       [HttpGet]
       public IActionResult Get() => Ok(products);
       
       [HttpPost]
       public IActionResult Create(Product product) {
           products.Add(product);
           return CreatedAtAction(nameof(Get), new { id = product.Id }, product);
       }
   }
   ```

4. **Run the App**
   ```bash
   dotnet run
   ```

## 🔹 Task 2: Register Services and Middleware

1. **Create a Service Interface and Class** (`Services/IProductService.cs` + `ProductService.cs`)
   ```csharp
   public interface IProductService {
       IEnumerable<Product> GetAll();
       void Add(Product product);
   }

   public class ProductService : IProductService {
       private readonly List<Product> _products = new();
       public IEnumerable<Product> GetAll() => _products;
       public void Add(Product product) => _products.Add(product);
   }
   ```

2. **Register Service in `Program.cs`**
   ```csharp
   builder.Services.AddSingleton<IProductService, ProductService>();
   ```

3. **Inject into Controller**
   ```csharp
   private readonly IProductService _service;
   public ProductController(IProductService service) => _service = service;
   ```

## 🔹 Task 3: Use EF Core with Repository Pattern

1. **Install EF Core**
   ```bash
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.Design
   ```

2. **Create DbContext** (`Data/AppDbContext.cs`)
   ```csharp
   public class AppDbContext : DbContext {
       public DbSet<Product> Products { get; set; }
       public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
   }
   ```

3. **Add Connection String in `appsettings.json`**
   ```json
   "ConnectionStrings": {
     "DefaultConnection": "Server=localhost;Database=ProductDb;Trusted_Connection=True;"
   }
   ```

4. **Register DbContext in `Program.cs`**
   ```csharp
   builder.Services.AddDbContext<AppDbContext>(options =>
       options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
   ```

5. **Create Repository Interface and Implementation**
   ```csharp
   // IProductRepository.cs
   public interface IProductRepository {
       IEnumerable<Product> GetAll();
       void Add(Product product);
   }

   // ProductRepository.cs
   public class ProductRepository : IProductRepository {
       private readonly AppDbContext _context;
       public ProductRepository(AppDbContext context) => _context = context;

       public IEnumerable<Product> GetAll() => _context.Products.ToList();

       public void Add(Product product) {
           _context.Products.Add(product);
           _context.SaveChanges();
       }
   }
   ```

## 🔹 Task 4: Add Swagger and JWT Authentication

1. **Add Swagger**
   ```bash
   dotnet add package Swashbuckle.AspNetCore
   ```

   In `Program.cs`:
   ```csharp
   builder.Services.AddEndpointsApiExplorer();
   builder.Services.AddSwaggerGen();

   app.UseSwagger();
   app.UseSwaggerUI();
   ```

2. **Add JWT Authentication**
   ```bash
   dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
   ```

   In `Program.cs`:
   ```csharp
   builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
       .AddJwtBearer(options => {
           options.TokenValidationParameters = new TokenValidationParameters {
               ValidateIssuer = true,
               ValidateAudience = true,
               ValidateLifetime = true,
               ValidateIssuerSigningKey = true,
               ValidIssuer = "yourdomain.com",
               ValidAudience = "yourdomain.com",
               IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your_secret_key"))
           };
       });

   app.UseAuthentication();
   app.UseAuthorization();
   ```

## 🔹 Task 5: Create and Consume Custom Middleware

1. **Create Middleware** (`Middlewares/RequestLoggingMiddleware.cs`)
   ```csharp
   public class RequestLoggingMiddleware {
       private readonly RequestDelegate _next;
       public RequestLoggingMiddleware(RequestDelegate next) => _next = next;

       public async Task Invoke(HttpContext context) {
           Console.WriteLine($"Request: {context.Request.Method} {context.Request.Path}");
           await _next(context);
       }
   }
   ```

2. **Register Middleware in `Program.cs`**
   ```csharp
   app.UseMiddleware<RequestLoggingMiddleware>();
   ```

---

✅ **Next Steps**  
- Modularize logic using SOLID principles  
- Add DTOs + AutoMapper  
- Implement global error handling middleware  
- Apply Clean Architecture  
- Deploy with Docker + CI/CD



---

## 2. SQL Server / RDBMS + Azure CosmosDB (1.5 Hours)

### Advanced Interview Questions + Answers

**Q1: What’s the difference between OLTP and OLAP systems?**\
A: OLTP handles real-time transactions (INSERT, UPDATE), OLAP is for analytics and reporting (aggregations, historical data).

**Q2: How would you model and partition a large dataset with billions of records?**\
A: Use partitioning strategies like horizontal (sharding) or table partitioning based on date or region. Optimize indexes.

**Q3: Explain indexing strategies and their impact on query performance.**\
A: Clustered index sorts data; only one per table. Non-clustered uses pointers. Use covering indexes for read-heavy tables.

**Q4: Describe a real scenario where a deadlock occurred and how you resolved it.**\
A: Example: Two transactions locking rows in reverse order. Fixed using consistent lock ordering and retry logic.

**Q5: Compare Azure CosmosDB and SQL Server in terms of consistency, scaling, and performance.**\
A: SQL Server is ACID-compliant and relational. CosmosDB is schema-less, globally distributed with tunable consistency.

**Q6: What is CAP Theorem and how does CosmosDB apply it?**\
A: It states you can only guarantee two of Consistency, Availability, and Partition tolerance. CosmosDB offers configurable consistency levels.

### Practice:

- ✅ Write SQL with window functions (e.g., RANK, ROW\_NUMBER)
- ✅ Use CTEs to organize complex logic
- ✅ Create stored procedures for data ops
- ✅ Model CosmosDB JSON documents
- ✅ Optimize SELECT queries with execution plans
---
# 🧪 Hands-On Tasks: SQL Server / RDBMS + Azure CosmosDB

This section provides practical hands-on exercises to reinforce database design, SQL querying, and NoSQL modeling with CosmosDB.

## 🔹 Task 1: Write SQL with Window Functions

1. **Sample Table: Employee Salaries**
   ```sql
   CREATE TABLE Employees (
       Id INT PRIMARY KEY,
       Name NVARCHAR(100),
       Department NVARCHAR(50),
       Salary DECIMAL(10,2)
   );
   ```

2. **Use RANK(), DENSE_RANK(), ROW_NUMBER()**
   ```sql
   SELECT Name, Department, Salary,
       RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS SalaryRank
   FROM Employees;
   ```

3. **Use NTILE() to group employees into quartiles**
   ```sql
   SELECT Name, Salary,
       NTILE(4) OVER (ORDER BY Salary DESC) AS Quartile
   FROM Employees;
   ```

---

## 🔹 Task 2: Use CTEs to Organize Complex Logic

1. **Create a CTE for Filtering High Earners**
   ```sql
   WITH HighEarners AS (
       SELECT * FROM Employees WHERE Salary > 80000
   )
   SELECT Name, Salary FROM HighEarners;
   ```

2. **Recursive CTE Example: Generate Dates**
   ```sql
   WITH DateSeries AS (
       SELECT CAST('2024-01-01' AS DATE) AS DateValue
       UNION ALL
       SELECT DATEADD(DAY, 1, DateValue)
       FROM DateSeries
       WHERE DateValue < '2024-01-10'
   )
   SELECT * FROM DateSeries;
   ```

---

## 🔹 Task 3: Create Stored Procedures for Data Operations

1. **Create a Stored Procedure to Insert an Employee**
   ```sql
   CREATE PROCEDURE AddEmployee
       @Name NVARCHAR(100),
       @Department NVARCHAR(50),
       @Salary DECIMAL(10,2)
   AS
   BEGIN
       INSERT INTO Employees (Name, Department, Salary)
       VALUES (@Name, @Department, @Salary);
   END;
   ```

2. **Execute Stored Procedure**
   ```sql
   EXEC AddEmployee @Name = 'Jane Doe', @Department = 'HR', @Salary = 75000;
   ```

---

## 🔹 Task 4: Model CosmosDB JSON Documents

1. **Design a Product Document**
   ```json
   {
     "id": "product-123",
     "name": "Wireless Mouse",
     "price": 25.99,
     "tags": ["electronics", "accessories"],
     "inventory": {
       "warehouse": "WH1",
       "quantity": 320
     }
   }
   ```

2. **Create Container in Azure CosmosDB Emulator**
   - Database: `StoreDB`
   - Container: `Products`
   - Partition Key: `/tags`

3. **Insert Document using Data Explorer or SDK**

---

## 🔹 Task 5: Optimize SELECT Queries Using Execution Plans

1. **Enable Execution Plan in SSMS**
   - Click `Include Actual Execution Plan` (Ctrl + M)

2. **Check Index Usage**
   ```sql
   SELECT * FROM Employees WHERE Department = 'Engineering';
   ```

3. **Create Index to Improve Performance**
   ```sql
   CREATE NONCLUSTERED INDEX IX_Department ON Employees(Department);
   ```

4. **Re-run and Compare Execution Plan**

---

✅ **Next Steps**
- Normalize and denormalize relational schemas  
- Implement transactions and isolation levels  
- Test CosmosDB partitioning with synthetic loads  
- Explore SQL indexing DMVs: `sys.dm_db_index_usage_stats`  
- Add pagination using `OFFSET-FETCH` in SQL Server

---

## 3. Angular (2 Hours)

### Expert Interview Questions + Answers

**Q1: What is Angular and how does it differ from AngularJS?**\
A: Angular is a modern TypeScript-based framework for building web apps. AngularJS (1.x) used JavaScript and had a different architecture (MVC vs component-based).

**Q2: What are the main building blocks of an Angular app?**\
A: Modules, Components, Templates, Services, Directives, Pipes, Routing.

**Q3: How does Angular handle dependency injection?**\
A: Angular has a hierarchical injector system. Services are provided in modules or components and injected via constructor.

**Q4: What is Change Detection and how does it work?**\
A: Angular uses Zone.js to track async events and run change detection. The default strategy checks the entire component tree.

**Q5: What is the difference between Reactive and Template-driven forms?**\
A: Reactive forms are model-driven and offer better scalability, testability, and dynamic form control. Template-driven forms are simpler, defined in the HTML template.

**Q6: What are Observables and how are they used in Angular?**\
A: Observables are provided by RxJS. They’re used for async operations (HTTP, forms, events). They support operators like `map`, `filter`, `switchMap`.

**Q7: How do you implement route guards?**\
A: Use `CanActivate`, `CanDeactivate`, `Resolve`, or `CanLoad` interfaces to control access to routes.

**Q8: What is lazy loading and how is it implemented?**\
A: Load modules on-demand using Angular’s Router. Define modules with `loadChildren` in the route config.

**Q9: What are lifecycle hooks in Angular?**\
A: Examples: `ngOnInit`, `ngOnChanges`, `ngAfterViewInit`, `ngOnDestroy` for component state and DOM lifecycle handling.

**Q10: How do you write unit tests for Angular components?**\
A: Use Jasmine + TestBed. Mock dependencies, test logic and UI binding using spies and fixture debug elements.

### Hands-On Tasks:

- ✅ Create a dynamic form using ReactiveFormsModule
- ✅ Use BehaviorSubject in a shared service
- ✅ Build a lazy-loaded module with a CanActivate guard
- ✅ Handle HTTP errors with interceptors
- ✅ Test components and services using Jasmine/Karma

---
# 🧪 Hands-On Tasks: Angular

This section guides you through essential Angular features using Reactive Forms, HTTP, routing, testing, and more.

## 🔹 Task 1: Create a Dynamic Form using ReactiveFormsModule

1. **Generate Module + Component**
   ```bash
   ng generate module product --routing
   ng generate component product/product-form
   ```

2. **Set up Reactive Form** (`product-form.component.ts`)
   ```ts
   import { FormBuilder, FormGroup, Validators } from '@angular/forms';

   export class ProductFormComponent {
     productForm: FormGroup;

     constructor(private fb: FormBuilder) {
       this.productForm = this.fb.group({
         name: ['', Validators.required],
         price: [0, [Validators.required, Validators.min(1)]],
         inStock: [true]
       });
     }

     onSubmit() {
       console.log(this.productForm.value);
     }
   }
   ```

3. **Add ReactiveFormsModule** to `AppModule`
   ```ts
   import { ReactiveFormsModule } from '@angular/forms';
   @NgModule({
     imports: [ReactiveFormsModule]
   })
   ```

---

## 🔹 Task 2: Use BehaviorSubject in a Shared Service

1. **Create Shared Service**
   ```bash
   ng generate service shared/product
   ```

2. **Implement BehaviorSubject** (`product.service.ts`)
   ```ts
   private productList = new BehaviorSubject<Product[]>([]);
   public products$ = this.productList.asObservable();

   addProduct(product: Product) {
     const current = this.productList.value;
     this.productList.next([...current, product]);
   }
   ```

3. **Subscribe from Component**
   ```ts
   this.productService.products$.subscribe(products => {
     this.allProducts = products;
   });
   ```

---

## 🔹 Task 3: Build a Lazy-Loaded Module with Route Guard

1. **Create Module**
   ```bash
   ng generate module dashboard --route dashboard --module app.module
   ```

2. **Add `CanActivate` Guard**
   ```bash
   ng generate guard auth/auth
   ```

3. **Implement Guard Logic** (`auth.guard.ts`)
   ```ts
   canActivate(): boolean {
     return this.authService.isLoggedIn();
   }
   ```

4. **Protect Route in `app-routing.module.ts`**
   ```ts
   {
     path: 'dashboard',
     loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule),
     canActivate: [AuthGuard]
   }
   ```

---

## 🔹 Task 4: Handle HTTP Errors with Interceptors

1. **Generate Interceptor**
   ```bash
   ng generate interceptor core/http-error
   ```

2. **Create Global Error Handler** (`http-error.interceptor.ts`)
   ```ts
   intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
     return next.handle(req).pipe(
       catchError((error: HttpErrorResponse) => {
         alert('An error occurred: ' + error.message);
         return throwError(() => error);
       })
     );
   }
   ```

3. **Register in `AppModule`**
   ```ts
   providers: [
     {
       provide: HTTP_INTERCEPTORS,
       useClass: HttpErrorInterceptor,
       multi: true
     }
   ]
   ```

---

## 🔹 Task 5: Test Components and Services Using Jasmine/Karma

1. **Basic Component Test**
   ```ts
   it('should create the component', () => {
     const fixture = TestBed.createComponent(ProductFormComponent);
     const component = fixture.componentInstance;
     expect(component).toBeTruthy();
   });
   ```

2. **Mocking a Service**
   ```ts
   beforeEach(() => {
     const spy = jasmine.createSpyObj('ProductService', ['addProduct']);
     TestBed.configureTestingModule({
       declarations: [ProductFormComponent],
       providers: [{ provide: ProductService, useValue: spy }]
     });
   });
   ```

3. **Testing Form Validation**
   ```ts
   it('form invalid when empty', () => {
     expect(component.productForm.valid).toBeFalsy();
   });
   ```

---

✅ **Next Steps**
- Add `FormArray` for dynamic field groups  
- Use `ngx-toastr` for error/success notifications  
- Explore component communication via `@Input`/`@Output`  
- Add E2E tests with Cypress or Protractor  
- Apply OnPush strategy for performance


---

## 4. React + TypeScript (2 Hours)

### Expert Interview Questions + Answers

**Q1: What is React and what are its core principles?**\
A: React is a JavaScript library for building user interfaces. It uses a virtual DOM, component-based architecture, and unidirectional data flow.

**Q2: What are functional components and how do they differ from class components?**\
A: Functional components are simpler, stateless (before hooks), and use hooks for logic. Class components use lifecycle methods and `this` binding.

**Q3: What are hooks and why are they important?**\
A: Hooks allow function components to manage state and side effects. Examples: `useState`, `useEffect`, `useRef`, `useMemo`, `useCallback`.

**Q4: What is the useEffect hook and how is it different from useLayoutEffect?**\
A: `useEffect` runs asynchronously after render. `useLayoutEffect` runs synchronously after DOM mutations — useful for measuring layout.

**Q5: What is prop drilling and how can it be avoided?**\
A: Prop drilling is passing props through many levels. Avoid it using Context API or state management libraries like Redux, Zustand.

**Q6: What is the Context API and how do you use it?**\
A: Context provides a way to share values like theme or auth across the component tree without prop drilling.

**Q7: What are keys in React and why are they important in lists?**\
A: Keys help React identify which items changed. Keys must be unique and stable (not array index).

**Q8: What is React Router and how does routing work in React apps?**\
A: React Router is a routing library. It uses `<BrowserRouter>`, `<Route>`, and `<Link>` to manage views.

**Q9: How does code splitting work in React?**\
A: React uses `React.lazy()` and `Suspense` for on-demand loading of components, improving performance.

**Q10: How do you test React components?**\
A: Use libraries like Jest and React Testing Library. Test user interaction, component state, and rendering logic.

### Hands-On Tasks:

- ✅ Build a simple login form with `useState` and `useEffect`
- ✅ Implement global auth state with Context API
- ✅ Code split dashboard components with `React.lazy()`
- ✅ Add unit tests for components using RTL + Jest
- ✅ Create reusable form field components with props + TypeScript types

---
# 🧪 Hands-On Tasks: React + TypeScript

This section walks through real-world tasks to practice functional components, hooks, state management, code splitting, and testing in React with TypeScript.

## 🔹 Task 1: Build a Simple Login Form with `useState` and `useEffect`

1. **Create Login Form Component**
   ```tsx
   import { useState, useEffect } from 'react';

   export default function LoginForm() {
     const [email, setEmail] = useState('');
     const [password, setPassword] = useState('');
     const [submitted, setSubmitted] = useState(false);

     useEffect(() => {
       if (submitted) {
         console.log('Form submitted!');
       }
     }, [submitted]);

     const handleSubmit = (e: React.FormEvent) => {
       e.preventDefault();
       setSubmitted(true);
     };

     return (
       <form onSubmit={handleSubmit}>
         <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
         <input type="password" value={password} onChange={e => setPassword(e.target.value)} />
         <button type="submit">Login</button>
       </form>
     );
   }
   ```

---

## 🔹 Task 2: Implement Global Auth State with Context API

1. **Create Auth Context**
   ```tsx
   import { createContext, useContext, useState } from 'react';

   interface AuthContextType {
     isAuthenticated: boolean;
     login: () => void;
     logout: () => void;
   }

   const AuthContext = createContext<AuthContextType | undefined>(undefined);

   export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
     const [isAuthenticated, setAuth] = useState(false);

     const login = () => setAuth(true);
     const logout = () => setAuth(false);

     return (
       <AuthContext.Provider value={{ isAuthenticated, login, logout }}>
         {children}
       </AuthContext.Provider>
     );
   };

   export const useAuth = () => useContext(AuthContext)!;
   ```

2. **Wrap App with Provider**
   ```tsx
   <AuthProvider>
     <App />
   </AuthProvider>
   ```

3. **Use in Components**
   ```tsx
   const { isAuthenticated, login } = useAuth();
   ```

---

## 🔹 Task 3: Code Split Dashboard Components with `React.lazy()`

1. **Lazy Load Component**
   ```tsx
   import React, { Suspense, lazy } from 'react';

   const Dashboard = lazy(() => import('./Dashboard'));

   export default function Home() {
     return (
       <Suspense fallback={<div>Loading...</div>}>
         <Dashboard />
       </Suspense>
     );
   }
   ```

2. **Split Routes Dynamically**
   ```tsx
   const Settings = lazy(() => import('./Settings'));
   ```

---

## 🔹 Task 4: Add Unit Tests with React Testing Library + Jest

1. **Install Libraries**
   ```bash
   npm install --save-dev @testing-library/react @testing-library/jest-dom jest
   ```

2. **Basic Test Example**
   ```tsx
   import { render, screen } from '@testing-library/react';
   import LoginForm from './LoginForm';

   test('renders login form', () => {
     render(<LoginForm />);
     expect(screen.getByText(/Login/i)).toBeInTheDocument();
   });
   ```

3. **Simulate User Input**
   ```tsx
   import userEvent from '@testing-library/user-event';

   test('submits form', () => {
     render(<LoginForm />);
     userEvent.type(screen.getByRole('textbox', { name: /email/i }), 'test@example.com');
     userEvent.click(screen.getByRole('button', { name: /login/i }));
   });
   ```

---

## 🔹 Task 5: Create Reusable Form Field Components

1. **FormField Component** (`FormField.tsx`)
   ```tsx
   interface FormFieldProps {
     label: string;
     type: string;
     value: string;
     onChange: (val: string) => void;
   }

   const FormField = ({ label, type, value, onChange }: FormFieldProps) => (
     <label>
       {label}
       <input type={type} value={value} onChange={e => onChange(e.target.value)} />
     </label>
   );

   export default FormField;
   ```

2. **Use in Parent Component**
   ```tsx
   <FormField label="Email" type="email" value={email} onChange={setEmail} />
   ```

---

✅ **Next Steps**
- Add routing with `react-router-dom`  
- Use `useReducer` for complex state  
- Explore `Zustand` or `Redux Toolkit`  
- Implement role-based access with Context  
- Write integration tests with MSW (Mock Service Worker)

---

## 5. TypeScript + Architecture (1 Hour)

### Expert Interview Questions + Answers

**Q1: What are utility types in TypeScript and how do you use them?**\
A: Utility types like `Partial<T>`, `Pick<T, K>`, `Omit<T, K>`, and `Record<K, T>` allow manipulation of existing types to simplify code reuse.

**Q2: What are generics and how are they useful?**\
A: Generics allow you to write functions or classes that can work with any type. Example: `function identity<T>(arg: T): T { return arg; }`

**Q3: What is structural typing and how does it compare to nominal typing?**\
A: Structural typing checks compatibility based on shape, not explicit inheritance or name. Nominal typing (e.g., Java) requires declarations to match.

**Q4: How do mapped types work in TypeScript?**\
A: Mapped types transform properties of another type. Example: `type Readonly<T> = { readonly [P in keyof T]: T[P] }`

**Q5: What are conditional types?**\
A: Conditional types return different results based on type inference. Example: `type IsString<T> = T extends string ? true : false;`

**Q6: How do you define a type-safe API response structure?**\
A: Use generics to enforce consistent responses. Example: `interface ApiResponse<T> { data: T; success: boolean; error?: string; }`

**Q7: How would you enforce immutability in TypeScript?**\
A: Use `readonly` properties or utility types like `Readonly<T>` to ensure values cannot be reassigned.

**Q8: How can interfaces and type aliases be composed or extended?**\
A: Interfaces support extension via `extends`; type aliases use intersection types: `type UserDetails = BasicInfo & AddressInfo;`

**Q9: What patterns do you use for scalable TypeScript codebases?**\
A: Modularization, shared utility types, typed API contracts, context-based state management, and isolated business logic.

**Q10: How does TypeScript improve large-scale application development?**\
A: It catches type errors at compile time, improves IDE support, enforces documentation through interfaces, and helps refactor safely.

### Hands-On Tasks:

- ✅ Create reusable generic utility types (e.g., `Paginated<T>`)
- ✅ Refactor a JS service layer into fully typed TypeScript
- ✅ Implement a type-safe form with validation and errors
- ✅ Use discriminated unions for API responses
- ✅ Design a scalable folder and file structure with TS modules

---
# 🧪 Hands-On Tasks: React + TypeScript

This section walks through real-world tasks to practice functional components, hooks, state management, code splitting, and testing in React with TypeScript.

## 🔹 Task 1: Build a Simple Login Form with `useState` and `useEffect`

1. **Create LoginForm Component**
   ```tsx
   import { useState, useEffect } from 'react';

   export default function LoginForm() {
     const [email, setEmail] = useState('');
     const [password, setPassword] = useState('');
     const [submitted, setSubmitted] = useState(false);

     useEffect(() => {
       if (submitted) {
         console.log('Form submitted!');
       }
     }, [submitted]);

     const handleSubmit = (e: React.FormEvent) => {
       e.preventDefault();
       setSubmitted(true);
     };

     return (
       <form onSubmit={handleSubmit}>
         <input type="email" value={email} onChange={e => setEmail(e.target.value)} />
         <input type="password" value={password} onChange={e => setPassword(e.target.value)} />
         <button type="submit">Login</button>
       </form>
     );
   }
   ```

---

## 🔹 Task 2: Implement Global Auth State with Context API

1. **Create Auth Context**
   ```tsx
   import { createContext, useContext, useState } from 'react';

   interface AuthContextType {
     isAuthenticated: boolean;
     login: () => void;
     logout: () => void;
   }

   const AuthContext = createContext<AuthContextType | undefined>(undefined);

   export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
     const [isAuthenticated, setAuth] = useState(false);

     const login = () => setAuth(true);
     const logout = () => setAuth(false);

     return (
       <AuthContext.Provider value={{ isAuthenticated, login, logout }}>
         {children}
       </AuthContext.Provider>
     );
   };

   export const useAuth = () => useContext(AuthContext)!;
   ```

2. **Wrap App with Provider**
   ```tsx
   <AuthProvider>
     <App />
   </AuthProvider>
   ```

3. **Use Context in Component**
   ```tsx
   const { isAuthenticated, login } = useAuth();
   ```

---

## 🔹 Task 3: Code Split Dashboard Components with `React.lazy()`

1. **Lazy Load Component**
   ```tsx
   import React, { Suspense, lazy } from 'react';

   const Dashboard = lazy(() => import('./Dashboard'));

   export default function Home() {
     return (
       <Suspense fallback={<div>Loading...</div>}>
         <Dashboard />
       </Suspense>
     );
   }
   ```

2. **Split Additional Routes**
   ```tsx
   const Settings = lazy(() => import('./Settings'));
   ```

---

## 🔹 Task 4: Add Unit Tests with React Testing Library + Jest

1. **Install Testing Libraries**
   ```bash
   npm install --save-dev @testing-library/react @testing-library/jest-dom @testing-library/user-event jest
   ```

2. **Write a Basic Test**
   ```tsx
   import { render, screen } from '@testing-library/react';
   import LoginForm from './LoginForm';

   test('renders login button', () => {
     render(<LoginForm />);
     expect(screen.getByText(/login/i)).toBeInTheDocument();
   });
   ```

3. **Simulate User Input**
   ```tsx
   import userEvent from '@testing-library/user-event';

   test('submits the form', () => {
     render(<LoginForm />);
     userEvent.type(screen.getByRole('textbox', { name: /email/i }), 'test@example.com');
     userEvent.click(screen.getByRole('button', { name: /login/i }));
   });
   ```

---

## 🔹 Task 5: Create Reusable Form Field Components with Props + TS Types

1. **FormField Component** (`FormField.tsx`)
   ```tsx
   interface FormFieldProps {
     label: string;
     type: string;
     value: string;
     onChange: (val: string) => void;
   }

   const FormField = ({ label, type, value, onChange }: FormFieldProps) => (
     <label>
       {label}
       <input type={type} value={value} onChange={e => onChange(e.target.value)} />
     </label>
   );

   export default FormField;
   ```

2. **Use It in a Form**
   ```tsx
   <FormField label="Email" type="email" value={email} onChange={setEmail} />
   ```

---

✅ **Next Steps**
- Add routing using `react-router-dom`  
- Use `useReducer` for complex form state  
- Store session in `localStorage`  
- Add loading spinners with `Suspense`  
- Write integration tests with MSW

---

## 6. System Design & DevOps (1.5 Hours)

### System Design & DevOps Interview Questions + Answers

**Q1: What is the difference between monolithic and microservices architecture?**\
A: Monoliths are a single deployable unit, simpler to build but harder to scale. Microservices split features into independent services that can be developed, deployed, and scaled separately.

**Q2: What are the tradeoffs of using microservices?**\
A: Pros: scalability, fault tolerance, team autonomy. Cons: complex DevOps, inter-service communication, eventual consistency.

**Q3: How would you design a high-availability booking system?**\
A: Use microservices with a gateway, load balancer, Redis for caching, SQL for consistency, NoSQL for scale, queues for event handling, retries and monitoring.

**Q4: What is eventual consistency and when is it acceptable?**\
A: Eventual consistency ensures that data will be consistent eventually. Acceptable in distributed systems like e-commerce carts or booking systems.

**Q5: How do you use queues in system design?**\
A: Queues decouple producers/consumers, absorb spikes in load. Example: RabbitMQ, Azure Service Bus, AWS SQS.

**Q6: What’s the difference between REST and GraphQL or gRPC?**\
A: REST uses structured endpoints, GraphQL is flexible and client-driven. gRPC is efficient and fast using protocol buffers.

**Q7: How do you monitor distributed applications?**\
A: Use centralized logging (ELK), distributed tracing (Jaeger, Zipkin), metrics dashboards (Prometheus, Grafana), and platform tools like Azure Monitor or AWS CloudWatch.

**Q8: How would you implement CI/CD for a .NET Core app?**\
A: Use GitHub Actions, Azure DevOps, or Jenkins to run tests, build Docker containers, push to registry, and deploy to cloud infrastructure.

**Q9: How do you handle secrets and environment configuration?**\
A: Use secret managers (Azure Key Vault, AWS Secrets Manager), inject configs through environment variables or secure vaults.

**Q10: What are some DevOps anti-patterns to avoid?**\
A: Manual deployments, hard-coded secrets, no rollback plans, lack of monitoring, tight coupling between infra and code.

### Hands-On Tasks:

- ✅ Diagram a scalable architecture (gateway, services, DB, queue, cache)
- ✅ Setup a GitHub Actions workflow for CI/CD
- ✅ Use Docker to containerize a .NET API and deploy it
- ✅ Design a fault-tolerant retry mechanism with exponential backoff
- ✅ Set up log aggregation and tracing with middleware + cloud monitoring

---
# 🧪 Hands-On Tasks: System Design & DevOps

This section focuses on designing scalable systems, building CI/CD pipelines, working with queues, Docker, and production-grade observability.

## 🔹 Task 1: Diagram a Scalable Architecture (Booking System)

1. **Draw or describe this architecture** (use Excalidraw, draw.io, or Miro):
   ```
   [Client App] 
       ↓
   [API Gateway]
       ↓
   [Microservices: Booking, Users, Notifications]
       ↓
   [Databases: SQL Server (Booking), NoSQL (Notifications)]
       ↘
   [Redis Cache] — [RabbitMQ Queue] — [Email Worker]
   ```

2. **Design Components**
   - API Gateway (e.g., NGINX, Azure API Management)
   - Microservices with health endpoints (`/health`)
   - Asynchronous communication via message queues
   - Redis for session and caching layer
   - Horizontal scaling with load balancers

---

## 🔹 Task 2: Setup GitHub Actions Workflow for CI/CD

1. **Create `.github/workflows/dotnet.yml`**
   ```yaml
   name: .NET CI/CD

   on:
     push:
       branches: [main]

   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest

       steps:
         - uses: actions/checkout@v3
         - name: Setup .NET
           uses: actions/setup-dotnet@v3
           with:
             dotnet-version: '7.0.x'
         - name: Restore Dependencies
           run: dotnet restore
         - name: Build
           run: dotnet build --no-restore
         - name: Test
           run: dotnet test --no-build --verbosity normal
         - name: Publish to Azure Web App
           uses: azure/webapps-deploy@v2
           with:
             app-name: your-app-name
             publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
             package: ./bin/Release/net7.0/publish
   ```

---

## 🔹 Task 3: Containerize a .NET API with Docker

1. **Create `Dockerfile`**
   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
   WORKDIR /app
   EXPOSE 80

   FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
   WORKDIR /src
   COPY . .
   RUN dotnet publish -c Release -o /app/publish

   FROM base AS final
   WORKDIR /app
   COPY --from=build /app/publish .
   ENTRYPOINT ["dotnet", "YourApp.dll"]
   ```

2. **Run Locally**
   ```bash
   docker build -t myapp .
   docker run -p 8080:80 myapp
   ```

3. **Push to Docker Hub (Optional)**
   ```bash
   docker tag myapp yourdockerhub/myapp
   docker push yourdockerhub/myapp
   ```

---

## 🔹 Task 4: Design Fault-Tolerant Retry Mechanism with Exponential Backoff

1. **Sample Pattern**
   ```csharp
   public async Task<T> RetryAsync<T>(Func<Task<T>> action, int maxRetries = 3) {
       int delay = 1000;
       for (int i = 0; i < maxRetries; i++) {
           try {
               return await action();
           } catch {
               await Task.Delay(delay);
               delay *= 2;
           }
       }
       throw new Exception("Operation failed after retries");
   }
   ```

2. **Use Case Examples**
   - Retrying external payment API calls
   - Re-sending email notifications
   - Re-processing failed queue messages

---

## 🔹 Task 5: Setup Centralized Logging & Tracing

1. **Logging with Serilog in .NET**
   ```csharp
   Log.Logger = new LoggerConfiguration()
       .Enrich.FromLogContext()
       .WriteTo.Console()
       .WriteTo.File("logs/log.txt", rollingInterval: RollingInterval.Day)
       .CreateLogger();

   builder.Host.UseSerilog();
   ```

2. **Tracing with OpenTelemetry (Optional)**
   ```bash
   dotnet add package OpenTelemetry.Exporter.Console
   dotnet add package OpenTelemetry.Extensions.Hosting
   ```

   ```csharp
   builder.Services.AddOpenTelemetry()
       .WithTracing(tracing => tracing
           .AddAspNetCoreInstrumentation()
           .AddHttpClientInstrumentation()
           .AddConsoleExporter());
   ```

3. **Cloud Tools to Explore**
   - Azure Monitor (App Insights, Log Analytics)
   - AWS CloudWatch
   - Elastic + Kibana (ELK Stack)
   - Grafana + Prometheus

---

✅ **Next Steps**
- Use Helm or Bicep to deploy Docker containers to AKS/ECS  
- Integrate Sentry or AppInsights for error tracking  
- Use Azure Key Vault or AWS Secrets Manager for secrets  
- Automate blue-green deployments with GitHub Actions  
- Practice DR (Disaster Recovery) with database replication

---

