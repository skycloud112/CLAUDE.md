# Commands

- `pnpm ts` (not `pnpm ts:check`) - Run TypeScript typechecker
- `pnpm test` - Run all tests

# General Guidelines
- Do not run build.
- when running tests, do not use more than 1 Task tool
- for use case tests, run all tests in \_tests folder by first using glob to find all spec files, then running with all file names in the command
- check impl of a method or function or return type of them to choose the best way to use them
- Update tests when changing use cases or gateways
- when I ask you to rename something, also rename: related file, folder, variable, prop, key, function, component names
- when I ask you to add a key to an object, check gateways, use cases, components, tests for usage of that object and update them accordingly
- when I ask you to apply a pattern, apply it to all relevant places
- when I ask you to refactor a variable to become instance variable, use the new field directly in all methods of the class insead of passing it as a parameter
- when I ask you to implement something in a react component, see if you can add the calculation in the use case class and let react component renders
- when have test failure, do not care if it is related to your current changes; just fix it.
- whenever possible, use 2 subagents.

# Important Docs

@docs/directInclude/coding-style.md
@docs/directInclude/react-components.md
@docs/directInclude/use-cases-and-actions.md
@docs/directInclude/gateway-styles.md
@docs/directInclude/testing-styles.md

# Reference When Need

- When working on hooks tests, refer to docs/referenceWhenNeed/hooks-testing.md
- When working on humble component testing, refer to docs/referenceWhenNeed/humble-component-testing.md
- When working on UUID URL param authentication, refer to docs/referenceWhenNeed/auth-uuid.md
- When working on username/password authentication, refer to docs/referenceWhenNeed/auth-credentials.md
- When working on loading guards or providers, refer to docs/referenceWhenNeed/loading-guards.md
