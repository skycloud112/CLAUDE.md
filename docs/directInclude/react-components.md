# React Components

## General
- Don't use `useMemo`, `useCallback`, or `React.memo`
- State management should be extracted to a separate hook
- Prefer small files.
- Do not add props that are not being used
- `import React from 'react';` is needed if a component is rendered in a test
- Never use plain HTML - use MUI components:
  - `<Box>` instead of `<div>`
  - prefer Stack over Box if flex container is needed
- Extend existing components with optional parameters - don't create wrapper/variant components. Add optional params: `onCreateSuccess?: (id: string) => void`

## Event Handlers
- Handlers or callbacks in components should be pure functions (with tests) that have hard-to-mock dependencies (such as useMutation callback and state, event value) passed to it

## Container vs Presentational
- Separate presentational and container components
- Container components call useMutation and useQuery and useSessionUser hooks, and pass data and callbacks to presentational components (named XYZView)
  - a XYZView component (and children) should not call any useMutation, useQuery, or useSessionUser hooks
- Loading states:
  - isFetching: should be handled in container components with LoadingBackdrop
  - isLoading: should pass to LoadingGuard

## Folder Structure
- If component has sub components, create a folder for it
- Folder name should match component name
- Sub components directly used by the main component in the folder are in "components" folder
- files used by multiple sub components of the main component should in the "shared" folder
- Example:
  ```
  MainComponent/
    MainComponent.tsx (main component)
    utils/
    shared/
      files used by both sub components of Tab1 and Tab2
    Tabs (or other sub domains)/
      Tab1.tsx
      Tab2.tsx
    components/
      utilA.ts - directly used by MainComponent
      utilB.tsx - directly used by MainComponent
      SubA.tsx - directly used by MainComponent
      SubB.tsx - directly used by MainComponent
  ```

## Dialog
- InfoIconButton and the Dialog component should be in the same component

## Next.js Pages
- Each page should have a page.tsx and a XXXPage.tsx
- page.tsx uses XXXPage.tsx
- page.tsx uses Next.js specific code
- XXXPage.tsx stores pure React components
