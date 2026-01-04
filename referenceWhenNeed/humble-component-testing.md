# Humble Component Testing Pattern

## Principle
- **Components**: For rendering only - no logic or state
- **Hooks**: Contain all logic and state, designed to be unit testable
- **Dependencies**: Hooks receive dependencies via props

## Testing Strategy
1. **Easy to mock in tests**: Use directly in the hook
   - Example: `react-hook-form` (`useFormContext`, `useController`)
   - These run easily in test environment without complex mocking

2. **Harder to mock in tests**: Pass as props to the hook
   - Data fetching functions (API calls, queries)
   - Notification/toast functions
   - Other external dependencies
   - This makes tests simple - just pass mock functions as arguments

## Example: useCustomerIdField

See `admin/components/molecules/jobDialogs/jobFormFields/CustomerIdField/useCustomerIdField.ts:5-8`

```typescript
export const useCustomerIdField = (
  getAddressIds: (customerId: string) => Promise<{ addressIds: string[] }>,
  showSuccess: (message: string) => void,
) => {
  // Uses react-hook-form directly (easy to test)
  const formContext = useFormContext<JobFormFields>();
  const customerIdField = useController<JobFormFields>({...});

  // Hook logic here...
}
```

**Key Points:**
- `getAddressIds` (data fetching) - passed as prop, easy to mock in tests
- `showSuccess` (notification) - passed as prop, easy to mock in tests
- `useFormContext`, `useController` - used directly, easy to run in tests

**Test:** `admin/components/molecules/jobDialogs/jobFormFields/CustomerIdField/useCustomerIdField.spec.ts:27-29`

```typescript
const { result } = renderHook(() => useCustomerIdField(mockGetAddressIds, mockShowSuccess), {
  wrapper, // Provides react-hook-form context
});
```

The component using this hook just renders UI based on the returned values - no logic to test in the component itself.
