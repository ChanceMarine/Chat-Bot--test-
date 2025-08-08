# Requirements Document

## Document Information
- **Feature Name**: Minimal Browser Chat-Bot (Echo)
- **Version**: 1.0
- **Date**: 2025-08-08
- **Author**: AI Assistant
- **Stakeholders**: User

## Introduction

This document defines the minimal requirements for a no-backend, single-file browser chat-bot that immediately works by opening an `index.html`. The bot simply replies with an "echo" of the user's message. There is no AI, no API, and no build step.

### Feature Summary
Single static HTML/CSS/JS page that lets a user send messages and see immediate bot echo replies.

### Business Value
Enables instant validation of a basic chat UI and message flow without infrastructure.

### Scope
- **Included**: Single page, input box, send button/Enter key, scrollable chat history, auto-scroll to latest, simple loading indicator, echo reply.
- **Excluded**: AI integration, servers/APIs, authentication, persistence, rich formatting, file uploads, multi-user, frameworks/build tools.

## Requirements

### REQ-001: Send Message and Render Chat History
**User Story:** As a user, I want to type a message and send it, so that I can see my message appear in the chat.

**Acceptance Criteria:**
1. WHEN the user types non-empty text and clicks "Send" OR presses Enter THEN the system SHALL append the user message to the chat history and clear the input.
2. IF the input is empty or whitespace THEN the system SHALL disable or ignore sending.
3. WHERE the chat history overflows the viewport the system SHALL auto-scroll to the latest message.

### REQ-002: Bot Echo Reply
**User Story:** As a user, I want to see a bot reply automatically, so that I can observe a complete chat flow.

**Acceptance Criteria:**
1. WHEN a user message is appended THEN the system SHALL append a bot reply that mirrors the user's text (e.g., `You said: <text>`).
2. WHILE the bot reply is pending the system SHALL show a temporary "Bot is typing…" indicator.
3. IF the user sends multiple messages quickly THEN the system SHALL queue one echo reply per user message in order.

### REQ-003: Input and Interaction
**User Story:** As a user, I want intuitive message entry, so that chat feels natural.

**Acceptance Criteria:**
1. WHEN the user presses Enter without Shift THEN the system SHALL send the message (if non-empty).
2. WHERE a Shift+Enter keypress the system SHALL insert a newline into the input.
3. WHEN a message is sent THEN the system SHALL trim leading/trailing whitespace from the message text.

### REQ-004: Layout and Responsiveness
**User Story:** As a mobile user, I want a responsive chat, so that I can use it on a small screen.

**Acceptance Criteria:**
1. WHERE the viewport is small the system SHALL keep the input area fixed at the bottom and the messages scrollable above it.
2. WHEN the on-screen keyboard opens on mobile THEN the system SHALL keep the latest message visible (auto-scroll on focus).

### REQ-005: Accessibility
**User Story:** As a user with assistive tech, I want accessible semantics, so that chat updates are announced.

**Acceptance Criteria:**
1. WHERE the message list the system SHALL use `role="log"` with `aria-live="polite"` and appropriate labels.
2. WHERE the input the system SHALL include an accessible label and associate the "Send" button with it.

### REQ-006: Delivery Constraints (No Build / Single File)
**User Story:** As a developer, I want a single file I can double-click, so that setup is instant.

**Acceptance Criteria:**
1. WHERE deployment the solution SHALL be a single `index.html` with inline CSS/JS and no external dependencies.
2. WHEN the file is opened via double-click in a modern browser THEN the system SHALL function entirely offline.

## Non-Functional Requirements
- Performance: Append and reply indicator updates SHALL occur within 100ms of event handlers; bot echo delay can be ~300–800ms to simulate typing.
- Browser Support: Latest Chrome/Safari/Firefox/Edge.
- Size: Keep total inline code minimal and readable; no frameworks.
# Requirements Document

## Introduction

This document outlines the requirements for a native SwiftUI meal planner app for iOS 26. The app will provide users with a comprehensive meal planning experience, featuring calorie and macro tracking, meal logging, and food library management. The app will use Supabase for backend authentication and data storage, with Sign in with Apple as the primary authentication method and email/password as a fallback option.

## Requirements

### Requirement 1

**User Story:** As a user, I want to authenticate securely using Sign in with Apple or email/password, so that I can access my personalized meal planning data.

#### Acceptance Criteria

1. WHEN the user opens the app for the first time THEN the system SHALL present Sign in with Apple as the primary authentication option
2. IF Sign in with Apple is unavailable or fails THEN the system SHALL provide email/password authentication as a fallback
3. WHEN the user successfully authenticates THEN the system SHALL store their session securely using Supabase
4. WHEN the user logs out THEN the system SHALL clear all local session data and return to the authentication screen

### Requirement 2

**User Story:** As a user, I want to view my daily nutrition progress on a home dashboard, so that I can track my calorie and macro intake at a glance.

#### Acceptance Criteria

1. WHEN the user opens the home screen THEN the system SHALL display a horizontal calendar strip showing days of the week with dates
2. WHEN displaying the calendar strip THEN the system SHALL highlight the current day with a red dot indicator
3. WHEN showing nutrition progress THEN the system SHALL display a large circular progress indicator for remaining calories
4. WHEN displaying macro information THEN the system SHALL show three separate circular progress indicators for protein (red), carbs (orange), and fat (blue) with remaining values
5. WHEN the user interacts with progress indicators THEN the system SHALL provide swipeable arc interactions with smooth animations

### Requirement 3

**User Story:** As a user, I want to log meals and view my daily food intake, so that I can track what I've eaten throughout the day.

#### Acceptance Criteria

1. WHEN the user views the home screen THEN the system SHALL display a swipeable section showing logged meals
2. WHEN displaying logged meals THEN the system SHALL show meal name, corresponding food icon, calories, macros, and portion size
3. WHEN the user wants to add food THEN the system SHALL provide an "Add food" button that navigates to the food library
4. WHEN meals are logged THEN the system SHALL automatically update the calorie and macro progress indicators
5. WHEN displaying meal information THEN the system SHALL use food icons from the provided icon folder

### Requirement 4

**User Story:** As a user, I want to navigate between different sections of the app using a bottom tab bar, so that I can easily access all app features.

#### Acceptance Criteria

1. WHEN the app loads THEN the system SHALL display a native SwiftUI TabView with four tabs: Home, Plans, Profile, and Search
2. WHEN displaying the tab bar THEN the system SHALL use system images: "house" for Home, "calendar" for Plans, "person" for Profile, "magnifyingglass" for Search
3. WHEN the app starts THEN the system SHALL set Home as the default selected tab
4. WHEN displaying the tab bar THEN the system SHALL automatically apply iOS 26 Liquid Glass effects using native configurations
5. WHEN the user switches tabs THEN the system SHALL provide smooth native iOS 26 transitions

### Requirement 5

**User Story:** As a user, I want to browse and search through a food library, so that I can find meals and ingredients to add to my daily plan.

#### Acceptance Criteria

1. WHEN the user accesses the food library THEN the system SHALL display a list of meals and ingredients from Supabase
2. WHEN displaying food items THEN the system SHALL show name, calories, macros (protein, carbs, fat), portion size, and matching food icon
3. WHEN the user selects food items THEN the system SHALL allow adding items to a "plate" (cart) for batch logging
4. WHEN items are added to the plate THEN the system SHALL calculate and display total calories, macros, and cost
5. WHEN using the food library THEN the system SHALL provide native NavigationStack navigation with automatic Liquid Glass effects

### Requirement 6

**User Story:** As a user, I want to create and edit meals and ingredients, so that I can customize my food library with personal recipes and items.

#### Acceptance Criteria

1. WHEN the user wants to add new food items THEN the system SHALL provide forms for creating meals or ingredients
2. WHEN creating food items THEN the system SHALL allow selection of food icons from the provided icon folder
3. WHEN saving food items THEN the system SHALL store data to Supabase with all required fields (name, calories, macros, price, portion, icon)
4. WHEN creating meals THEN the system SHALL allow selection of ingredients and automatically calculate total calories, macros, and price
5. WHEN editing existing items THEN the system SHALL pre-populate forms with current data and save changes to Supabase

### Requirement 7

**User Story:** As a user, I want the app to have a modern dark theme with iOS 26 design elements, so that I have a visually appealing and consistent experience.

#### Acceptance Criteria

1. WHEN the app loads THEN the system SHALL apply a dark theme with black/gray background as default
2. WHEN displaying UI elements THEN the system SHALL use rounded corners (10-15pt) and subtle shadows adapted for dark mode
3. WHEN showing progress indicators THEN the system SHALL use circular progress bars with colored arcs and gray base circles optimized for dark mode visibility
4. WHEN displaying standard UI components THEN the system SHALL automatically apply Liquid Glass effects to native elements like tab bars and navigation bars
5. WHEN rendering text THEN the system SHALL use consistent San Francisco font typography with bold headers

### Requirement 8

**User Story:** As a user, I want the app to store and sync my data reliably, so that my meal plans and progress are preserved across sessions and devices.

#### Acceptance Criteria

1. WHEN the user logs meals or creates food items THEN the system SHALL save data to Supabase database
2. WHEN the app starts THEN the system SHALL fetch user's food library and logged meals from Supabase
3. WHEN data operations occur THEN the system SHALL handle network errors gracefully with appropriate user feedback
4. WHEN the user switches devices THEN the system SHALL sync all data through their authenticated Supabase account
5. WHEN offline THEN the system SHALL cache essential data locally and sync when connection is restored

### Requirement 9

**User Story:** As a user, I want smooth and fluid animations throughout the app, so that I have a premium iOS experience.

#### Acceptance Criteria

1. WHEN navigating between screens THEN the system SHALL use native iOS 26 SwiftUI transitions
2. WHEN interacting with progress indicators THEN the system SHALL provide smooth swipeable animations
3. WHEN updating nutrition data THEN the system SHALL animate progress bar changes fluidly
4. WHEN adding or removing items THEN the system SHALL use appropriate iOS 26 animation patterns
5. WHEN displaying lists THEN the system SHALL implement smooth scrolling with native iOS momentum

### Requirement 10

**User Story:** As a user, I want the app to be optimized for iOS 26, so that I can take advantage of the latest platform features and performance improvements.

#### Acceptance Criteria

1. WHEN building the app THEN the system SHALL target iOS 26 beta using Xcode 17+
2. WHEN using UI components THEN the system SHALL prioritize native SwiftUI components over custom implementations
3. WHEN applying visual effects THEN the system SHALL use iOS 26-specific Liquid Glass features automatically
4. WHEN handling data THEN the system SHALL use modern Swift concurrency patterns (async/await)
5. WHEN the app runs THEN the system SHALL be compatible with iOS 26 beta simulator and devices