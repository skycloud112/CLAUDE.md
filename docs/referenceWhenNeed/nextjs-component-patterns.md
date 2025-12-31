
## Next.js Page Pattern

Separate framework-agnostic components from Next.js page wrappers.

- ✅ `page.tsx` - Thin wrapper, extracts route params
- ✅ `GuidPage.tsx` - Framework-agnostic, business logic + UI
- ❌ Don't mix Next.js APIs (`use(params)`) with business logic

### App Folder is Routes Only

- ✅ Route files: `page.tsx`, `layout.tsx`, `error.tsx`, route-specific components
- ❌ NO `app/utils/`, `app/components/`, `app/actions/`, `app/hooks/` folders
- Use root folders instead: `utils/`, `components/`, `actions/`

### Example: FireDetectorsMap (sensorry-ai)

**Structure:**
```typescript
// FireDetectorsMap.tsx - Component handles data fetching internally
export const FireDetectorsMap = ({ onClickMark }: {
  onClickMark: (fireDetectorNo: number) => void
}) => {
  const { data, isFetching, error } = useFireDetectorsListForMap();

  if (error) return <InfoBox style='error'>Error...</InfoBox>;
  if (isFetching) return <LoadingIndicator />;

  return <Map>{/* render data.items */}</Map>;
};
```

**Parent component (simplified):**
```typescript
// SensorDataHomePage.tsx - Parent just passes callback
export function SensorDataHomePage({ redirectToFireDetectorDetail }) {
  return (
	<PageWrapperWithLogo headerText='Fire detectors'>
	  <Typography>Click a fire detector to view details.</Typography>
	  <FireDetectorsMap onClickMark={redirectToFireDetectorDetail} />
	</PageWrapperWithLogo>
  );
}
```

**Use case methods should be placed in the use case that corresponds to the page/feature where they're used**, not in a generic form/CRUD use case:
  - ✅ Good: `createAddressFromInquiry` in `InquiryHomeUseCase` (only used on inquiry home page)
  - ❌ Bad: `createAddressFromInquiry` in `AddressFormUseCase` (not used on address form)
  - This keeps use cases focused and reduces unnecessary dependencies
- Handle edge cases (like empty arrays) with early returns to avoid unnecessary database calls

## component testing
- Use `user.paste()` instead of `user.type()` (4-5x faster)
- Use `act()` instead of `setTimeout()`: `await act(async () => { render(...) })`
- Test loading states: `new Promise<T>(() => {})` (never resolves)
