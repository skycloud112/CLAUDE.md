# Hooks Testing

- Hooks export only what tests need (e.g., `trigger`, `getValues`)
- Don't export unused form context methods (e.g., `control`, `watch`)
- Tests access form state via hook's exports, not wrapper
- Use `createSimpleFormWrapper` when needed
- Use real `initTranslation()` - don't mock `react-i18next`.
