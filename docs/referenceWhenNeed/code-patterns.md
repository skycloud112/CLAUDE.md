# Code Patterns & Best Practices

## Frontend Patterns

### Forms & Input
- `inputMode=numeric` on Safari: Doesn't show keypad, just shows number stepper
- In `react-hook-form`, to update nested properties: Get whole object out, change it, then assign it back at the top level
- Since form provides default for new, just use fields to update entity without checking for undefined

### UI/UX
- Need "keep-content-when-loading" to make in-place editing look not jarring
- Keep the space while loading so that changes caused by content below don't cause layout shift
- If a div container for an image has extra space at the bottom, set the height based on image height dynamically

### Dialogs & Modals
- **Create/Update dialog**: Different dialogs for each wrapper with different title. Each content wrapper has different mutation method. Update one needs ID
- Action menu encapsulates mutations and shows loading on the action menu button

### Data Hooks
- Pattern: Data hooks handle success/error notification and invalidation

## Backend Patterns

### Date & Time
- `format()` can accept a string
- Show timezone ET string when it is formatted in server side

### Error Handling
- In production, server action errors will be omitted to client
- **New worker app error approach**: Throw error on unauthenticated, ignore `InvalidSessionError` in Datadog monitor. Client will receive error and should show it. If user refreshes, will redirect to login

### Architecture
- Having constructors receive interface dependencies allows for checking missing deps
- Can have multiple methods in one use case
- Add "UseCase" suffix to all use case files to make it easier for AI to identify
- DTOs interface should be generic to allow entity to change without affecting DTO
- Deviate from entity pattern in gateway is not worth the complexity that comes with customized response

## Code Quality

### Debugging & Development
- Make a bad solution first to make app compile, then think of better solutions
- When made a mistake, fix ASAP and apologize for what the mistake was (poor communication, taking away a feature that was needed) and say will fix ASAP

### Common Gotchas
- `entities.sort((a, b) => (a.order || 0) - (b.order || 0));` - Need `()` to evaluate correctly
- Just renaming file with only casing change - does not get tracked by git

## Tooling

### ESLint
- `Failed to load plugin 'only-warn' declared in '.eslintrc.js » @repo/eslint-config/next.js'` - Need to install eslint dependency at project root
- Can run eslint directly in terminal to debug issues. The error message is more helpful
- Can install eslint at root of monorepo to run it anywhere in packages
- If a rule is causing problems, can disable it

### Package Management
- `'@repo/apps-comp/QuoteChip'` ✓ correct - `'@repo/apps-comp'` ✗ wrong
- `./C` package export needs `@repo/pkg/C`
- When moving an app to root, update it in Vercel setting for root, and update pnpm yaml file

### Claude Code
- Can add delete button according to existing pattern easily. Give instruction to create use case, the button, the action and connect
- Used Claude Code to move actions to be together with use case files. Imports in moved files are not updated. Imports in consuming files are also not updated. Can fix imports after moved
- Claude Code can add eslint config file to all packages and delete old config files from all packages
- Claude Code Max is fast
- Can ask code agent to:
  - Clean up code
  - Make code look more professional

### Other Tools
- Install coding agents: Gemini by brew, Codex by npm
- **Photopea** - Create image clip: 2 layers. Below: shape. Above: image, right click → clipping mask

## Library-Specific

### Next.js
- To use `react-konva` in Next.js, use dynamic import to prevent server side importing Node.js canvas lib. Need to install canvas for build to pass

### React
- When scaling circles with an image, should scale width, height, border width so that offset scales correctly

### Google APIs
- `url` from Google autocomplete can be used for Google Maps
- `location` from Google autocomplete could be saved for coordinates
- Wrap Google API provider at page level so that all models with different requirements are all working

## Design Documents

Sections to include:
- Goal
- Page updates (with logic)
- Schema updates

## Philosophy

- Do what I like → Feel enjoyment → Transfer merit to be smarter → Do work with better quality and faster → More money
- If tracking time too diligently, will feel stress
