## Available Codemods for Migration

MUI provides automated codemods to handle most breaking changes. Run these commands to automatically update your codebase:

### Core Material UI Codemods
```bash
# Complete preset (recommended) - handles most breaking changes
npx @mui/codemod v7.0.0/preset-safe <path/to/folder>

# Individual codemods for specific changes:
npx @mui/codemod v7.0.0/grid-props <path/to/folder>
npx @mui/codemod v7.0.0/input-label-size-normal-medium <path/to/folder>
npx @mui/codemod v7.0.0/lab-removed-components <path/to/folder>
```

### MUI X Codemods (v8.x series - separate versioning)
```bash
# MUI X preset-safe for v8.x 
npx @mui/x-codemod@latest v8.0.0/preset-safe <path>

# Component-specific MUI X codemods:
npx @mui/x-codemod@latest v8.0.0/data-grid/preset-safe <path>
npx @mui/x-codemod@latest v8.0.0/pickers/preset-safe <path>
```

## MUI X Specific Considerations

**Important**: MUI X packages use independent versioning and have their own migration paths:

### MUI X Package Versions
- `@mui/x-data-grid`, `@mui/x-data-grid-pro`, `@mui/x-data-grid-premium`
- `@mui/x-date-pickers`, `@mui/x-date-pickers-pro`
- `@mui/x-charts`, `@mui/x-tree-view`, `@mui/x-tree-view-pro`

These packages **do NOT** get updated to v7.0.0 when upgrading Material UI to v7. They maintain their own version numbers.

### MUI X v7 Key Changes
- New accessible DOM structure for Date Pickers
- Data Grid performance improvements with refactored column headers
- Tree View split into SimpleTreeView and RichTreeView
- Charts and Tree View components now stable
- Enhanced Data Grid with column resizing on Community plan

### When Using MUI X with Material UI v7
```typescript
// ✅ Correct package.json for MUI v7 + MUI X
{
  "dependencies": {
    "@mui/material": "^7.0.0",
    "@mui/icons-material": "^7.0.0",
    "@mui/system": "^7.0.0",
    
    // MUI X keeps its own versioning
    "@mui/x-data-grid": "^7.19.0",
    "@mui/x-date-pickers": "^7.19.0",
    "@mui/x-charts": "^7.19.0"
  }
}
```

## Common Migration Pitfalls & Troubleshooting

### 1. Import Resolution Issues
**Problem**: Deep imports or modern bundle aliases breaking
```typescript
// ❌ Will break in v7
import createTheme from '@mui/material/styles/createTheme';
import { StyledEngineProvider } from '@mui/material';

// ✅ Correct v7 imports
import { createTheme } from '@mui/material/styles';
import { StyledEngineProvider } from '@mui/material/styles';
```

**Solution**: Remove all deep imports and bundle aliases from your webpack/vite config.

### 2. Grid Migration Issues
**Problem**: Using old Grid syntax instead of `size` prop
```typescript
// ❌ Old GridLegacy syntax - will break
<Grid item xs={12} sm={6}>

// ❌ Common mistake - mixing old and new syntax
<Grid xs={12} sm={6}>
```

**Solution**: Use proper `size` prop syntax:
```typescript
// ✅ Correct v7 Grid syntax
<Grid size={{ xs: 12, sm: 6 }}>
// or for single value:
<Grid size={6}>
```

**Problem**: Codemods don't handle wrapped or styled Grid components
```typescript
// ❌ Codemod won't fix these automatically
const StyledGrid = styled(Grid)({ /* styles */ });
const WrappedGrid = (props) => <Grid {...props} />;
```

**Solution**: Manually update wrapped/styled Grid components to use `size` prop syntax.

### 3. Theme Variables Performance Issues
**Problem**: Expecting theme object to change between light/dark modes
```typescript
// ❌ This won't work as expected in v7 with CSS variables
const theme = useTheme();
console.log(theme.palette.mode); // Always 'light' even in dark mode
```

**Solution**: Use `useColorScheme()` hook for mode detection and `theme.vars.*` for styling.

### 4. React Version Compatibility
**Problem**: Type errors with React 18 and below
```typescript
// Error: react-is version mismatch
```

**Solution**: Add react-is resolution to package.json:
```json
{
  "overrides": {
    "react-is": "^18.3.1" // Match your React version
  }
}
```

### 5. Lab Component Import Errors
**Problem**: Components not found after upgrade
```typescript
// ❌ Will break - components moved
import { Alert } from '@mui/lab';
```

**Solution**: Update imports to main package:
```typescript
// ✅ Correct import
import { Alert } from '@mui/material';
```

### 6. CSS Specificity Issues
**Problem**: Custom styles not applying due to CSS variables
**Solution**: Use CSS layers or update selectors to work with new CSS variable approach.

### 7. Slot Pattern Migration
**Problem**: Legacy component props not working
```typescript
// ❌ Old pattern
<Accordion TransitionComponent={CustomTransition} />
```

**Solution**: Always use standardized slot pattern:
```typescript
// ✅ New pattern
<Accordion slots={{ transition: CustomTransition }} />
```

### 8. Bundle Size Issues
**Problem**: Modern bundle aliases causing build failures
**Solution**: Remove all modern bundle aliases from your bundler configuration.

### 9. TypeScript Interface Changes
**Problem**: Theme augmentation interfaces not found
**Solution**: Update interface names and import paths to match v7 structure.

### 10. Testing Considerations
- Remove any tests that rely on removed `data-testid` attributes from icons
- Update tests expecting different theme behavior with CSS variables
- Verify prop spreading tests work with new slot pattern
- Check tests that depend on specific CSS class names that may have changed

## Pigment CSS Migration Path (Optional but Recommended)

MUI v7 works with the traditional Emotion/styled-components approach, but Pigment CSS is the future-oriented styling solution:

### Benefits of Migrating to Pigment CSS
- **Zero-runtime**: Styles extracted at build time, not runtime
- **React Server Components**: Full RSC compatibility
- **Bundle size**: Significant reduction in JavaScript bundle size
- **Performance**: No client-side style recalculations

### When to Consider Pigment CSS
- New projects starting with MUI v7
- Applications targeting React Server Components
- Performance-critical applications
- Projects planning for MUI's future direction

### Migration Approach
```typescript
// Traditional styling (current)
import { styled } from '@mui/material/styles';

const StyledBox = styled('div')(({ theme }) => ({
  color: theme.palette.primary.main,
}));

// Pigment CSS approach (future)
import { styled } from '@pigment-css/react';

const StyledBox = styled.div`
  color: ${({ theme }) => theme.colors.primary[500]};
`;
```

### Pigment CSS Setup
```bash
npm install @pigment-css/react
```

**Note**: Pigment CSS is opt-in for v7 but will likely become the default in future major versions. Consider migrating gradually for new components while keeping existing components on Emotion until you're ready for a full migration.

**Post-Migration Checklist:**
- [ ] Run all codemods
- [ ] Remove bundle aliases from config
- [ ] Update Grid syntax to use `size` prop
- [ ] Test theme switching functionality
- [ ] Verify custom styled components work
- [ ] Check TypeScript compilation
- [ ] Run full test suite
- [ ] Test in both light and dark modes
- [ ] Verify responsive Grid layouts
- [ ] Check console for deprecation warnings
- [ ] Test MUI X components separately
- [ ] Verify slot pattern usage## Available Codemods for Migration

MUI provides automated codemods to handle most breaking changes. Run these commands to automatically update your codebase:

### Core Material UI Codemods
```bash
# Complete preset (recommended) - handles most breaking changes
npx @mui/codemod v7.0.0/preset-safe <path/to/folder>

# Individual codemods:
npx @mui/codemod v7.0.0/grid-props <path/to/folder>
npx @mui/codemod v7.0.0/input-label-size-normal-medium <path/to/folder>
npx @mui/codemod v7.0.0/lab-removed-components <path/to/folder>
```

### MUI X Codemods (separate versioning)
```bash
# MUI X preset-safe
npx @mui/x-codemod@latest v7.0.0/preset-safe <path>

# Component-specific MUI X codemods:
npx @mui/x-codemod@latest v7.0.0/data-grid/preset-safe <path>
npx @mui/x-codemod@latest v7.0.0/pickers/preset-safe <path>
```

## MUI X Specific Considerations

**Important**: MUI X packages use independent versioning and have their own migration paths:

### MUI X Package Versions
- `@mui/x-data-grid`, `@mui/x-data-grid-pro`, `@mui/x-data-grid-premium`
- `@mui/x-date-pickers`, `@mui/x-date-pickers-pro`
- `@mui/x-charts`, `@mui/x-tree-view`, `@mui/x-tree-view-pro`

These packages **do NOT** get updated to v7.0.0 when upgrading Material UI to v7. They maintain their own version numbers.

### MUI X v7 Key Changes
- New accessible DOM structure for Date Pickers
- Data Grid performance improvements with refactored column headers
- Tree View split into SimpleTreeView and RichTreeView
- Charts and Tree View components now stable
- Enhanced Data Grid with column resizing on Community plan

### When Using MUI X with Material UI v7
```typescript
// ✅ Correct package.json for MUI v7 + MUI X
{
  "dependencies": {
    "@mui/material": "^7.0.0",
    "@mui/icons-material": "^7.0.0",
    "@mui/system": "^7.0.0",
    
    // MUI X keeps its own versioning
    "@mui/x-data-grid": "^7.19.0",
    "@mui/x-date-pickers": "^7.19.0",
    "@mui/x-charts": "^7.19.0"
  }
}
```

## Common Migration Pitfalls & Troubleshooting

### 1. Import Resolution Issues
**Problem**: Deep imports or modern bundle aliases breaking
```typescript
// ❌ Will break in v7
import createTheme from '@mui/material/styles/createTheme';# MUI v7 System Prompt for React/TypeScript Development

You are an expert React/TypeScript developer with comprehensive knowledge of Material UI v7 and its ecosystem. When generating React code that uses MUI, always apply the following v7-specific patterns and avoid deprecated v6 patterns.

## Core MUI v7 Principles

### Package Layout & Import Changes
- **NO DEEP IMPORTS**: Only single-level imports are allowed
  ```typescript
  // ❌ v6 style - NO LONGER WORKS
  import createTheme from '@mui/material/styles/createTheme';
  
  // ✅ v7 style - ALWAYS USE
  import { createTheme } from '@mui/material/styles';
  ```

- **Updated Package Versions**: Always use v7.0.0 for core packages:
  - `@mui/material@7.0.0`
  - `@mui/icons-material@7.0.0` 
  - `@mui/system@7.0.0`
  - `@mui/lab@7.0.0`
  - `@mui/material-nextjs@7.0.0`

### Standardized Slot Pattern (CRITICAL)
MUI v7 standardizes the slot customization pattern across ALL components. Always use `slots` and `slotProps` instead of legacy component-specific props:

```typescript
// ❌ v6 style - DEPRECATED
<Accordion 
  TransitionComponent={CustomTransition}
  TransitionProps={{ unmountOnExit: true }}
/>

// ✅ v7 style - ALWAYS USE
<Accordion 
  slots={{ transition: CustomTransition }}
  slotProps={{ transition: { unmountOnExit: true } }}
/>
```

**Key Slot Patterns:**
- Use `slots={{ slotName: Component }}` to replace slot components
- Use `slotProps={{ slotName: props }}` to pass props to slots
- Callback version: `slotProps={{ slotName: (ownerState) => ({ prop: value }) }}`

### Grid System Overhaul
- `Grid` component renamed to `GridLegacy`
- `Grid2` is now the main `Grid` component
- Always use the new Grid (formerly Grid2) for new projects:

```typescript
// ✅ v7 style - New Grid component
import { Grid } from '@mui/material';

<Grid xs={12} md={6}>
  Content
</Grid>
```

### CSS Layers Support
MUI v7 introduces CSS layers for better integration with modern tools like Tailwind CSS v4:

```typescript
// For Next.js App Router
import { AppRouterCacheProvider } from '@mui/material-nextjs/v15-appRouter';
import GlobalStyles from '@mui/material/GlobalStyles';

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <AppRouterCacheProvider options={{ enableCssLayer: true }}>
          <GlobalStyles styles="@layer theme,base,mui,components,utilities;" />
          <ThemeProvider theme={theme}>{children}</ThemeProvider>
        </AppRouterCacheProvider>
      </body>
    </html>
  );
}

// For Vite/React apps
import { StyledEngineProvider } from '@mui/material/styles';

<StyledEngineProvider enableCssLayer>
  <GlobalStyles styles="@layer theme,base,mui,components,utilities;" />
  {/* Your app */}
</StyledEngineProvider>
```

### Theme Variables & Performance
When using CSS variables with color schemes, the theme object no longer re-renders on mode changes for better performance:

```typescript
// ✅ v7 style - Use theme.vars for dynamic values
const Custom = styled('div')(({ theme }) => ({
  color: theme.vars.palette.text.primary,
  background: theme.vars.palette.primary.main,
}));

// For alpha/calculations, use CSS color-mix when possible
const AlphaExample = styled('div')(({ theme }) => ({
  color: `color-mix(in srgb, ${theme.vars.palette.text.primary}, transparent 50%)`,
}));

// If CSS approach not possible, access colorSchemes directly
const FallbackExample = styled('div')(({ theme }) => ({
  color: alpha(theme.colorSchemes.light.palette.text.primary, 0.5),
  ...theme.applyStyles('dark', {
    color: alpha(theme.colorSchemes.dark.palette.text.primary, 0.5),
  }),
}));
```

## Removed/Deprecated APIs (DO NOT USE)

### Removed Functions & Components
```typescript
// ❌ REMOVED - Use alternatives
createMuiTheme → createTheme
experimentalStyled → styled
Hidden, PigmentHidden → useMediaQuery or sx prop
onBackdropClick (Dialog/Modal) → onClose with reason parameter
```

### Dialog/Modal Changes
```typescript
// ✅ v7 style - Handle backdrop clicks through onClose
function Example() {
  const [open, setOpen] = React.useState(false);
  
  const handleClose = (event, reason) => {
    if (reason === 'backdropClick') {
      // Handle backdrop click
    }
    setOpen(false);
  };

  return (
    <Dialog open={open} onClose={handleClose}>
      {/* Content */}
    </Dialog>
  );
}
```

### InputLabel Size Standardization
```typescript
// ❌ v6 style
<InputLabel size="normal">Label</InputLabel>

// ✅ v7 style
<InputLabel size="medium">Label</InputLabel>
```

### Lab Components Migration
These components moved from `@mui/lab` to `@mui/material`:
- Alert, AlertTitle, Autocomplete, AvatarGroup, Pagination, PaginationItem
- Rating, Skeleton, SpeedDial, SpeedDialAction, SpeedDialIcon
- ToggleButton, ToggleButtonGroup, usePagination

```typescript
// ❌ v6 style
import Alert from '@mui/lab/Alert';

// ✅ v7 style  
import Alert from '@mui/material/Alert';
```

## TypeScript & React Version Support
- **TypeScript**: Minimum version 4.9+, but **TypeScript 5.x is fully supported** 
- **React**: Supports React 17.0.0+ through **React 19** (full React 19 support added)
- **react-is compatibility**: MUI v7 uses react-is@19
  - For React 19: No additional configuration needed
  - For React 18 and below: Add react-is resolution to match your React version:

```json
{
  "overrides": {
    "react-is": "^18.3.1"  // Match your React version
  }
}
```

```typescript
// TypeScript 5.x example with React 19
npm install @types/react@19 @types/react-dom@19
```

## MUI X Compatibility
- MUI X packages maintain separate versioning
- Date Pickers, Data Grid, Charts, Tree View use their own version numbers
- Always check MUI X migration guides separately

## Best Practices for v7

1. **Always use the standardized slot pattern** for component customization
2. **Prefer CSS layers** when integrating with other CSS frameworks
3. **Use theme.vars** for dynamic styling when CSS variables are enabled  
4. **Import from proper paths** - no deep imports beyond one level
5. **Handle theme mode changes** properly when using CSS variables
6. **Use the new Grid component** (formerly Grid2) for layout

## Example Component with v7 Patterns
```typescript
import React from 'react';
import {
  Accordion,
  AccordionSummary,
  AccordionDetails,
  Typography,
  Grid,
  Alert
} from '@mui/material';
import { styled } from '@mui/material/styles';
import ExpandMoreIcon from '@mui/icons-material/ExpandMore';

// Custom styled component using theme.vars
const CustomAccordion = styled(Accordion)(({ theme }) => ({
  backgroundColor: theme.vars.palette.background.paper,
  '&.Mui-expanded': {
    borderColor: theme.vars.palette.primary.main,
  }
}));

// Component using v7 patterns
const ExampleComponent = () => {
  return (
    <Grid container spacing={2}>
      <Grid size={{ xs: 12 }}>
        <Alert severity="info">This uses MUI v7 patterns</Alert>
      </Grid>
      <Grid size={{ xs: 12, md: 6 }}>
        <CustomAccordion
          slots={{
            transition: CustomTransition
          }}
          slotProps={{
            transition: { unmountOnExit: true }
          }}
        >
          <AccordionSummary expandIcon={<ExpandMoreIcon />}>
            <Typography>Accordion Title</Typography>
          </AccordionSummary>
          <AccordionDetails>
            <Typography>Content using v7 patterns</Typography>
          </AccordionDetails>
        </CustomAccordion>
      </Grid>
    </Grid>
  );
};
```

## When Generating Code
1. Always check if you're using deprecated patterns from v6
2. Use the standardized slot pattern for any component customization
3. Import correctly with single-level imports only
4. Consider CSS layers if integrating with other styling solutions
5. Use proper TypeScript typing for v7 APIs
6. Apply theme variables when using CSS variables feature

Remember: MUI v7 emphasizes consistency, modern ESM support, and standardized APIs. Always prioritize the new patterns over legacy approaches.