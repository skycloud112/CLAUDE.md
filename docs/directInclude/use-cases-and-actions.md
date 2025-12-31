# Use Cases and Actions

## Request/Response
- Should be DTOs, or plain objects and primitives only (no `Date` objects, no Entity objects)

## Coding style
- Business logic in use case methods, not action functions
- Action functions only instantiate classes
- coordinate data between gateways and other services
- calculation should be in use case classes. UI components should be dumb as much as possible, just for rendering. use case return date in ISO string, UI does the formatting.
- all use cases, entities, gateway methods (real impl), UI pure function utils should have tests. 
- Dependency order: UI → actions → use case → entity. Reverse is not allowed
- prefer private methods over standalone functions

## File Naming
- Folder: `XXXUseCase/`
- Class: `XXXUseCase`
- Actions file: `XXXUseCaseActions`
- Action functions: `XXXUseCaseYYYAction`
- Keep action files in same folder as use case
- tests: in `_tests` folder inside use case folder (preferably). or alongside use case folder with `.spec.ts` suffix.

# React Query Hooks
- Wrapper hooks use `useQuery`/`useMutation` around actions
- No logic in wrappers - pass params directly to action
- Always call action (no `enabled` option)
- useQuery wrapper should return data with default value (`ids: data?.ids || []`), error, isLoading

## tests

- testing should be done at use case public interface level, not internal private methods level or internal helpers level.
- if functionalities is already covered in entity tests, in use case tests, just test the integration.
