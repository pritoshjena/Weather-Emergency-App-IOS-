# Weather Emergency App — iOS Lab Project

## 1. Project Information

**Project:** Weather Emergency App  
**Platform:** iOS  
**Development Environment:** Xcode  
**Language:** Swift 5  
**Minimum iOS Version:** iOS 16.0  
**UI Architecture:** SwiftUI-oriented View / ViewModel / Service separation  
**Backend:** Supabase  
**Supabase Swift SDK:** 2.49.0  
**Project Version:** 1.0  
**Device Family:** iPhone  
**Bundle Identifier:** `com.pritosh.WeatherEmergencyApp`  
**Mentor:** Anjali Srivastava

### Team

| Student | Enrollment / Roll No. | Primary Responsibility |
|---|---|---|
| **Pritosh** | `2502221530143` | **Project Lead, Architecture & Integration** |
| **Prayag** | `2502221530141` | **Weather, Location & Maps Module** |
| **Rahul** | `2502221530148` | **Authentication, Emergency Reporting & Backend Module** |

### Overall Responsibility

**Pritosh should take overall project responsibility** because the project needs one person responsible for integrating the UI, ViewModels, services, backend configuration, testing, Git/merge coordination, and final Xcode build.

This does **not** mean Pritosh does all the coding. Each member owns a defined module, while Pritosh owns final integration and release readiness.

---

# 2. Project Objective

The Weather Emergency App is an iOS application designed to combine:

1. Weather information.
2. User location detection.
3. Location/geocoding support.
4. Nearby emergency-service discovery.
5. Emergency information and safety guidance.
6. Emergency incident reporting.
7. User authentication/profile management.
8. A history of submitted emergency reports.

The application follows a layered architecture so that UI code, business logic, network/API operations, and data models remain separated.

---

# 3. High-Level Architecture

```text
                    ┌──────────────────────┐
                    │      SwiftUI UI      │
                    │  Views / Navigation  │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │      ViewModels      │
                    │ State + Business     │
                    │ Logic + UI Binding   │
                    └──────────┬───────────┘
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
       ┌─────────────┐ ┌─────────────┐ ┌──────────────┐
       │ Weather     │ │ Location /  │ │ Emergency /  │
       │ Services    │ │ Map Services│ │ Report       │
       └──────┬──────┘ └──────┬──────┘ └──────┬───────┘
              │               │               │
              └───────────────┼───────────────┘
                              ▼
                    ┌──────────────────────┐
                    │ External Services    │
                    │ Weather / Maps /     │
                    │ Geocoding / Supabase │
                    └──────────────────────┘
```

---

# 4. Project Structure

The Xcode project contains the following major components.

## 4.1 App Entry and Root Navigation

### `WeatherEmergencyApp.swift`
Application entry point.

Responsibilities:
- Starts the application.
- Configures the root SwiftUI hierarchy.
- Initializes application-level dependencies.

### `RootView.swift`
Controls the application's initial/root navigation state.

Typical responsibility:
- Decide whether the user sees authentication, splash, or the main application.
- Connect the authentication state to the main UI.

### `SplashView.swift`
Initial loading/splash screen.

---

# 5. Data Models

The project defines dedicated Swift model files.

### `EmergencyReport.swift`

Represents an emergency report submitted by a user.

Expected responsibilities:
- Report identifier.
- User association.
- Emergency type/category.
- Description/details.
- Location information.
- Timestamp/status.
- Serialization/deserialization for backend communication.

### `UserProfile.swift`

Represents application user/profile information.

Responsibilities:
- User identity.
- Profile information.
- Backend user association.
- Data used by profile screens.

### `EmergencyInfoData.swift`

Stores structured emergency/safety information.

Responsibilities:
- Emergency categories.
- Safety instructions.
- Static emergency guidance.
- Information displayed by `EmergencyInfoView`.

### `EmergencyServicePlace.swift`

Represents a nearby emergency-service location.

Possible service types:
- Hospital
- Police station
- Fire station
- Other emergency facilities

The model is used by emergency-service search/map functionality.

### `WeatherModels.swift`

Contains weather API response models.

Responsibilities:
- Decode JSON weather responses.
- Represent weather conditions.
- Store temperature and related weather fields.
- Provide strongly typed data to `WeatherViewModel`.

---

# 6. ViewModels

ViewModels are responsible for connecting the SwiftUI views to business/application logic.

## `AuthViewModel.swift`

Owner: **Rahul**

Responsibilities:
- Login state.
- Sign-up state.
- Authentication validation.
- Session handling.
- Supabase authentication integration.
- Authentication errors.

## `WeatherViewModel.swift`

Owner: **Prayag**

Responsibilities:
- Request weather information.
- Manage loading state.
- Store weather data.
- Handle API errors.
- Update the weather UI.

## `LocationViewModel.swift`

Owner: **Prayag**

Responsibilities:
- Coordinate current-location state.
- Request location information from `LocationService`.
- Convert coordinates into usable application location data.
- Provide location information to weather/maps features.

## `EmergencyServicesViewModel.swift`

Owner: **Prayag**

Responsibilities:
- Search nearby emergency services.
- Coordinate location + map search.
- Manage loading/error states.
- Supply service locations to the emergency-services UI.

## `ReportViewModel.swift`

Owner: **Rahul**

Responsibilities:
- Create emergency reports.
- Validate report data.
- Submit reports through `ReportService`.
- Track submission/loading/error states.

## `MyReportsViewModel.swift`

Owner: **Rahul**

Responsibilities:
- Retrieve user's previous reports.
- Manage report history.
- Refresh report data.
- Handle backend errors.

---

# 7. Service Layer

Services isolate network/backend/platform operations from the UI.

## `WeatherService.swift`

Owner: **Prayag**

Responsibilities:
- Weather API communication.
- Construct API requests.
- Decode weather responses.
- Handle HTTP/network failures.

Workflow:

```text
WeatherView
     ↓
WeatherViewModel
     ↓
WeatherService
     ↓
Weather API
     ↓
WeatherModels
     ↓
WeatherViewModel
     ↓
WeatherView
```

## `GeocodingService.swift`

Owner: **Prayag**

Responsibilities:
- Convert coordinates into human-readable addresses.
- Support reverse geocoding.
- Handle geocoding errors.

## `MapSearchService.swift`

Owner: **Prayag**

Responsibilities:
- Search for nearby emergency services.
- Integrate with map/search functionality.
- Return structured `EmergencyServicePlace` data.

## `LocationService.swift`

Owner: **Prayag**

Responsibilities:
- Request user location permission.
- Access current device coordinates.
- Monitor/update location.
- Handle location authorization/errors.

This component should use Apple's Core Location framework.

---

## `ReportService.swift`

Owner: **Rahul**

Responsibilities:
- Create/submit emergency reports.
- Retrieve reports.
- Communicate with Supabase.
- Map backend errors into application errors.

---

## `SupabaseManager.swift`

Owner: **Pritosh + Rahul**

Responsibilities:
- Central Supabase client configuration.
- Backend dependency initialization.
- Database/auth access.
- Prevent duplicated backend configuration throughout the app.

**Pritosh:** integration/configuration ownership.  
**Rahul:** authentication/report/database implementation ownership.

---

# 8. SwiftUI Views

## Authentication

### `LoginView.swift`
User login interface.

### `SignUpView.swift`
New user registration interface.

Ownership: **Rahul**

---

## Main Application

### `HomeView.swift`

Main dashboard/home screen.

Likely responsibilities:
- Weather summary.
- Emergency shortcuts.
- Navigation to emergency services.
- Navigation to reports/profile.

Ownership: **Pritosh**

---

## Weather

### `WeatherView.swift`

Displays weather information.

Ownership: **Prayag**

Typical UI flow:

```text
User opens WeatherView
        ↓
WeatherViewModel
        ↓
LocationViewModel / LocationService
        ↓
Current coordinates
        ↓
WeatherService
        ↓
Weather API
        ↓
WeatherModels
        ↓
WeatherView
```

---

## Emergency Services

### `EmergencyServicesView.swift`

Displays nearby emergency services.

Ownership: **Prayag**

Workflow:

```text
User opens Emergency Services
        ↓
Request location permission
        ↓
Get coordinates
        ↓
Search nearby services
        ↓
MapSearchService
        ↓
EmergencyServicePlace
        ↓
Display services on UI
```

---

## Emergency Information

### `EmergencyInfoView.swift`

Displays emergency/safety information.

Ownership: **Pritosh**

Data source:
- `EmergencyInfoData.swift`

This module can remain mostly local/static and therefore does not need a backend request for every screen load.

---

## Emergency Reporting

### `ReportEmergencyView.swift`

Allows the user to submit an emergency report.

Ownership: **Rahul**

Workflow:

```text
User fills report
        ↓
Input validation
        ↓
ReportViewModel
        ↓
ReportService
        ↓
Supabase
        ↓
Success / Error
        ↓
UI feedback
```

---

## User Profile

### `ProfileView.swift`

Displays user profile/account information.

Ownership: **Rahul**

---

## My Reports

### `MyReportsView.swift`

Displays previously submitted emergency reports.

Ownership: **Rahul**

Workflow:

```text
MyReportsView
      ↓
MyReportsViewModel
      ↓
ReportService
      ↓
Supabase Database
      ↓
EmergencyReport models
      ↓
MyReportsView
```

---

# 9. Shared Utilities

## `AppError.swift`

Centralized application error representation.

Use it to avoid inconsistent error handling across modules.

Examples:

```text
Network error
Authentication error
Location permission error
Invalid response
Database error
Validation error
Unknown error
```

## `Helpers.swift`

Reusable helper functions/extensions.

## `Constants.swift`

Central location for constants such as:
- API configuration keys/names.
- Application constants.
- Database table names.
- UI/application-level configuration.

Avoid putting secrets directly into Swift source files.

---

# 10. Secrets and Configuration

The project contains:

- `Secrets.example.plist`
- `Secrets.plist`

### Important

`Secrets.plist` must not be committed to a public Git repository if it contains real credentials.

Use:

```text
Secrets.example.plist
```

as a template.

Typical sensitive values may include:
- API keys.
- Supabase URL.
- Supabase publishable/anon key.
- Other environment configuration.

Recommended workflow:

```text
Secrets.example.plist
        ↓
Copy locally
        ↓
Secrets.plist
        ↓
Insert local credentials
        ↓
Keep Secrets.plist out of Git
```

---

# 11. Supabase Backend

The project includes Supabase Swift package integration.

Configured package version:

```text
supabase-swift 2.49.0
```

The project also contains:

```text
schema.sql
```

The schema should be used as the database definition/reference for the application backend.

## Backend Responsibilities

The backend should support at least:

```text
Authentication
      │
      ├── User registration
      ├── User login
      └── Session/user identity
             │
             ▼
       User Profile
             │
             ▼
      Emergency Reports
             │
             ├── Create report
             ├── Retrieve own reports
             └── Track report data
```

Rahul owns implementation of authentication/report operations, while Pritosh verifies final backend integration.

---

# 12. Dependency

The project uses Swift Package Manager.

Important dependency:

```text
Supabase
Version: 2.49.0
```

The exact resolved dependency information is maintained by:

```text
Package.resolved
```

Do not manually delete or randomly change resolved package versions without checking compatibility.

---

# 13. Team Responsibility Matrix

| Module | Pritosh | Prayag | Rahul |
|---|---:|---:|---:|
| Overall project lead | **Primary** | Support | Support |
| Xcode project/integration | **Primary** | Support | Support |
| App architecture | **Primary** | Support | Support |
| Root navigation | **Primary** | - | Support |
| Home dashboard | **Primary** | Support | Support |
| Weather UI | Review | **Primary** | - |
| Weather API/service | Review | **Primary** | - |
| Location | Review | **Primary** | - |
| Geocoding | Review | **Primary** | - |
| Map/emergency service search | Review | **Primary** | - |
| Emergency information | **Primary** | Support | - |
| Login/signup | Review | - | **Primary** |
| Supabase authentication | Review | - | **Primary** |
| Emergency reporting | Review | - | **Primary** |
| My Reports | Review | - | **Primary** |
| User profile | Review | - | **Primary** |
| Database/schema | **Integration** | - | **Primary** |
| Error handling | **Primary** | Module owner | Module owner |
| Unit testing | **Lead** | Weather/location tests | Auth/report tests |
| UI testing | **Lead** | Weather/maps UI | Auth/report UI |
| Git/merge coordination | **Primary** | Support | Support |
| Final demo/build | **Primary** | Support | Support |
| Documentation | **Primary** | Technical input | Technical input |

---

# 14. Recommended Git Workflow

Use a shared Git repository with one protected main branch.

```text
main
 │
 ├── feature/pritosh-integration
 ├── feature/prayag-weather-location
 └── feature/rahul-auth-reports
```

## Branch Ownership

### Pritosh

```text
feature/pritosh-integration
```

Focus:
- Root navigation.
- Home.
- Integration.
- Architecture.
- Configuration.
- Final merging.

### Prayag

```text
feature/prayag-weather-location
```

Focus:
- Weather.
- Location.
- Geocoding.
- Maps/emergency services.

### Rahul

```text
feature/rahul-auth-reports
```

Focus:
- Authentication.
- Supabase user operations.
- Emergency reports.
- Profile.
- My Reports.

---

# 15. Development Workflow

The recommended end-to-end workflow is:

```text
1. Requirement Analysis
        ↓
2. UI / Architecture Design
        ↓
3. Data Model Definition
        ↓
4. Service Layer Development
        ↓
5. ViewModel Development
        ↓
6. SwiftUI View Development
        ↓
7. Supabase Integration
        ↓
8. Module Testing
        ↓
9. Git Pull Request / Code Review
        ↓
10. Integration by Pritosh
        ↓
11. Full App Testing
        ↓
12. Bug Fixing
        ↓
13. Final Build
        ↓
14. iOS Lab Demonstration
```

---

# 16. Recommended Implementation Order

## Phase 1 — Project Setup

**Owner:** Pritosh

- Configure Xcode project.
- Verify iOS 16 target.
- Verify Swift version.
- Configure Swift Package Manager.
- Configure Supabase dependency.
- Establish Git repository.
- Create branch strategy.

---

## Phase 2 — Models

**Owners:** All module owners

Create/verify:

```text
WeatherModels.swift
EmergencyReport.swift
UserProfile.swift
EmergencyInfoData.swift
EmergencyServicePlace.swift
```

Models should be agreed upon before implementing dependent ViewModels/services.

---

## Phase 3 — Authentication

**Owner:** Rahul

Implement:

```text
SignUpView
LoginView
AuthViewModel
Supabase authentication
User session
```

Test:
- Valid signup.
- Invalid signup.
- Valid login.
- Invalid login.
- Logout/session behavior.

---

## Phase 4 — Weather and Location

**Owner:** Prayag

Implement:

```text
LocationService
LocationViewModel
WeatherService
WeatherViewModel
GeocodingService
WeatherView
```

Test:
- Permission granted.
- Permission denied.
- Current location.
- Weather API success.
- Weather API failure.
- Invalid/empty response.

---

## Phase 5 — Emergency Services

**Owner:** Prayag

Implement:

```text
MapSearchService
EmergencyServicesViewModel
EmergencyServicesView
EmergencyServicePlace
```

Test:
- Location available.
- Nearby services found.
- No services found.
- Network failure.
- Invalid location.

---

## Phase 6 — Emergency Reports

**Owner:** Rahul

Implement:

```text
ReportEmergencyView
ReportViewModel
ReportService
EmergencyReport
MyReportsViewModel
MyReportsView
```

Test:
- Valid report.
- Missing required information.
- Successful submission.
- Backend failure.
- Retrieve previous reports.

---

## Phase 7 — Integration

**Owner:** Pritosh

Connect:

```text
Splash
  ↓
Authentication
  ↓
Home
  ├── Weather
  ├── Emergency Services
  ├── Emergency Information
  ├── Report Emergency
  ├── My Reports
  └── Profile
```

Verify that every module works together without duplicated state or broken navigation.

---

# 17. Testing Strategy

The project contains test targets for:

```text
WeatherEmergencyAppTests
WeatherEmergencyAppUITests
```

## Unit Testing

Test ViewModels and service-related logic independently.

Examples:

```text
AuthViewModel
WeatherViewModel
LocationViewModel
EmergencyServicesViewModel
ReportViewModel
MyReportsViewModel
```

## UI Testing

Test important user journeys.

### Journey 1 — Login

```text
Launch
 ↓
Login
 ↓
Enter credentials
 ↓
Authenticate
 ↓
Home
```

### Journey 2 — Weather

```text
Home
 ↓
Weather
 ↓
Location permission
 ↓
Weather data
 ↓
Weather displayed
```

### Journey 3 — Emergency Service

```text
Home
 ↓
Emergency Services
 ↓
Location
 ↓
Nearby service search
 ↓
Service displayed
```

### Journey 4 — Emergency Report

```text
Home
 ↓
Report Emergency
 ↓
Enter information
 ↓
Submit
 ↓
Backend
 ↓
Success
 ↓
My Reports
```

---

# 18. Error Handling

Errors should be handled at the service/ViewModel boundary rather than causing application crashes.

Recommended pattern:

```text
Service
   ↓
throws AppError
   ↓
ViewModel catches error
   ↓
updates @Published state
   ↓
SwiftUI displays user-friendly message
```

Example user-facing messages:

```text
"Unable to load weather. Please try again."

"Location permission is required to find nearby services."

"Unable to submit your emergency report."

"Please check your internet connection."
```

Do not expose raw backend/API errors directly to users.

---

# 19. Location and Privacy

The application uses location-related functionality.

The app should:
- Request location permission only when needed.
- Clearly explain why location is required.
- Handle denied/restricted permission.
- Avoid unnecessarily storing precise location.
- Follow Apple's privacy requirements.

Location should primarily be used to:
1. Determine the user's current area.
2. Retrieve weather.
3. Search nearby emergency services.
4. Optionally associate an emergency report with location when required by the project design.

---

# 20. Security Requirements

### Never commit:

```text
Secrets.plist
API private keys
Passwords
Service-role keys
User credentials
```

### Use:

```text
Secrets.example.plist
```

for documentation/template purposes.

Supabase client configuration must use a client-safe key where appropriate; privileged server/service-role credentials must never be embedded in an iOS application.

---

# 21. Code Quality Rules

All three members should follow the same conventions.

### Naming

Use descriptive Swift names:

```swift
WeatherViewModel
ReportService
EmergencyReport
```

Avoid:

```swift
VM1
Service2
dataManager
```

### View Responsibility

SwiftUI Views should primarily handle:
- Layout.
- User interaction.
- Navigation.
- Display state.

Business/network logic should remain in:
- ViewModels.
- Services.

### Service Responsibility

Services should handle:
- API requests.
- Backend requests.
- Platform service communication.
- Serialization/deserialization.

---

# 22. Definition of Done

A module is considered complete only when:

- [ ] Code compiles.
- [ ] No obvious force unwrap/crash path remains.
- [ ] Loading states are handled.
- [ ] Errors are handled.
- [ ] Empty states are handled.
- [ ] UI is connected to the correct ViewModel.
- [ ] Service layer is separated from UI.
- [ ] Relevant unit/UI tests are added.
- [ ] Code has been reviewed by at least one other team member.
- [ ] Feature branch merges cleanly.
- [ ] Feature works on a clean Xcode build.

---

# 23. Final Integration Checklist

### Pritosh — Final Owner

- [ ] Pull all team branches.
- [ ] Resolve merge conflicts.
- [ ] Verify project builds from a clean build folder.
- [ ] Verify package dependencies.
- [ ] Verify Secrets configuration.
- [ ] Verify navigation.
- [ ] Verify authentication-to-home flow.
- [ ] Verify weather flow.
- [ ] Verify emergency-services flow.
- [ ] Verify report submission.
- [ ] Verify My Reports.
- [ ] Verify profile.
- [ ] Run unit tests.
- [ ] Run UI tests.
- [ ] Test on physical iPhone/simulator.
- [ ] Prepare final demonstration.

### Prayag

- [ ] Weather module complete.
- [ ] Location permission flow complete.
- [ ] Geocoding tested.
- [ ] Emergency service search tested.
- [ ] Map/location failure states tested.

### Rahul

- [ ] Login tested.
- [ ] Signup tested.
- [ ] Logout/session tested.
- [ ] Supabase auth tested.
- [ ] Report creation tested.
- [ ] My Reports tested.
- [ ] Profile tested.
- [ ] Database error handling tested.

---

# 24. Suggested Lab Demonstration Workflow

For the final iOS lab demonstration, use this order:

```text
1. Introduce project
        ↓
2. Introduce team and mentor
        ↓
3. Explain architecture
        ↓
4. Show authentication
        ↓
5. Show Home screen
        ↓
6. Demonstrate current weather
        ↓
7. Demonstrate location/emergency services
        ↓
8. Demonstrate emergency information
        ↓
9. Submit an emergency report
        ↓
10. Show My Reports
        ↓
11. Show profile
        ↓
12. Explain Supabase backend
        ↓
13. Explain testing
        ↓
14. Explain individual responsibilities
```

---

# 25. Team Presentation Roles

### Pritosh — Project Lead

Explain:
- Overall problem statement.
- Architecture.
- Xcode project structure.
- Navigation/integration.
- Supabase integration overview.
- Testing and final build.

### Prayag — Weather & Location

Explain:
- Weather API workflow.
- Location permissions.
- Geocoding.
- Maps/emergency-service search.
- Error handling for location/network operations.

### Rahul — Authentication & Emergency Reports

Explain:
- Login/signup.
- Supabase authentication.
- Emergency report creation.
- Database interaction.
- My Reports/profile.

### Mentor

**Anjali Srivastava**

---

# 26. Responsibility Summary

The cleanest responsibility structure for the three-student team is:

```text
                    Anjali Srivastava
                         Mentor
                            │
                            ▼
                    ┌──────────────┐
                    │    Pritosh   │
                    │ Project Lead │
                    └──────┬───────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      ┌──────────────┐            ┌──────────────┐
      │    Prayag    │            │    Rahul     │
      │ Weather +    │            │ Auth +       │
      │ Location +   │            │ Reports +    │
      │ Maps         │            │ Backend      │
      └──────────────┘            └──────────────┘
```

**Final answer to "who takes responsibility?"**

> **Pritosh (2502221530143) is the overall project responsible/lead.**  
> **Prayag (2502221530141) owns Weather, Location, Geocoding and Emergency Services/Maps.**  
> **Rahul (2502221530148) owns Authentication, Supabase user/report operations, Emergency Reporting, My Reports and Profile.**  
> **Anjali Srivastava is the project mentor.**

This division gives every student a substantial technical contribution while keeping one person accountable for final integration and submission.

---

# 27. Technical Stack Summary

| Layer | Technology |
|---|---|
| Platform | iOS |
| IDE | Xcode |
| Language | Swift 5 |
| UI | SwiftUI-oriented architecture |
| Minimum OS | iOS 16.0 |
| Architecture | View + ViewModel + Service |
| Backend | Supabase |
| Backend SDK | Supabase Swift 2.49.0 |
| Authentication | Supabase Auth |
| Database | Supabase/PostgreSQL |
| Location | Core Location |
| Maps/Search | Apple map/location services |
| Weather | Weather service/API |
| Geocoding | Geocoding service |
| Testing | XCTest / XCUITest targets |
| Dependency Management | Swift Package Manager |
| Configuration | `.plist` secrets/configuration |
| Source Control | Git (recommended) |

---

## Conclusion

The project is best managed as a **three-module team project with Pritosh as the integration lead**.

The division is intentionally based on the existing Xcode project structure:

- **Pritosh:** architecture, integration, Home, final build, testing coordination.
- **Prayag:** weather, location, geocoding, maps and emergency-service discovery.
- **Rahul:** authentication, Supabase user/report functionality, emergency reporting, My Reports and profile.
- **Anjali Srivastava:** mentor.

This structure makes the contribution of all three students technically demonstrable during an iOS lab evaluation while maintaining clear ownership of each subsystem.
