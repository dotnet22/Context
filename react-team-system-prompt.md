# ReactJS TypeScript Frontend Team Development Standards

## Core Technology Stack
- **Framework**: React 18+
- **Language**: TypeScript 5.x (strict mode enabled)
- **State Management**: Context API / Zustand / Redux Toolkit (project-specific)
- **Styling**: CSS Modules / Styled Components / Tailwind CSS
- **Build Tool**: Vite / Next.js
- **Package Manager**: npm / yarn / pnpm
- **Testing**: Jest, React Testing Library, Cypress/Playwright for E2E

## TypeScript Configuration Standards

### Required TSConfig Settings
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Code Organization & Architecture

### Project Structure
```
src/
├── components/          # Reusable UI components
│   ├── common/         # Generic components (Button, Input, etc.)
│   ├── layout/         # Layout components (Header, Footer, etc.)
│   └── features/       # Feature-specific components
├── pages/              # Page components (if not using Next.js)
├── hooks/              # Custom React hooks
├── services/           # API services and external integrations
├── store/              # State management
├── types/              # TypeScript type definitions
├── utils/              # Utility functions
├── styles/             # Global styles and theme
└── constants/          # Application constants
```

### Component Standards

#### Functional Components Only
- Use functional components with hooks
- No class components in new code
- Use React.FC sparingly (prefer explicit return types)

#### Component File Structure
```typescript
// ComponentName.tsx
import React from 'react';
import styles from './ComponentName.module.css';

interface ComponentNameProps {
  // Props interface
}

export const ComponentName: React.FC<ComponentNameProps> = ({ prop1, prop2 }) => {
  // Hooks first
  // State declarations
  // Effects
  // Event handlers
  // Helper functions
  
  return (
    <div className={styles.container}>
      {/* JSX */}
    </div>
  );
};
```

## TypeScript Best Practices

### Type Definitions
- **Always define interfaces for props**
- **Prefer interfaces over types for object shapes**
- **Use enums for fixed sets of values**
- **Export shared types from dedicated files**

```typescript
// ❌ Avoid
const Component = (props: any) => { };

// ✅ Preferred
interface ComponentProps {
  id: string;
  title: string;
  onClick?: () => void;
}

const Component: React.FC<ComponentProps> = ({ id, title, onClick }) => { };
```

### Strict Typing Rules
- **No `any` types** - use `unknown` if type is truly unknown
- **No implicit any** - all function parameters must be typed
- **Use discriminated unions for complex state**
- **Leverage TypeScript's type inference where appropriate**

```typescript
// Discriminated union example
type LoadingState = 
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: User[] }
  | { status: 'error'; error: string };
```

## React Patterns & Best Practices

### Hooks Rules
- **Custom hooks must start with "use"**
- **Extract complex logic into custom hooks**
- **Memoize expensive computations with useMemo**
- **Memoize callbacks passed to children with useCallback**

```typescript
// Custom hook example
const useDebounce = <T,>(value: T, delay: number): T => {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => clearTimeout(handler);
  }, [value, delay]);
  
  return debouncedValue;
};
```

### Performance Optimization
- **Use React.memo for expensive pure components**
- **Implement proper key strategies for lists**
- **Lazy load routes and heavy components**
- **Virtualize long lists (react-window/react-virtualized)**

### Error Boundaries
- **Implement error boundaries for feature sections**
- **Provide fallback UI for errors**
- **Log errors to monitoring service**

## API Integration Standards

### Service Layer Pattern
```typescript
// services/api/userService.ts
import { ApiClient } from './apiClient';
import { User, CreateUserDTO } from '@/types/user';

export class UserService {
  private static instance: UserService;
  private apiClient: ApiClient;
  
  private constructor() {
    this.apiClient = ApiClient.getInstance();
  }
  
  static getInstance(): UserService {
    if (!UserService.instance) {
      UserService.instance = new UserService();
    }
    return UserService.instance;
  }
  
  async getUsers(): Promise<User[]> {
    return this.apiClient.get<User[]>('/users');
  }
  
  async createUser(data: CreateUserDTO): Promise<User> {
    return this.apiClient.post<User>('/users', data);
  }
}
```

### Error Handling
- **Implement centralized error handling**
- **Type API errors properly**
- **Provide user-friendly error messages**
- **Use try-catch with async/await**

## State Management Guidelines

### Local State
- Use useState for component-specific state
- Use useReducer for complex local state logic

### Global State
- Context API for cross-cutting concerns (theme, auth)
- Zustand/Redux for complex application state
- Avoid prop drilling beyond 2 levels

### Form State
- Use controlled components
- Implement proper validation
- Consider form libraries (React Hook Form, Formik) for complex forms

## Testing Requirements

### Unit Testing
- **Minimum 80% code coverage**
- **Test all utility functions**
- **Test custom hooks with @testing-library/react-hooks**

### Component Testing
- **Test user interactions**
- **Test conditional rendering**
- **Mock external dependencies**
- **Use data-testid for reliable element selection**

```typescript
// Example test
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('should call onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    fireEvent.click(screen.getByText('Click me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

## Code Quality Standards

### Linting & Formatting
- **ESLint with recommended React & TypeScript rules**
- **Prettier for consistent formatting**
- **Pre-commit hooks with Husky**
- **Commit message convention (Conventional Commits)**

### Code Review Checklist
- [ ] TypeScript types are properly defined
- [ ] No use of `any` type
- [ ] Components follow naming conventions
- [ ] Props interfaces are exported when needed
- [ ] Error handling is implemented
- [ ] Loading states are handled
- [ ] Accessibility requirements met (ARIA labels, keyboard nav)
- [ ] Tests are included and passing
- [ ] Performance considerations addressed
- [ ] Code is DRY and follows SOLID principles

## Accessibility (a11y) Requirements
- **Semantic HTML elements**
- **ARIA labels for interactive elements**
- **Keyboard navigation support**
- **Focus management for modals/drawers**
- **Color contrast compliance (WCAG AA)**
- **Screen reader testing**

## Security Best Practices
- **Sanitize user inputs**
- **Validate data from APIs**
- **Use HTTPS for all API calls**
- **Implement CSP headers**
- **Avoid dangerouslySetInnerHTML**
- **Store sensitive data securely (never in localStorage for tokens)**
- **Implement proper authentication flow**

## Performance Metrics
- **Core Web Vitals targets:**
  - LCP (Largest Contentful Paint): < 2.5s
  - FID (First Input Delay): < 100ms
  - CLS (Cumulative Layout Shift): < 0.1
- **Bundle size monitoring**
- **Code splitting implementation**
- **Image optimization (WebP, lazy loading)**

## Documentation Requirements
- **Component documentation with prop descriptions**
- **README for complex features**
- **Inline comments for complex logic**
- **JSDoc for utility functions**
- **Storybook for component library**

## Git Workflow
- **Feature branch workflow**
- **Branch naming: feature/*, bugfix/*, hotfix/***
- **PR required for main branch**
- **Minimum 1 reviewer approval**
- **CI/CD pipeline must pass**
- **Squash commits on merge**

## Deployment Checklist
- [ ] All tests passing
- [ ] Build successful with no warnings
- [ ] Bundle size within limits
- [ ] Lighthouse score meets targets
- [ ] Security headers configured
- [ ] Error tracking configured
- [ ] Analytics implemented
- [ ] Feature flags reviewed
- [ ] Environment variables set
- [ ] CDN cache invalidated

## Continuous Improvement
- **Weekly code quality reviews**
- **Monthly dependency updates**
- **Quarterly performance audits**
- **Regular team knowledge sharing sessions**
- **Maintain living documentation**
- **Track and address technical debt**