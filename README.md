# What If I Learn React Native Again

> Inspired by HUST alumni brothers to thrive in React Native.

## About

A personal learning journey revisiting React Native - documenting progress, experiments, and lessons learned along the way.

## Tech Stack

- **React Native** — Cross-platform mobile framework
- **Expo** *(planned)* — Toolchain for rapid development
- **TypeScript** *(planned)* — Type-safe JavaScript

## Getting Started

```bash
# Clone the repo
git clone https://github.com/<your-username>/what-if-i-learn-react-native-again.git
cd what-if-i-learn-react-native-again

# Install dependencies (once project is initialized)
npm install

# Start the dev server
npx expo start
```

## Roadmap

> Based on [roadmap.sh/react-native](https://roadmap.sh/react-native)

### 1. TypeScript Review

- [ ] TypeScript review for writing hooks (generics, type inference, union types)
- [ ] JSX / TSX syntax
- [ ] ES6+ syntax (arrow functions, destructuring, spread, async/await)

### 2. Environment Setup

- [ ] Expo CLI (`npx create-expo-app`)
- [ ] React Native CLI (bare workflow)
- [ ] Android Studio & emulator
- [ ] Xcode & iOS simulator
- [ ] Debugging tools (Flipper, React DevTools)

### 3. Core Components

- [ ] View, Text, Image, TextInput
- [ ] ScrollView, FlatList, SectionList
- [ ] TouchableOpacity, Pressable
- [ ] SafeAreaView, KeyboardAvoidingView
- [ ] Platform-specific code (`Platform.OS`, `.ios.tsx` / `.android.tsx`)

### 4. Styling

- [ ] StyleSheet API & Flexbox layout
- [ ] Dimensions API & `useWindowDimensions`
- [ ] NativeWind (Tailwind for RN) / Styled Components
- [ ] Responsive design patterns

### 5. Navigation

- [ ] React Navigation setup
- [ ] Stack Navigator
- [ ] Tab Navigator (Bottom Tabs)
- [ ] Drawer Navigator
- [ ] Passing params between screens
- [ ] Deep linking configuration
- [ ] Expo Router (file-based routing)

### 6. State Management

- [ ] useState + useContext (small apps)
- [ ] Zustand (lightweight, recommended)
- [ ] Redux Toolkit (enterprise alternative)
- [ ] TanStack Query / React Query (server state)

### 7. Networking & APIs

- [ ] Fetch API
- [ ] Axios (interceptors, error handling)
- [ ] TanStack Query (`useQuery`, `useMutation`, `useInfiniteQuery`)
- [ ] API mocking (MSW)

### 8. Local Storage

- [ ] AsyncStorage (key-value)
- [ ] MMKV (high-performance, synchronous)
- [ ] SecureStore / Keychain (sensitive data)
- [ ] SQLite / WatermelonDB (relational/offline-first)

### 9. Authentication

- [ ] Firebase Auth
- [ ] JWT-based auth flow
- [ ] OAuth / Social login
- [ ] Secure token storage

### 10. Animations

- [ ] Reanimated 3 (UI thread animations)
- [ ] `useSharedValue`, `useAnimatedStyle`
- [ ] `withTiming`, `withSpring`, `withRepeat`
- [ ] React Native Gesture Handler
- [ ] Shared element transitions

### 11. Push Notifications

- [ ] Expo Notifications
- [ ] Firebase Cloud Messaging (FCM)
- [ ] Handling foreground / background / killed states
- [ ] Deep linking from notifications

### 12. Testing

- [ ] Jest (unit tests)
- [ ] React Native Testing Library (component tests)
- [ ] Detox / Maestro (E2E tests)

### 13. Native Modules & Bridging

- [ ] New Architecture (JSI, TurboModules, Fabric)
- [ ] Custom native modules (Swift/Kotlin)
- [ ] Native UI components

### 14. Performance

- [ ] Hermes engine
- [ ] FlatList optimization (`keyExtractor`, `getItemLayout`)
- [ ] `React.memo`, `useMemo`, `useCallback`
- [ ] JS thread vs UI thread profiling
- [ ] Reanimated for smooth 60/120 fps

### 15. Build & Deployment

- [ ] EAS Build (cloud builds for iOS & Android)
- [ ] EAS Submit (App Store & Play Store)
- [ ] EAS Update (OTA updates)
- [ ] App signing & versioning
- [ ] CI/CD with GitHub Actions
- [ ] Crash reporting (Sentry)

## Contributing

Open to suggestions and PRs from fellow HUST alumni and learners.

## License

MIT
