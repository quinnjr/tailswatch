<!-- converted from Cursor rules -->

## Cursor rule: `.cursor/rules/angular-best-practices.mdc`

_Angular best practices and conventions_

# Angular Best Practices

Follow these conventions when working with Angular code in this project.

## Component Architecture

1. **Standalone components**: Always use standalone components (`standalone: true`)
2. **Signals over BehaviorSubject**: Prefer Angular signals (`signal()`, `computed()`) over RxJS for component state
3. **Zoneless change detection**: This project uses `provideExperimentalZonelessChangeDetection()`
4. **Smart/dumb components**: Keep pages as smart components, extract reusable UI into dumb components

## File Organization

```
component-name/
  component-name.component.ts
  component-name.component.html
  component-name.component.scss
```

- Use separate template and style files (not inline)
- Group related components in feature folders
- Use barrel exports (`index.ts`) for component groups

## Templates

1. **Control flow**: Use new Angular control flow syntax (`@if`, `@for`, `@switch`) instead of `*ngIf`, `*ngFor`
2. **Track functions**: Always provide `track` in `@for` loops
3. **Semantic HTML**: Use proper HTML elements for accessibility
4. **No custom CSS**: Use only Tailwind utility classes, no custom SCSS styling

```html
<!-- Good -->
@if (isVisible()) {
  <div>Content</div>
}

@for (item of items; track item.id) {
  <div>{{ item.name }}</div>
}

<!-- Bad -->
<div *ngIf="isVisible">Content</div>
<div *ngFor="let item of items">{{ item.name }}</div>
```

## TypeScript

1. **Strict typing**: Always define interfaces for data structures
2. **Inject function**: Use `inject()` instead of constructor injection
3. **Readonly signals**: Mark signals as `readonly` when exposed publicly
4. **Computed for derived state**: Use `computed()` for derived values

```typescript
// Good
readonly items = signal<Item[]>([]);
readonly itemCount = computed(() => this.items().length);
private readonly service = inject(MyService);

// Bad
items = new BehaviorSubject<Item[]>([]);
constructor(private service: MyService) {}
```

## Services

1. **providedIn: 'root'**: Use tree-shakable providers
2. **Signals for state**: Use signals for reactive state management
3. **SSR safety**: Check for browser APIs before using them

```typescript
@Injectable({ providedIn: 'root' })
export class MyService {
  private readonly state = signal<State>(initialState);

  constructor() {
    if (typeof localStorage !== 'undefined') {
      // Safe to use localStorage
    }
  }
}
```

## Imports

1. **Import only what's needed**: Don't import entire modules
2. **RouterModule for routing**: Import `RouterModule` for `routerLink`
3. **CommonModule rarely needed**: With standalone components, import individual pipes/directives

## Performance

1. **OnPush not needed**: Signals + zoneless handles change detection efficiently
2. **Lazy load routes**: Use lazy loading for feature modules
3. **trackBy in loops**: Always use `track` in `@for`

## Testing

1. **Use Vitest**: This project uses Vitest, not Karma/Jasmine
2. **Component testing**: Use `@angular/core/testing` utilities
3. **Mock services**: Use dependency injection for testability


## Cursor rule: `.cursor/rules/commit-after-work.mdc`

_Always commit changes after completing work_

# Commit After Work

After completing a task or set of changes, always commit the work to git with a descriptive commit message.

## Guidelines

1. **When to commit**: After finishing any task, feature, bug fix, or meaningful set of changes
2. **Commit message format**: Use conventional commit format when appropriate:
   - `feat:` for new features
   - `fix:` for bug fixes
   - `docs:` for documentation changes
   - `style:` for formatting/styling changes
   - `refactor:` for code refactoring
   - `chore:` for maintenance tasks
3. **Commit scope**: Group related changes together in a single commit
4. **Don't forget**: Stage all relevant files before committing

## Example

```bash
git add -A
git commit -m "feat: add scrollable side navigation"
```


## Cursor rule: `.cursor/rules/deploy-docs-after-work.mdc`

# Deploy Documentation After Work

After completing a task or set of changes, always update and deploy the documentation website.

## Guidelines

1. **When to deploy**: After finishing any task that affects:
   - Theme files (`src/themes/*.scss`)
   - Theme service (`docs/src/app/services/theme.service.ts`)
   - Documentation components or pages
   - Package.json (version, exports, keywords)
   - README or CHANGELOG

2. **Deployment steps**:
   ```bash
   # 1. Verify docs build works
   cd docs && pnpm run build

   # 2. Merge develop to main
   cd .. && git checkout main
   git merge develop --no-ff -m "chore: merge develop - [description of changes]"

   # 3. Push to trigger GitHub Pages deployment
   git push origin main

   # 4. Switch back to develop
   git checkout develop
   ```

3. **What triggers deployment**: Pushing to `main` branch automatically triggers the GitHub Actions workflow that builds and deploys the docs to GitHub Pages.

4. **Live URL**: https://quinnjr.github.io/tailswatch

## Checklist Before Deploying

- [ ] All theme changes are committed
- [ ] Theme service is updated with new/modified themes
- [ ] angular.json includes any new theme bundles
- [ ] Docs build completes without errors
- [ ] Changes are merged to main branch


## Cursor rule: `.cursor/rules/git-flow-branching.mdc`

_Git-flow branching strategy_

# Git-Flow Branching Strategy

This project follows the git-flow branching model.

## Main Branches

- **`main`**: Production-ready code. Only receives merges from `release` and `hotfix` branches.
- **`develop`**: Integration branch for features. This is the default working branch.

## Supporting Branches

### Feature Branches
- **Naming**: `feature/<feature-name>` (e.g., `feature/add-dark-mode`)
- **Branch from**: `develop`
- **Merge into**: `develop`
- **Purpose**: New features and non-emergency fixes

```bash
# Create feature branch
git checkout develop
git checkout -b feature/my-feature

# When complete, merge back
git checkout develop
git merge --no-ff feature/my-feature
git branch -d feature/my-feature
```

### Release Branches
- **Naming**: `release/<version>` (e.g., `release/1.2.0`)
- **Branch from**: `develop`
- **Merge into**: `main` AND `develop`
- **Purpose**: Prepare for production release, version bumps, final fixes

```bash
# Create release branch
git checkout develop
git checkout -b release/1.2.0

# When ready, merge to main and develop
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Version 1.2.0"

git checkout develop
git merge --no-ff release/1.2.0
git branch -d release/1.2.0
```

### Hotfix Branches
- **Naming**: `hotfix/<issue>` (e.g., `hotfix/critical-bug`)
- **Branch from**: `main`
- **Merge into**: `main` AND `develop`
- **Purpose**: Emergency production fixes

```bash
# Create hotfix branch
git checkout main
git checkout -b hotfix/critical-bug

# When fixed, merge to main and develop
git checkout main
git merge --no-ff hotfix/critical-bug
git tag -a v1.2.1 -m "Hotfix 1.2.1"

git checkout develop
git merge --no-ff hotfix/critical-bug
git branch -d hotfix/critical-bug
```

## Commit Message Convention

Use conventional commits:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: Performance improvement
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples**:
```
feat(themes): add Dracula theme
fix(docs): correct theme selector dropdown alignment
docs: update README with installation instructions
chore: update dependencies
```

## Workflow Summary

1. Create feature branches from `develop`
2. Work on features, commit frequently
3. Merge completed features back to `develop`
4. When ready for release, create `release` branch from `develop`
5. Test and fix issues on release branch
6. Merge release to `main` and tag with version
7. Merge release back to `develop`
8. For production emergencies, create `hotfix` from `main`

