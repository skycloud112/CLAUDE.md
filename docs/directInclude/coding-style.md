# Coding Style

## Core Principles

1. **Simplicity over cleverness** - Write code that's immediately understandable
2. **Leverage existing solutions** - Use standard libraries, don't reinvent
3. **Early returns** - Guard clauses over nested conditionals
4. **Match existing patterns** - Follow the file's conventions exactly

## General Principles
- Follow SOLID, DRY, YAGNI, KISS, TDD, Law of Demeter, CQRS principles
- prefer small functions or methods, single responsibility principle. try to keep each function smaller than 3-4 lines.
- When you need something (function, re-exports, types) from another file, export it. Do not create duplicated code.
- use ESNext syntax and features
- always use undefined, not use null
- do not write comments unless instructed. code should be self-explanatory.
- remove unused imports, variables, functions, types
- functions should be pure

## Entity File Export Rules
- Internal type files (e.g., `types/JobEvent.ts`) should only export their own types, not re-export types from sibling files
- Only the master entity file (e.g., `JobEvents.ts`) should re-export types for external and tests usage

## Classes
- should provide generic methods; not special methods

## Type Safety
- Type function parameters, return values, and variables
- Prefer compile-time type checking over runtime checks
- Use existing types over creating new types
- Use Enums over strings

## Function or Method Design
- Prefer object parameters over individual params list
- Make functions `async`. Do not use `Promise.resolve`
- Avoid passing null or undefined to functions
- name of params of function or props of component should be at the level of abstraction of the function or component

## Step-Down Rule
- Caller should be declared above callee in a source file
- High level function or components at the top
- Same rule applies to types. High level types should be at the top.

## Export Style
- Do not do `export {A}`, do `export const A = ...`
- Don't create `index.ts` or `index.tsx` files to re-export files in a folder

## shared file structure
- if a file is shared between folders or files, it is placed at the common ancestor folder