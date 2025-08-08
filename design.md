# Design Document

## Overview

The iOS Meal Planner app is a native SwiftUI application designed for iOS 26 that provides comprehensive meal planning and nutrition tracking capabilities. The app leverages Supabase for backend services and implements modern iOS design patterns with Liquid Glass effects for a premium user experience.

The architecture follows MVVM (Model-View-ViewModel) pattern with SwiftUI, utilizing modern Swift concurrency (async/await) for data operations and native iOS 26 components for optimal performance and visual fidelity.

## Architecture

### High-Level Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   SwiftUI Views │    │   ViewModels    │    │     Models      │
│                 │    │                 │    │                 │
│ • HomeView      │◄──►│ • HomeViewModel │◄──►│ • FoodItem      │
│ • FoodLibrary   │    │ • AuthViewModel │    │ • User          │
│ • ProfileView   │    │ • FoodViewModel │    │ • NutritionData │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │    Services     │
                    │                 │
                    │ • SupabaseAuth  │
                    │ • DataService   │
                    │ • IconService   │
                    └─────────────────┘
```

### Technology Stack

- **Frontend**: SwiftUI (iOS 26)
- **Backend**: Supabase (Authentication, Database, Real-time)
- **Authentication**: Sign in with Apple + Email/Password fallback
- **Database**: PostgreSQL (via Supabase)
- **Image Assets**: Local PNG icons with dynamic loading
- **Concurrency**: Swift async/await patterns

## Components and Interfaces

### Core Views

#### 1. ContentView (Root Container)
- Manages authentication state
- Presents AuthenticationView or MainTabView based on user session
- Handles app lifecycle and deep linking

#### 2. MainTabView
- Native SwiftUI TabView with four tabs
- Automatic Liquid Glass effects via `.toolbarBackground(.automatic, for: .tabBar)`
- Tab structure:
  - Home: HomeView (house icon)
  - Plans: PlansView (calendar icon) 
  - Profile: ProfileView (person icon)
  - Search: SearchView (magnifyingglass icon)

#### 3. HomeView
- **Calendar Strip**: Horizontal ScrollView with date components
  - Current day highlighted with red dot indicator
  - Smooth scrolling with snap-to-date behavior
- **Nutrition Dashboard**: 
  - Large circular progress for remaining calories
  - Three macro progress circles (protein/red, carbs/orange, fat/blue)
  - Swipeable arc interactions with haptic feedback
- **Meal Log Section**:
  - Horizontal ScrollView of logged meals
  - Each meal card shows icon, name, calories, macros
  - "Add Food" button with plus icon

#### 4. FoodLibraryView
- NavigationStack with automatic Liquid Glass navigation bar
- List/Grid toggle for food items display
- Search functionality with real-time filtering
- "Plate" (cart) system for batch meal logging
- Add/Edit forms with icon selection

#### 5. AuthenticationView
- Sign in with Apple button (primary)
- Email/password form (fallback)
- Clean, minimal design with app branding

### ViewModels

#### 1. AuthenticationViewModel
```swift
@MainActor
class AuthenticationViewModel: ObservableObject {
    @Published var isAuthenticated = false
    @Published var currentUser: User?
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    func signInWithApple() async
    func signInWithEmail(email: String, password: String) async
    func signOut() async
}
```

#### 2. HomeViewModel
```swift
@MainActor
class HomeViewModel: ObservableObject {
    @Published var currentDate = Date()
    @Published var dailyNutrition: NutritionData
    @Published var loggedMeals: [LoggedMeal] = []
    @Published var caloriesRemaining: Double = 0
    @Published var macrosRemaining: MacroData
    
    func loadDailyData() async
    func addMealToLog(_ meal: FoodItem) async
    func updateNutritionProgress()
}
```

#### 3. FoodLibraryViewModel
```swift
@MainActor
class FoodLibraryViewModel: ObservableObject {
    @Published var foodItems: [FoodItem] = []
    @Published var plateItems: [FoodItem] = []
    @Published var searchText = ""
    @Published var isLoading = false
    
    func loadFoodItems() async
    func addToPlate(_ item: FoodItem)
    func saveFoodItem(_ item: FoodItem) async
    func calculatePlateNutrition() -> NutritionData
}
```

## Data Models

### Core Models

#### 1. FoodItem
```swift
struct FoodItem: Identifiable, Codable {
    let id: UUID
    var name: String
    var calories: Double
    var protein: Double
    var carbs: Double
    var fat: Double
    var price: Double
    var portion: String
    var isMeal: Bool
    var ingredients: [UUID]? // For meals only
    var iconName: String
    var createdAt: Date
    var userId: UUID
}
```

#### 2. User
```swift
struct User: Identifiable, Codable {
    let id: UUID
    var email: String?
    var appleUserId: String?
    var dailyCalorieGoal: Double
    var dailyProteinGoal: Double
    var dailyCarbGoal: Double
    var dailyFatGoal: Double
    var createdAt: Date
}
```

#### 3. NutritionData
```swift
struct NutritionData: Codable {
    var calories: Double
    var protein: Double
    var carbs: Double
    var fat: Double
    var cost: Double
    
    static func + (lhs: NutritionData, rhs: NutritionData) -> NutritionData
    static func - (lhs: NutritionData, rhs: NutritionData) -> NutritionData
}
```

#### 4. LoggedMeal
```swift
struct LoggedMeal: Identifiable, Codable {
    let id: UUID
    let userId: UUID
    let foodItemId: UUID
    let foodItem: FoodItem
    let loggedAt: Date
    let quantity: Double
}
```

### Supabase Schema

#### food_items table
```sql
CREATE TABLE food_items (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES auth.users(id),
    name TEXT NOT NULL,
    calories DOUBLE PRECISION NOT NULL,
    protein DOUBLE PRECISION NOT NULL,
    carbs DOUBLE PRECISION NOT NULL,
    fat DOUBLE PRECISION NOT NULL,
    price DOUBLE PRECISION DEFAULT 0,
    portion TEXT NOT NULL,
    is_meal BOOLEAN DEFAULT false,
    ingredients UUID[] DEFAULT NULL,
    icon_name TEXT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

#### logged_meals table
```sql
CREATE TABLE logged_meals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES auth.users(id),
    food_item_id UUID REFERENCES food_items(id),
    logged_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    quantity DOUBLE PRECISION DEFAULT 1
);
```

#### user_profiles table
```sql
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id),
    daily_calorie_goal DOUBLE PRECISION DEFAULT 2000,
    daily_protein_goal DOUBLE PRECISION DEFAULT 150,
    daily_carb_goal DOUBLE PRECISION DEFAULT 250,
    daily_fat_goal DOUBLE PRECISION DEFAULT 65,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

## Services Architecture

### 1. SupabaseService
```swift
class SupabaseService {
    static let shared = SupabaseService()
    private let client: SupabaseClient
    
    // Authentication
    func signInWithApple() async throws -> User
    func signInWithEmail(email: String, password: String) async throws -> User
    func signOut() async throws
    
    // Data Operations
    func fetchFoodItems() async throws -> [FoodItem]
    func saveFoodItem(_ item: FoodItem) async throws
    func fetchLoggedMeals(for date: Date) async throws -> [LoggedMeal]
    func logMeal(_ meal: LoggedMeal) async throws
}
```

### 2. IconService
```swift
class IconService {
    static let shared = IconService()
    private let iconCache: NSCache<NSString, UIImage>
    
    func loadIcon(named: String) -> Image?
    func availableIcons() -> [String]
    func cacheIcon(_ image: UIImage, forName name: String)
}
```

## User Interface Design

### Design System

#### Color Palette (Dark Theme)
- **Primary Background**: `Color(.systemBackground)` - Adapts to dark mode
- **Secondary Background**: `Color(.secondarySystemBackground)`
- **Card Background**: `Color(.tertiarySystemBackground)`
- **Accent Colors**:
  - Protein: `Color.red`
  - Carbs: `Color.orange` 
  - Fat: `Color.blue`
  - Current Day: `Color.red`

#### Typography
- **Headers**: San Francisco Bold, 24pt
- **Subheaders**: San Francisco Semibold, 18pt
- **Body**: San Francisco Regular, 16pt
- **Captions**: San Francisco Regular, 14pt

#### Spacing & Layout
- **Card Padding**: 16pt
- **Section Spacing**: 24pt
- **Corner Radius**: 12pt for cards, 8pt for buttons
- **Progress Circle Size**: 120pt (large), 80pt (macro circles)

### Liquid Glass Implementation

#### Automatic Native Effects
```swift
// Tab Bar - Automatic Liquid Glass
TabView {
    // Tab content
}
.toolbarBackground(.automatic, for: .tabBar)

// Navigation Bar - Automatic Liquid Glass  
NavigationStack {
    // Content
}
.toolbarBackground(.automatic, for: .navigationBar)

// Card Backgrounds - Manual Liquid Glass
VStack {
    // Card content
}
.background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 12))
```

### Custom Components

#### 1. CircularProgressView
```swift
struct CircularProgressView: View {
    let progress: Double
    let total: Double
    let color: Color
    let lineWidth: CGFloat
    
    var body: some View {
        ZStack {
            Circle()
                .stroke(Color.gray.opacity(0.3), lineWidth: lineWidth)
            
            Circle()
                .trim(from: 0, to: progress / total)
                .stroke(color, style: StrokeStyle(lineWidth: lineWidth, lineCap: .round))
                .rotationEffect(.degrees(-90))
                .animation(.easeInOut(duration: 0.5), value: progress)
        }
    }
}
```

#### 2. CalendarStripView
```swift
struct CalendarStripView: View {
    @Binding var selectedDate: Date
    let dates: [Date]
    
    var body: some View {
        ScrollView(.horizontal, showsIndicators: false) {
            HStack(spacing: 16) {
                ForEach(dates, id: \.self) { date in
                    DateCellView(date: date, isSelected: Calendar.current.isDate(date, inSameDayAs: selectedDate))
                        .onTapGesture {
                            selectedDate = date
                        }
                }
            }
            .padding(.horizontal)
        }
    }
}
```

## Error Handling

### Error Types
```swift
enum AppError: LocalizedError {
    case authenticationFailed
    case networkUnavailable
    case dataCorrupted
    case supabaseError(String)
    case iconNotFound(String)
    
    var errorDescription: String? {
        switch self {
        case .authenticationFailed:
            return "Authentication failed. Please try again."
        case .networkUnavailable:
            return "Network connection unavailable."
        case .dataCorrupted:
            return "Data appears to be corrupted."
        case .supabaseError(let message):
            return "Database error: \(message)"
        case .iconNotFound(let name):
            return "Icon '\(name)' not found."
        }
    }
}
```

### Error Handling Strategy
- **Network Errors**: Retry mechanism with exponential backoff
- **Authentication Errors**: Clear session and redirect to login
- **Data Errors**: Show user-friendly messages with retry options
- **Icon Loading Errors**: Fallback to default system icons

## Testing Strategy

### Unit Testing
- **ViewModels**: Test business logic, data transformations, async operations
- **Services**: Mock Supabase responses, test error handling
- **Models**: Test data validation, calculations, serialization

### Integration Testing
- **Authentication Flow**: Test Apple Sign-In and email/password flows
- **Data Persistence**: Test Supabase CRUD operations
- **Navigation**: Test tab switching and deep linking

### UI Testing
- **Critical User Flows**: Authentication, meal logging, food creation
- **Accessibility**: VoiceOver support, Dynamic Type scaling
- **Dark Mode**: Visual regression testing

### Performance Testing
- **Memory Usage**: Monitor for memory leaks in image loading
- **Network Performance**: Test with slow network conditions
- **Animation Performance**: Ensure 60fps during transitions

## Security Considerations

### Authentication Security
- Secure storage of authentication tokens using Keychain
- Automatic token refresh handling
- Proper session management and cleanup

### Data Security
- Row Level Security (RLS) policies in Supabase
- Input validation and sanitization
- Secure API key management (environment variables)

### Privacy
- Minimal data collection (only necessary for app functionality)
- Local caching with automatic cleanup
- Compliance with Apple's privacy guidelines