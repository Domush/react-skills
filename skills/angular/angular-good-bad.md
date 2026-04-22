# Angular Good / Bad Examples

This file shows concrete examples of modern Angular 21+ patterns and deprecated anti-patterns.

## Good: Separate files and small components

```typescript
// src/app/features/user-profile/user-profile.component.ts
import { Component, Input } from '@angular/core';
import { User } from '../../../shared/models/user.model';

@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
  styleUrls: ['./user-profile.component.css'],
  standalone: true,
})
export class UserProfileComponent {
  @Input() readonly user!: User;
}
```

```html
<!-- src/app/features/user-profile/user-profile.component.html -->
<article class="user-card">
  <h2>{{ user.name }}</h2>
  <p>{{ user.email }}</p>
</article>
```

```css
/* src/app/features/user-profile/user-profile.component.css */
.user-card {
  border: 1px solid var(--border-color);
  padding: 1rem;
  border-radius: 0.75rem;
}
```

## Bad: Monolithic component and inline template

```typescript
@Component({
  selector: 'app-user-profile',
  template: `
    <article><h2>{{ user.name }}</h2><p>{{ user.email }}</p></article>
  `,
  styles: ['article { padding: 1rem; }'],
})
export class UserProfileComponent {
  @Input() user: any;
}
```

## Good: Typed service with OOP structure

```typescript
export interface Article {
  id: string;
  title: string;
  content: string;
}

@Injectable({ providedIn: 'root' })
export class ArticleService {
  constructor(private readonly http: HttpClient) {}

  async loadArticle(articleId: string): Promise<Article> {
    try {
      return await this.http.get<Article>(`/api/articles/${articleId}`).toPromise();
    } catch (error) {
      throw new Error('Unable to load article');
    }
  }
}
```

## Bad: Any-typed service and error swallowing

```typescript
@Injectable()
export class ArticleService {
  constructor(private readonly http: HttpClient) {}

  async loadArticle(id: string): Promise<any> {
    return this.http.get(`/api/articles/${id}`).toPromise();
  }
}
```

## Good: No `as`, explicit type assignment

```typescript
const apiResponse: ArticleResponse = await this.http.get<ArticleResponse>('/api/articles').toPromise();
const article: Article = apiResponse.article;
```

## Bad: `as` type assertion

```typescript
const article = await this.http.get('/api/articles').toPromise() as Article;
```

## Good: Async/await, explicit handling, and pure helper methods

```typescript
async submitForm(): Promise<void> {
  try {
    this.submissionState = 'pending';
    await this.formService.save(this.form.value);
    this.submissionState = 'success';
  } catch (error) {
    this.handleError(error);
  }
}

private handleError(error: unknown): void {
  console.error(error);
  this.submissionState = 'error';
}
```

## Bad: Promise chains and missing error handling

```typescript
submitForm(): void {
  this.formService
    .save(this.form.value)
    .then(() => (this.submissionState = 'success'));
}
```

## Good: Router provider in Angular 21+

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes),
  ],
});
```

## Bad: Deprecated route provider

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideRoutes(routes),
  ],
});
```

## Good: Shared type alias instead of enum for status values

```typescript
export type LoadingStatus = 'idle' | 'pending' | 'success' | 'error';
```

## Bad: Enum for generic status values

```typescript
export enum LoadingStatus {
  Idle = 'idle',
  Pending = 'pending',
  Success = 'success',
  Error = 'error',
}
```

## Good: Strongly typed form creation

```typescript
export interface LoginForm {
  email: string;
  password: string;
}

this.loginForm = this.formBuilder.group<Readonly<LoginForm>>({
  email: ['', [Validators.required, Validators.email]],
  password: ['', [Validators.required]],
});
```

## Bad: Untyped form controls

```typescript
this.loginForm = this.formBuilder.group({
  email: ['', Validators.required],
  password: ['', Validators.required],
});
```
