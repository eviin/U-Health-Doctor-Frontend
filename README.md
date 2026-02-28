# doctor-app

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Run Unit Tests with [Vitest](https://vitest.dev/)

```sh
npm run test:unit
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

---------------------------------------------


# User Guide

## 1. System Overview

The Admin (Doctor) Frontend of U-Health provides full administrative control over patients, bookings, treatments, and medical records.

Unlike the User Frontend, which is patient-oriented, this interface is designed for medical professionals and administrative workflows.

### Core Responsibilities

- Patient Management
- Booking & Time Slot Management
- Disease Administration
- Medication Administration
- Treatment Documentation
- Authentication & Security Management
- Real-time Dashboard Statistics


## 2. Authentication & Access Control

### Login
Route:
```sh
/doctor/login
````

- Email and password are validated against the backend.
- On success, an authentication token is stored in LocalStorage.
- The system redirects to /doctor/dashboard.
- Invalid credentials trigger an error message.

### Forgot Password
1. Navigate to the Login page.
2. Click “Forgot password?”
3. Enter your registered email.
4. A reset link is generated (visible in Mailtrap).
5. The link redirects to:
```sh
/password-reset/{token}?email=..
````

The system automatically extracts:
- Reset token (from route parameter)
- Email (from query parameter)
 
A new password can then be securely set.

### Change Password 
Doctors can change their password within the portal.

Requirements:

- Current password verification
- New password confirmation

The system prevents reuse of the existing password.

### Logout
Logout is available in the navigation bar.

- Clears authentication token
- Ends session
- Redirects to Login page

## 3. Doctor Dashboard
Route:
```sh
/doctor/dashboard
````
The Dashboard provides an operational overview of system activity.

##### Displayed Statistics
- Total Patients
- Total Time Slots
- Booked Today
- Free Upcoming Slots

### Booked Today
Displays confirmed bookings where:
- status = 1
- time_slot_start = today

API:
```sh
GET /api/v1/admin/bookings/today
````

### Free Upcoming
Displays future time slots where:
- status = 0
- time_slot_start > now

## 4. Patient Management
Route:
```sh
/doctor/patients
````
#### Features
- View all registered patients
- Search by name
- View detailed patient profile
- Update patient information

#### Patient Detail View

Displays:

- First Name
- Last Name
- Age
- Sex
- Location
- Username
- Email
- Email Verification Status

#### Editing
Doctors can update:
- First Name
- Last Name
- Age
- Sex
- Location

API:
```sh
PUT /api/v1/admin/patients/{id}
```

## 5. Booking Management
Route:
```sh
/doctor/bookings
````

#### Capabilities
- View all bookings (past and future)
- Create new time slots
- Update time slots
- Delete time slots
- Search by patient name
- Search by patient ID

### Booking Status
|Status| Meaning|
|------|--------|
|0     |Free    |
|1     |Booked  |

### API ENDPOINTS
```sh
GET    /api/v1/admin/bookings
GET    /api/v1/admin/bookings/today
GET    /api/v1/admin/bookings/patients?name=
GET    /api/v1/admin/bookings/patients/{id}
POST   /api/v1/admin/bookings
PUT    /api/v1/admin/bookings/{id}
DELETE /api/v1/admin/bookings/{id}
```

## 6. Disease Management
Route:
```sh
/doctor/diseases
```

### Capabilities
- View all diseases
- Search by:
  - Name
  - Description
  - ICD Code
- Create new disease entries
- Update disease entries
- Delete diseases

### Disease Fields
- Disease ID
- Name
- Description
- ICD Code

API:
```sh
GET    /api/v1/admin/diseases
POST   /api/v1/admin/diseases
PUT    /api/v1/admin/diseases/{id}
DELETE /api/v1/admin/diseases/{id}
```

## 7. Medication Management
Route:
```sh
/doctor/medications
```

### Capabilities
- View all medications
- Search by name
- Create medications
- Update medications
- Delete medications

### Medication Fields
Medication ID
Name
Description

API:
```sh
GET    /api/v1/admin/medications
POST   /api/v1/admin/medications
PUT    /api/v1/admin/medications/{id}
DELETE /api/v1/admin/medications/{id}
```

## 8. Treatment Management
Route:
```sh
/doctor/treatments
```

### Creating a Treatment
Required Fields:
- Patient ID
- Diagnosis
- Type of Treatment
- Date of Treatment

Optional:
- Diseases (ICD code or name)
- Medications (with dose and amount)

### Viewing Treatments
Each entry displays:
- Treatment ID
- Patient ID
- Date
- Diagnosis
- Type of Treatment

### Delete Treatment
API:
```sh
DELETE /api/v1/admin/treatments/{treatment_id}
```
The table refreshes automatically after deletion.

## 9. Security & Token Handling
- Token-based authentication
- Tokens stored in LocalStorage
- All protected routes require Authorization headers
- Invalid tokens return 401 Unauthorized
- Password reset flow securely validated

## 10. Technical Architecture
src/

├── services/       # Axios configuration & token interceptor

├── views/doctor/   # Doctor-specific pages

├── router/         # Route definitions

└── App.vue         # Root component

All administrative operations use:
```sh
/api/v1/admin/*
```

The system follows RESTful communication principles.

## 11. Summary
The Doctor Frontend enables:
- Complete administrative control
- Structured patient data management
- Real-time booking oversight
- Secure authentication workflows
- Fully synchronized backend communication

It serves as the operational control center of the U-Health system.

