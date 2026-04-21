# 🐾 PawSchedule

> A mobile application for managing pet veterinary appointments, built with **Jetpack Compose** and **Kotlin** for Android.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [API Integration](#api-integration)
- [Screens](#screens)
- [Running Tests](#running-tests)
- [Authors](#authors)

---

## Overview

PawSchedule is an Android application designed to help pet owners manage veterinary appointments and keep a digital profile for each of their pets. Users can register, log in, add their pets (with photos and GPS-based home location), schedule appointments, and manage everything from a clean and intuitive interface.

---

## Features

- 🔐 **User Authentication** — Register and log in securely via a REST API backend.
- 🐶 **Pet Management** — Add, view, and delete pets with photos, breed (fetched from external APIs), and GPS home location.
- 📅 **Appointment Scheduling** — Create and manage vet appointments with date picker, time picker, and form validation.
- 🗺️ **GPS Location** — Capture and store the pet's home location using device GPS.
- 🖼️ **Photo Picker** — Attach a photo to each pet from the device gallery.
- 📡 **Real-time Breed Data** — Fetches dog and cat breeds from [TheDogAPI](https://thedogapi.com) and [TheCatAPI](https://thecatapi.com).
- ✅ **Form Validation** — Inline error messages with animated visibility for all input forms.
- 🎨 **Custom Theme** — Consistent design system with a mint, orange, and lilac color palette.
- 🔘 **Bounce Button Animation** — Spring-animated button feedback for improved UX.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Kotlin |
| UI Framework | Jetpack Compose + Material 3 |
| Navigation | Navigation Compose |
| State Management | ViewModel + StateFlow |
| Networking | Retrofit 2 + OkHttp 3 |
| JSON Parsing | Gson |
| Image Loading | Coil |
| Location | Google Play Services Location |
| Animations | Compose Animation API |
| Testing | JUnit 4 |
| Build System | Gradle (Kotlin DSL) |
| Min SDK | API 26 (Android 8.0) |
| Target SDK | API 34 (Android 14) |

---

## Architecture

PawSchedule follows the **MVVM (Model-View-ViewModel)** pattern with a **Repository layer** that acts as the single source of truth for data.

```
UI Layer (Screens / Composables)
        ↓ observes StateFlow
ViewModel Layer (business logic, validation)
        ↓ calls
Repository Layer (data coordination)
        ↓ calls
Network Layer (Retrofit API services)
        ↓ HTTP
Remote Backend (REST API)
```

Data flows **unidirectionally**: the UI reacts to state changes exposed by ViewModels via `StateFlow`. Repositories use `MutableStateFlow` internally and expose immutable `StateFlow` to the rest of the app.

---

## Project Structure

```
app/src/main/java/martinvergara_diegoboggle/pawschedule/
│
├── data/
│   ├── network/
│   │   ├── RetrofitClient.kt          # OkHttp + Retrofit configuration
│   │   ├── PawApiService.kt           # Pets & Appointments endpoints
│   │   ├── AuthApiService.kt          # Register & Login endpoints
│   │   ├── AuthModels.kt              # Request/Response data classes for auth
│   │   └── BreedApiService.kt         # External Dog & Cat breed APIs
│   └── repository/
│       ├── PetRepository.kt           # Pet data source (StateFlow + API calls)
│       ├── AppointmentRepository.kt   # Appointment data source
│       └── UserRepository.kt         # Local user state
│
├── model/
│   ├── Pet.kt                         # Pet data class
│   ├── Appointment.kt                 # Appointment data class
│   ├── AppointmentValidator.kt        # Pure validation logic
│   └── User.kt                        # User data class
│
├── navigation/
│   ├── AppNavigation.kt               # NavHost + route definitions
│   └── AppScreens.kt                  # Sealed class with all screen routes
│
├── ui/
│   ├── screens/
│   │   ├── auth/
│   │   │   ├── LoginScreen.kt
│   │   │   ├── RegisterScreen.kt
│   │   │   └── AuthViewModel.kt
│   │   ├── home/
│   │   │   ├── HomeScreen.kt
│   │   │   └── HomeViewModel.kt
│   │   ├── add_appointment/
│   │   │   ├── AddAppointmentScreen.kt
│   │   │   └── AddAppointmentViewModel.kt
│   │   ├── add_pet/
│   │   │   ├── AddPetScreen.kt
│   │   │   └── AddPetViewModel.kt
│   │   └── pet_profile/
│   │       ├── PetListScreen.kt
│   │       └── PetViewModel.kt
│   ├── theme/
│   │   ├── Color.kt                   # Custom color palette
│   │   ├── Theme.kt                   # MaterialTheme configuration
│   │   ├── Type.kt                    # Typography definitions
│   │   └── Shape.kt                   # Shape definitions
│   └── Animations.kt                  # BounceButton composable
│
└── MainActivity.kt                    # App entry point
```

---

## Getting Started

### Prerequisites

- Android Studio **Hedgehog** or later
- JDK 21
- Android SDK with API 34 installed
- A running instance of the PawSchedule backend (or update the `BASE_URL` in `RetrofitClient.kt`)

### Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-username/PawSchedule.git
   cd PawSchedule
   ```

2. **Configure the backend URL:**

   Open `app/src/main/java/martinvergara_diegoboggle/pawschedule/data/network/RetrofitClient.kt` and update the `BASE_URL` constant to point to your backend server:
   ```kotlin
   private const val BASE_URL = "https://your-backend-url.com/"
   ```

3. **Open in Android Studio** and let Gradle sync the project.

4. **Run the app** on an emulator (API 26+) or a physical device.

### Required Permissions

The following permissions are declared in `AndroidManifest.xml` and may be requested at runtime:

| Permission | Purpose |
|---|---|
| `ACCESS_FINE_LOCATION` | Capturing the pet's home GPS location |
| `ACCESS_COARSE_LOCATION` | Fallback location accuracy |
| `INTERNET` | API calls to backend and breed APIs |
| `ACCESS_NETWORK_STATE` | Network availability checks |

---

## API Integration

### Backend (PawSchedule REST API)

The app communicates with a custom Python backend through the following endpoints:

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/register` | Register a new user |
| `POST` | `/login` | Authenticate an existing user |
| `GET` | `/pets/{owner_id}` | Fetch all pets for a user |
| `POST` | `/pets` | Create a new pet |
| `DELETE` | `/pets/{pet_id}/{owner_id}` | Delete a pet |
| `GET` | `/appointments/{owner_id}` | Fetch all appointments for a user |
| `POST` | `/appointments` | Create a new appointment |
| `DELETE` | `/appointments/{app_id}/{owner_id}` | Delete an appointment |

### External APIs

| API | Base URL | Usage |
|---|---|---|
| TheDogAPI | `https://api.thedogapi.com/v1/` | Fetching dog breed list |
| TheCatAPI | `https://api.thecatapi.com/v1/` | Fetching cat breed list |

---

## Screens

| Screen | Description |
|---|---|
| **Login** | Email and password authentication with error feedback |
| **Register** | New account creation with password confirmation |
| **Home** | List of all scheduled appointments with delete support |
| **Add Appointment** | Form to schedule a new vet appointment with date/time pickers |
| **Pet List** | Gallery of registered pets with circular photo display |
| **Add Pet** | Form to register a new pet with photo, breed dropdown, and GPS location |

---

## Running Tests

The project includes unit tests for the appointment validation logic.

To run all unit tests from the terminal:

```bash
./gradlew test
```

### Test Coverage

**`AppointmentValidatorTest.kt`** covers the following cases for `AppointmentValidator.validateAppointment()`:

| Test | Scenario |
|---|---|
| ✅ `returns TRUE when all data is correct` | All fields valid, terms accepted |
| ❌ `returns FALSE when pet name is empty` | Pet name is blank |
| ❌ `returns FALSE when symptoms are too short` | Symptoms description under 10 characters |
| ❌ `returns FALSE when terms are not agreed` | Terms and conditions checkbox unchecked |

---

## Authors

- **Martín Vergara** — Development
- **Diego Boggle** — Development

---

> PawSchedule — Because every paw deserves great care.
