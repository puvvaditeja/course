# Angular Modules

## What are NgModules?

NgModules are containers for a cohesive block of code dedicated to an application domain, a workflow, or a closely related set of capabilities. They help organize an Angular application into cohesive blocks of functionality.

### Key Characteristics

- **Declarations**: Components, directives, and pipes that belong to the module
- **Imports**: Other modules whose exported classes are needed
- **Providers**: Services that the module contributes to the global collection
- **Exports**: Declarations that should be visible to other modules
- **Bootstrap**: The main application view (root component)

### Module Types

1. **Root Module**: The main module that bootstraps the application
2. **Feature Modules**: Organize code by features
3. **Shared Modules**: Contain commonly used directives, pipes, and components
4. **Core Module**: Singleton services and app-wide components
5. **Routing Modules**: Handle navigation

---

## NgModule Decorator

The `@NgModule` decorator marks a class as an Angular module and provides configuration metadata.

### Basic Structure

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { HeaderComponent } from './header/header.component';
import { DataService } from './services/data.service';

@NgModule({
  // Components, directives, and pipes that belong to this module
  declarations: [
    AppComponent,
    HeaderComponent
  ],

  // Other modules to import
  imports: [
    BrowserModule,
    FormsModule
  ],

  // Services to provide
  providers: [
    DataService
  ],

  // Components to make available to other modules
  exports: [
    HeaderComponent
  ],

  // Component to bootstrap (root module only)
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### NgModule Properties Explained

#### 1. Declarations

Components, directives, and pipes that belong to this module.

```typescript
@NgModule({
  declarations: [
    AppComponent,           // Components
    HighlightDirective,     // Directives
    CapitalizePipe         // Pipes
  ]
})
export class AppModule { }
```

**Rules:**
- Must declare each component, directive, and pipe in exactly one module
- Only declare items that belong to this module
- Don't re-declare items from other modules

#### 2. Imports

Other modules whose exported classes are needed by components in this module.

```typescript
@NgModule({
  imports: [
    BrowserModule,          // Browser-specific services
    FormsModule,            // Template-driven forms
    HttpClientModule,       // HTTP client
    RouterModule,           // Routing
    CustomFeatureModule     // Custom module
  ]
})
export class AppModule { }
```

#### 3. Providers

Services that become available for dependency injection.

```typescript
@NgModule({
  providers: [
    UserService,
    AuthService,
    { provide: API_URL, useValue: 'https://api.example.com' },
    { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
  ]
})
export class AppModule { }
```

#### 4. Exports

Components, directives, pipes, and modules that can be used by other modules.

```typescript
@NgModule({
  declarations: [
    SharedComponent,
    SharedDirective,
    SharedPipe
  ],
  exports: [
    SharedComponent,
    SharedDirective,
    SharedPipe,
    CommonModule,    // Re-export imported module
    FormsModule      // Re-export imported module
  ]
})
export class SharedModule { }
```

#### 5. Bootstrap

The root component that Angular creates and inserts into the index.html host page.

```typescript
@NgModule({
  bootstrap: [AppComponent]  // Only in root module
})
export class AppModule { }
```

---

## Root Module

The root module (typically `AppModule`) is the entry point of an Angular application. It bootstraps the application and is loaded when the application starts.

### Basic Root Module

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,        // Required for browser apps
    AppRoutingModule      // Routing configuration
  ],
  providers: [],
  bootstrap: [AppComponent]  // Bootstrap component
})
export class AppModule { }
```

### Root Module with Common Imports

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
import { HttpClientModule } from '@angular/common/http';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { CoreModule } from './core/core.module';
import { SharedModule } from './shared/shared.module';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    // Angular modules
    BrowserModule,
    BrowserAnimationsModule,
    HttpClientModule,
    FormsModule,
    ReactiveFormsModule,

    // Custom modules
    CoreModule,
    SharedModule,

    // Routing (should be last)
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Bootstrapping the Application

```typescript
// main.ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule } from './app/app.module';

platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.error(err));
```

### Root Module Best Practices

1. **Keep it lean**: Move feature-specific code to feature modules
2. **Import order**: BrowserModule first, routing last
3. **One-time imports**: Only import BrowserModule in root module
4. **Singleton services**: Provide app-wide services here or use `providedIn: 'root'`

---

## Feature Modules

Feature modules organize code by application features, promoting better code organization and lazy loading capabilities.

### Creating a Feature Module

```bash
# Generate a feature module
ng generate module features/products
ng generate module features/users --routing
```

### Basic Feature Module

```typescript
// products/products.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ProductsRoutingModule } from './products-routing.module';

import { ProductListComponent } from './product-list/product-list.component';
import { ProductDetailComponent } from './product-detail/product-detail.component';
import { ProductFormComponent } from './product-form/product-form.component';

@NgModule({
  declarations: [
    ProductListComponent,
    ProductDetailComponent,
    ProductFormComponent
  ],
  imports: [
    CommonModule,              // Use instead of BrowserModule
    ProductsRoutingModule      // Feature routing
  ]
})
export class ProductsModule { }
```

### Feature Module with Dependencies

```typescript
// users/users.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { ReactiveFormsModule } from '@angular/forms';

import { UsersRoutingModule } from './users-routing.module';
import { SharedModule } from '../shared/shared.module';

import { UserListComponent } from './user-list/user-list.component';
import { UserDetailComponent } from './user-detail/user-detail.component';
import { UserService } from './services/user.service';

@NgModule({
  declarations: [
    UserListComponent,
    UserDetailComponent
  ],
  imports: [
    CommonModule,
    ReactiveFormsModule,
    SharedModule,           // Import shared functionality
    UsersRoutingModule
  ],
  providers: [
    UserService            // Feature-specific service
  ]
})
export class UsersModule { }
```

### Lazy Loading Feature Modules

```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

const routes: Routes = [
  {
    path: 'products',
    loadChildren: () => import('./features/products/products.module')
      .then(m => m.ProductsModule)
  },
  {
    path: 'users',
    loadChildren: () => import('./features/users/users.module')
      .then(m => m.UsersModule)
  },
  { path: '', redirectTo: '/products', pathMatch: 'full' }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### Feature Module Routing

```typescript
// products/products-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { ProductListComponent } from './product-list/product-list.component';
import { ProductDetailComponent } from './product-detail/product-detail.component';

const routes: Routes = [
  { path: '', component: ProductListComponent },
  { path: ':id', component: ProductDetailComponent }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],  // Use forChild for feature modules
  exports: [RouterModule]
})
export class ProductsRoutingModule { }
```

### Feature Module Benefits

1. **Organization**: Group related functionality
2. **Lazy Loading**: Load modules on demand
3. **Separation of Concerns**: Clear boundaries between features
4. **Reusability**: Modules can be reused across applications
5. **Team Scalability**: Different teams can work on different modules

---

## Shared Modules

Shared modules contain commonly used components, directives, and pipes that are used across multiple feature modules.

### Creating a Shared Module

```bash
ng generate module shared
```

### Basic Shared Module

```typescript
// shared/shared.module.ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

// Shared Components
import { LoadingSpinnerComponent } from './components/loading-spinner/loading-spinner.component';
import { ErrorMessageComponent } from './components/error-message/error-message.component';
import { ConfirmDialogComponent } from './components/confirm-dialog/confirm-dialog.component';

// Shared Directives
import { HighlightDirective } from './directives/highlight.directive';
import { AutofocusDirective } from './directives/autofocus.directive';

// Shared Pipes
import { TruncatePipe } from './pipes/truncate.pipe';
import { SafeHtmlPipe } from './pipes/safe-html.pipe';

@NgModule({
  declarations: [
    // Components
    LoadingSpinnerComponent,
    ErrorMessageComponent,
    ConfirmDialogComponent,

    // Directives
    HighlightDirective,
    AutofocusDirective,

    // Pipes
    TruncatePipe,
    SafeHtmlPipe
  ],
  imports: [
    CommonModule
  ],
  exports: [
    // Re-export common modules
    CommonModule,
    FormsModule,
    ReactiveFormsModule,

    // Export shared components
    LoadingSpinnerComponent,
    ErrorMessageComponent,
    ConfirmDialogComponent,

    // Export shared directives
    HighlightDirective,
    AutofocusDirective,

    // Export shared pipes
    TruncatePipe,
    SafeHtmlPipe
  ]
})
export class SharedModule { }
```

### Shared Module with Material Components

```typescript
// shared/material.module.ts
import { NgModule } from '@angular/core';

// Material imports
import { MatButtonModule } from '@angular/material/button';
import { MatInputModule } from '@angular/material/input';
import { MatCardModule } from '@angular/material/card';
import { MatTableModule } from '@angular/material/table';
import { MatPaginatorModule } from '@angular/material/paginator';
import { MatDialogModule } from '@angular/material/dialog';

const MATERIAL_MODULES = [
  MatButtonModule,
  MatInputModule,
  MatCardModule,
  MatTableModule,
  MatPaginatorModule,
  MatDialogModule
];

@NgModule({
  imports: MATERIAL_MODULES,
  exports: MATERIAL_MODULES
})
export class MaterialModule { }
```

```typescript
// shared/shared.module.ts (updated)
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/forms';
import { MaterialModule } from './material.module';

@NgModule({
  imports: [
    CommonModule,
    MaterialModule
  ],
  exports: [
    CommonModule,
    MaterialModule,
    // ... other exports
  ]
})
export class SharedModule { }
```

### Using Shared Module in Feature Modules

```typescript
// products/products.module.ts
import { NgModule } from '@angular/core';
import { SharedModule } from '../shared/shared.module';
import { ProductsRoutingModule } from './products-routing.module';
import { ProductListComponent } from './product-list/product-list.component';

@NgModule({
  declarations: [
    ProductListComponent
  ],
  imports: [
    SharedModule,           // Import once, get all shared functionality
    ProductsRoutingModule
  ]
})
export class ProductsModule { }
```

### Shared Module Best Practices

1. **No Providers**: Don't provide services in shared modules (causes multiple instances)
2. **Re-export Common Modules**: Export CommonModule, FormsModule, etc.
3. **Pure Components**: Include only presentational components
4. **Widely Used**: Only include truly shared functionality
5. **Import Everywhere**: Import in every feature module that needs it

### What NOT to Put in Shared Modules

```typescript
// DON'T: Provide services (they'll be duplicated)
@NgModule({
  providers: [UserService]  // BAD - creates multiple instances
})

// DON'T: Include feature-specific components
@NgModule({
  declarations: [ProductDetailComponent]  // BAD - belongs in feature module
})

// DON'T: Import BrowserModule
@NgModule({
  imports: [BrowserModule]  // BAD - only in root module
})
```

---

## Core Module (Optional Pattern)

The Core module is an optional pattern for singleton services and single-use components loaded once by the root module.

### Creating a Core Module

```typescript
// core/core.module.ts
import { NgModule, Optional, SkipSelf } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HttpClientModule } from '@angular/common/http';

// Services
import { AuthService } from './services/auth.service';
import { LoggerService } from './services/logger.service';
import { ErrorHandlerService } from './services/error-handler.service';

// Single-use components
import { HeaderComponent } from './components/header/header.component';
import { FooterComponent } from './components/footer/footer.component';
import { NavComponent } from './components/nav/nav.component';

// Guards
import { AuthGuard } from './guards/auth.guard';

// Interceptors
import { HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthInterceptor } from './interceptors/auth.interceptor';
import { ErrorInterceptor } from './interceptors/error.interceptor';

@NgModule({
  declarations: [
    HeaderComponent,
    FooterComponent,
    NavComponent
  ],
  imports: [
    CommonModule,
    HttpClientModule
  ],
  exports: [
    HeaderComponent,
    FooterComponent,
    NavComponent
  ],
  providers: [
    AuthService,
    LoggerService,
    ErrorHandlerService,
    AuthGuard,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    },
    {
      provide: HTTP_INTERCEPTORS,
      useClass: ErrorInterceptor,
      multi: true
    }
  ]
})
export class CoreModule {
  // Prevent reimport of CoreModule
  constructor(@Optional() @SkipSelf() parentModule: CoreModule) {
    if (parentModule) {
      throw new Error('CoreModule is already loaded. Import it only in AppModule');
    }
  }
}
```

### Using Core Module

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { CoreModule } from './core/core.module';
import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    CoreModule  // Import once in AppModule only
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

## Module Organization Patterns

### Recommended Project Structure

```
src/app/
├── core/
│   ├── services/
│   │   ├── auth.service.ts
│   │   ├── logger.service.ts
│   │   └── error-handler.service.ts
│   ├── guards/
│   │   └── auth.guard.ts
│   ├── interceptors/
│   │   ├── auth.interceptor.ts
│   │   └── error.interceptor.ts
│   ├── components/
│   │   ├── header/
│   │   ├── footer/
│   │   └── nav/
│   └── core.module.ts
│
├── shared/
│   ├── components/
│   │   ├── loading-spinner/
│   │   ├── error-message/
│   │   └── confirm-dialog/
│   ├── directives/
│   │   ├── highlight.directive.ts
│   │   └── autofocus.directive.ts
│   ├── pipes/
│   │   ├── truncate.pipe.ts
│   │   └── safe-html.pipe.ts
│   └── shared.module.ts
│
├── features/
│   ├── products/
│   │   ├── components/
│   │   ├── services/
│   │   ├── products-routing.module.ts
│   │   └── products.module.ts
│   ├── users/
│   │   ├── components/
│   │   ├── services/
│   │   ├── users-routing.module.ts
│   │   └── users.module.ts
│   └── dashboard/
│       ├── components/
│       ├── dashboard-routing.module.ts
│       └── dashboard.module.ts
│
├── app-routing.module.ts
├── app.component.ts
└── app.module.ts
```

### Module Import Order

```typescript
@NgModule({
  imports: [
    // 1. Angular modules
    BrowserModule,
    BrowserAnimationsModule,
    HttpClientModule,

    // 2. Third-party modules
    NgbModule,

    // 3. Core module (singleton services)
    CoreModule,

    // 4. Shared module
    SharedModule,

    // 5. Feature modules
    ProductsModule,
    UsersModule,

    // 6. Routing (always last)
    AppRoutingModule
  ]
})
export class AppModule { }
```

---

## forRoot and forChild Pattern

Used for modules that need different configurations in root vs. feature modules.

### forRoot Pattern

```typescript
// custom-config.module.ts
import { NgModule, ModuleWithProviders } from '@angular/core';
import { CommonModule } from '@angular/common';

export interface CustomConfig {
  apiUrl: string;
  timeout: number;
}

@NgModule({
  imports: [CommonModule]
})
export class CustomConfigModule {
  static forRoot(config: CustomConfig): ModuleWithProviders<CustomConfigModule> {
    return {
      ngModule: CustomConfigModule,
      providers: [
        { provide: 'API_CONFIG', useValue: config }
      ]
    };
  }

  static forChild(): ModuleWithProviders<CustomConfigModule> {
    return {
      ngModule: CustomConfigModule,
      providers: []  // No providers in child modules
    };
  }
}
```

### Using forRoot/forChild

```typescript
// app.module.ts (root module)
@NgModule({
  imports: [
    CustomConfigModule.forRoot({
      apiUrl: 'https://api.example.com',
      timeout: 5000
    })
  ]
})
export class AppModule { }

// feature.module.ts (feature module)
@NgModule({
  imports: [
    CustomConfigModule.forChild()
  ]
})
export class FeatureModule { }
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| NgModules | Containers for organizing related code |
| @NgModule | Decorator with declarations, imports, providers, exports |
| Root Module | Entry point, contains BrowserModule and bootstrap |
| Feature Modules | Organize by features, support lazy loading |
| Shared Modules | Reusable components, directives, pipes |
| Core Module | Singleton services and app-wide components |

## Next Topic

Continue to [Directives and Pipes](./04-directives-and-pipes.md) to learn about Angular directives and pipes.
