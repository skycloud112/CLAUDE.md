# WebStorm Configuration & Tips

## Editor Settings

### Templates
- "Edit templates" in WebStorm to set timezone always

### General Settings
- **Smart Keys**: Preferences → Editor → General → Smart Keys for auto indent settings
- **Save on switch**: "Save file when switching to built-in terminal" is needed for dev server to pick up new file changes after refactor
- **Reload files**: "Reload all from disk" is needed after changing files in terminal
- **Sync files automatically**: Appearance → System Settings

### Gutter Icons
- Show "run" gutter icon: Editor → General → Gutter Icon

## Performance Optimization

### Speed Up WebStorm
- Settings → System → Disable sync experiment
- Allocate more memory
- Exclude unnecessary folders
- Sync only when active window is needed for TypeScript
- Disable unnecessary settings to improve performance
- Git commit status per line works well

## Navigation & Search

- Have libs have a non-"index" name, or a unique name to make Cmd+P search easier
- Cmd+Click on React components needs React plugin

## Running & Testing

### Keyboard Shortcuts
- **Repeat run**: Keymap → Search "Run" → Set to Cmd+R

## Plugin & Integration

### ESLint
- To have WebStorm pick up new ESLint version, install new version at the root

### React
- Cmd+Click on React components in WebStorm needs React plugin
