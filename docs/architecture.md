## Frontend
- Expo.dev for React Native development
- React Native 0.79.5
- Expo Router for navigation
- TypeScript for type safety
- **Primary focus: iOS phone app**

## Backend
- Supabase for backend services
- Authentication with Supabase Auth
- Real-time subscriptions

## Frontend Data Management
- Expo SQLite for local data storage
- Sync with Supabase for offline functionality
- AsyncStorage for key-value storage

## UI Library
- React Native Elements for UI components

## Architecture Patterns

### State Management
- React Context API for global state (AuthContext, NotificationContext, OnboardingContext)
- Custom hooks for component-level state management
- Service layer for business logic separation

### Data Layer
- Service layer pattern with dedicated services:
  - authService, clubService, matchService
  - challengeService, notificationService
  - rankingService, safetyService
- Offline-first architecture with sync strategies
- Singleton pattern for sync services

### Offline Support
- Offline queue management system
- Network detection and automatic sync
- Local SQLite as source of truth
- Background sync with retry logic

### Real-time Features
- Supabase real-time subscriptions
- Push notifications integration
- Live data updates

## Development Methodology
- **Test-Driven Development (TDD)** - Always write tests first

## Key Libraries
- @react-navigation/native for navigation
- @supabase/supabase-js for backend integration
- expo-location for location services
- expo-notifications for push notifications
- expo-image-picker for media handling
- react-native-reanimated for animations
- jest for testing
