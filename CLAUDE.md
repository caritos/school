# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Expo React Native application built with TypeScript, using Expo Router for file-based navigation. The project follows a modern React Native development setup with **primary focus on iOS phone development**, with secondary support for Android and web.

## Development Commands

### Core Development
- `npm start` or `npx expo start` - Start the development server
- `npm run ios` - **PRIMARY TARGET** - Run on iOS simulator/device  
- `npm run android` - Run on Android emulator/device (secondary)
- `npm run web` - Run in web browser (secondary)
- `npm run lint` - Run ESLint
- `npm run reset-project` - Reset to blank project (moves current code to app-example/)

### Testing (TDD Approach)
- `npm test` - Run all tests
- `npm run test -- --watch` - Run tests in watch mode for TDD
- `npm run test -- ComponentName.test.tsx` - Run specific test file
- **DEVELOPMENT WORKFLOW: Always write tests first (TDD)**

### Package Management
- `npm install` - Install dependencies
- `npm ci` - Clean install from package-lock.json

## Architecture Overview

### Navigation Structure
- Uses Expo Router with file-based routing
- Main navigation is in `app/_layout.tsx` (Stack navigator)
- Tab navigation is in `app/(tabs)/_layout.tsx` 
- Route files: `app/(tabs)/index.tsx` (Home), `app/(tabs)/explore.tsx` (Explore)
- 404 handling: `app/+not-found.tsx`

### Component Organization
- **Components**: Reusable UI components in `/components/`
  - Platform-specific variants (e.g., `IconSymbol.ios.tsx`)
  - Themed components (`ThemedText.tsx`, `ThemedView.tsx`)
  - UI components in `/components/ui/`
- **Constants**: Shared constants like Colors in `/constants/`
- **Hooks**: Custom React hooks in `/hooks/`
  - Color scheme management with platform-specific implementations

### Theming System
- Automatic light/dark mode support via `useColorScheme` hook
- Color definitions in `/constants/Colors.ts`
- Theme provider integration with React Navigation themes
- Platform-aware styling (iOS blur effects, etc.)

### TypeScript Configuration
- Strict mode enabled
- Path aliases: `@/*` maps to project root
- Expo TypeScript base configuration
- Includes `.expo/types/**/*.ts` for Expo-generated types

### Platform Support
According to the architecture documentation, this project supports:
- **Frontend**: React Native 0.79.5 with Expo SDK
- **Backend**: Supabase integration planned
- **Data**: Expo SQLite for local storage, AsyncStorage for key-value pairs
- **UI**: React Native Elements (planned)
- **State Management**: React Context API with custom hooks
- **Offline Support**: Offline-first architecture with sync strategies

## Key Libraries and Dependencies

### Core Framework
- Expo SDK ~53.0.20
- React 19.0.0 
- React Native 0.79.5
- TypeScript ~5.8.3

### Navigation & Routing
- Expo Router ~5.1.4
- @react-navigation/native, bottom-tabs, elements

### UI & Styling
- Expo Vector Icons, Symbols
- React Native Reanimated ~3.17.4
- Expo Blur, Image

### Development Tools
- ESLint with expo-config
- Expo development build support

## File Structure Patterns

### App Directory (`/app/`)
- File-based routing with Expo Router
- `_layout.tsx` files define layout components
- `(tabs)` directory for tab-based navigation
- Route components export default React components

### Component Patterns
- Platform-specific components use `.ios.tsx` / `.android.tsx` extensions
- Themed components accept color scheme props
- Custom hooks for cross-platform functionality

### Asset Management
- Images in `/assets/images/` with multiple resolutions (@2x, @3x)
- Fonts in `/assets/fonts/`
- App icons, splash screens, and favicons configured in app.json

## Development Notes

### New Architecture
- Expo new architecture enabled (`"newArchEnabled": true`)
- React Native new architecture support

### Platform Considerations
- iOS: Tab bar transparency and blur effects
- Android: Edge-to-edge enabled, adaptive icons
- Web: Metro bundler, static output

### Path Resolution
- Use `@/` prefix for imports from project root
- TypeScript path mapping configured in tsconfig.json

### Reset Project Workflow
The `reset-project` script allows starting fresh while preserving existing code:
1. Moves current `/app`, `/components`, `/hooks`, `/constants`, `/scripts` to `/app-example/`
2. Creates new minimal `/app` directory with basic index and layout files
3. Useful for transitioning from template to custom implementation

## Development Memories
- put this in the docs directory.. still trying to figure out if this is worthwhile