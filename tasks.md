# Implementation Plan

- [x] 1. Set up Xcode project structure and dependencies
  - Create new iOS 26 SwiftUI project targeting iOS 26.0+
  - Add Supabase Swift SDK dependency via Swift Package Manager
  - Configure project settings for Sign in with Apple capability
  - Set up folder structure for Models, Views, ViewModels, Services
  - _Requirements: 10.1, 10.2_

- [ ] 2. Create core data models and types
  - Implement FoodItem model with all required properties and Codable conformance
  - Create User model with authentication and nutrition goal properties
  - Implement NutritionData model with arithmetic operators for calculations
  - Create LoggedMeal model for meal tracking functionality
  - Define AppError enum with localized error descriptions
  - _Requirements: 8.1, 8.2, 6.3_

- [x] 3. Implement Supabase service layer
  - Create SupabaseService singleton with client configuration
  - Implement authentication methods (Apple Sign-In, email/password, sign out)
  - Add CRUD operations for food items with async/await patterns
  - Implement meal logging and retrieval methods
  - Add error handling and network retry logic
  - _Requirements: 1.1, 1.2, 1.3, 8.1, 8.3_

- [x] 4. Create authentication system
  - Implement AuthenticationViewModel with published properties for UI state
  - Create AuthenticationView with Sign in with Apple button and email/password form
  - Add Sign in with Apple configuration and credential handling
  - Implement email/password fallback authentication flow
  - Add secure session management with proper cleanup on logout
  - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [ ] 5. Build main app structure and navigation
  - Create ContentView as root container managing authentication state
  - Implement MainTabView with native SwiftUI TabView and four tabs
  - Configure tab bar with system images and Liquid Glass effects
  - Set up NavigationStack for each tab with automatic Liquid Glass
  - Add proper tab selection state management
  - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5_

- [x] 6. Implement custom UI components
  - Create CircularProgressView with animated progress arcs and color customization
  - Build CalendarStripView with horizontal scrolling and current day highlighting
  - Implement DateCellView with red dot indicator for current day
  - Create FoodItemCardView for displaying meals with icons and nutrition info
  - Add MealLogCardView for logged meals display with swipe interactions
  - _Requirements: 2.2, 2.3, 2.4, 2.5, 3.1, 3.2_

- [x] 7. Create icon management system
  - Implement IconService for loading and caching food icons from local folder
  - Add icon loading methods with fallback to system icons
  - Create icon picker component for food item creation/editing
  - Implement icon caching mechanism for performance optimization
  - Add validation for icon file existence and format
  - _Requirements: 3.5, 6.2, 7.4_

- [x] 8. Build home dashboard view
  - Create HomeViewModel with nutrition tracking and meal logging state
  - Implement HomeView with calendar strip, nutrition progress, and meal log sections
  - Add large circular progress indicator for remaining calories with swipeable interactions
  - Create three macro progress circles (protein/red, carbs/orange, fat/blue)
  - Implement meal log section with horizontal scrolling and "Add Food" button
  - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3, 3.4_

- [x] 9. Implement food library functionality
  - Create FoodLibraryViewModel with food items management and plate system
  - Build FoodLibraryView with list display, search, and navigation
  - Add food item creation/editing forms with all required fields
  - Implement "plate" (cart) system for batch meal logging
  - Add search and filtering functionality for food items
  - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5, 6.1, 6.2, 6.3, 6.4, 6.5_

- [x] 10. Add meal calculation and logging logic
  - Implement nutrition calculation methods in ViewModels
  - Add meal composition logic for calculating totals from ingredients
  - Create meal logging functionality that updates daily nutrition progress
  - Implement automatic progress indicator updates when meals are logged
  - Add validation for nutrition data and portion calculations
  - _Requirements: 3.4, 6.4, 8.1_

- [x] 11. Implement dark theme and iOS 26 styling
  - Apply dark theme color scheme throughout the app
  - Configure Liquid Glass effects for tab bar and navigation bars
  - Add rounded corners, shadows, and spacing according to design system
  - Implement San Francisco font typography with proper sizing
  - Apply iOS 26 specific visual effects and materials
  - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 10.3_

- [x] 12. Add animations and transitions
  - Implement smooth SwiftUI transitions between views and tabs
  - Add swipeable arc animations for progress indicators
  - Create fluid animations for nutrition progress updates
  - Add haptic feedback for user interactions
  - Implement smooth scrolling animations for lists and calendar
  - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5, 4.5_

- [x] 13. Create placeholder views for remaining tabs
  - Implement basic PlansView structure for future meal planning features
  - Create ProfileView with user settings and nutrition goals
  - Add SearchView with food search functionality
  - Ensure all views follow the same design system and navigation patterns
  - Add proper tab bar integration for all placeholder views
  - _Requirements: 4.1, 4.2_

- [x] 14. Add comprehensive error handling
  - Implement error handling throughout all ViewModels
  - Add user-friendly error messages and retry mechanisms
  - Create network error handling with offline state management
  - Add validation errors for form inputs
  - Implement graceful degradation for missing icons or data
  - _Requirements: 8.3, 8.4_

- [x] 15. Write unit tests for core functionality
  - Create unit tests for all data models and their methods
  - Test SupabaseService methods with mocked responses
  - Add tests for ViewModels business logic and state management
  - Test nutrition calculations and meal composition logic
  - Create tests for authentication flows and error handling
  - _Requirements: 8.1, 8.2, 1.1, 1.2_

- [x] 16. Integrate and test complete user flows
  - Test complete authentication flow from login to main app
  - Verify meal logging updates nutrition progress correctly
  - Test food item creation and editing with icon selection
  - Validate plate system for batch meal logging
  - Ensure proper data persistence and synchronization with Supabase
  - _Requirements: 1.1, 1.2, 1.3, 3.3, 3.4, 5.3, 5.4, 6.3, 6.4, 6.5, 8.1, 8.2_