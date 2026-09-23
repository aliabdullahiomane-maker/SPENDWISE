# SpendWise

SpendWise is a Kenyan personal finance dashboard that helps users track monthly income, record expenses, and monitor their remaining balance in Kenyan Shillings.

The application uses Google authentication and Firebase Firestore to securely store each user's financial records.

## Features

- Google/Gmail sign-in.
- Personal user accounts.
- Monthly income tracking.
- Expense recording in Kenyan Shillings.
- KES currency formatting.
- Remaining balance calculation.
- Expense records stored in Firebase Firestore.
- Expenses visible for 30 days.
- Expense expiry using an `expiresAt` timestamp.
- Remove expense functionality.
- Dark mode and light mode.
- User settings section.
- Logout functionality.
- Email reminder preparation.
- Responsive design for desktop, tablet, and mobile devices.
- Copyright footer for Ali Omane.

## Technologies Used

- HTML5
- CSS3
- JavaScript
- Firebase Authentication
- Google Authentication
- Cloud Firestore
- Firebase Hosting
- Live Server
- Git and GitHub

## Project Structure

```text
spendwise/
│
├── index.html
├── style.css
├── script.js
├── firestore.rules
└── README.md
```

## Requirements

Before running the project, install or prepare:

- A modern web browser.
- Visual Studio Code.
- VS Code Live Server extension.
- A Google account.
- A Firebase project.
- Firebase Authentication.
- Cloud Firestore.

## Firebase Setup

### 1. Create a Firebase project

1. Open the [Firebase Console](https://console.firebase.google.com/).
2. Create a new Firebase project.
3. Name the project.
4. Register a Web app.
5. Copy the Firebase configuration.

### 2. Enable Google Authentication

In Firebase Console:

```text
Authentication → Sign-in method → Google → Enable
```

Select the project support email and save the settings.

Add the following authorized domains for local development:

```text
localhost
127.0.0.1
```

### 3. Create Firestore

In Firebase Console:

```text
Databases & Storage → Firestore Database → Create database
```

Choose a database location and create the database.

### 4. Add Firestore security rules

Open:

```text
Firestore Database → Rules
```

Replace the existing rules with:

```text
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null
        && request.auth.uid == userId;

      match /expenses/{expenseId} {
        allow read, write: if request.auth != null
          && request.auth.uid == userId;
      }

      match /settings/{settingId} {
        allow read, write: if request.auth != null
          && request.auth.uid == userId;
      }
    }
  }
}
```

Click **Publish**.

These rules ensure that a signed-in user can only read and modify their own income and expenses.

## Currency

SpendWise uses Kenyan Shillings.

The application uses:

```text
KES
```

Currency formatting is handled using:

```javascript
const currencyFormatter = new Intl.NumberFormat("en-KE", {
  style: "currency",
  currency: "KES",
  currencyDisplay: "code",
  minimumFractionDigits: 2,
  maximumFractionDigits: 2
});
```

Examples:

```text
KES 5,000.00
KES 25,500.00
KES 100,000.00
```

## Running the Project Locally

Do not open `index.html` directly using the `file:///` URL.

### Using VS Code Live Server

1. Open the project folder in Visual Studio Code.
2. Install the **Live Server** extension.
3. Right-click `index.html`.
4. Select:

```text
Open with Live Server
```

5. The application will open at an address similar to:

```text
http://127.0.0.1:5500/index.html
```

### Using Python

Open PowerShell inside the project folder and run:

```powershell
py -m http.server 5500
```

Then open:

```text
http://localhost:5500
```

## Using the Application

### Sign in

1. Open the application.
2. Click **Continue with Gmail**.
3. Choose a Google account.
4. The dashboard will open after successful login.

### Set income

1. Enter your monthly income.
2. Click **Update income**.
3. The income will be saved to Firestore.

### Add an expense

1. Enter the expense name.
2. Enter the expense amount.
3. Click **Add expense**.
4. The expense will appear in the Expense Records section.

### Remove an expense

Click the **Remove** button on an expense record.

### Change the theme

1. Open the Settings section.
2. Select **Light mode** or **Dark mode**.
3. Your theme preference is saved in the browser.

### Log out

Click **Log out** in the sidebar.

## Expense Expiry

Each expense is stored for 30 days.

When an expense is added, the application creates an expiration date:

```javascript
const expiryDate = new Date(
  createdDate.getTime() + 30 * 24 * 60 * 60 * 1000
);
```

The expiry time is stored in Firestore using:

```javascript
expiresAt
```

The application only displays expenses whose expiry date has not passed.

For automatic physical deletion from Firestore, create a Firestore TTL policy using:

```text
Collection group: expenses
Timestamp field: expiresAt
```

TTL deletion may occur after the exact expiry time.

## Firestore Data Structure

SpendWise stores data using the following structure:

```text
users/
└── userId/
    ├── expenses/
    │   └── expenseId/
    │       ├── name
    │       ├── amount
    │       ├── currency
    │       ├── createdAt
    │       └── expiresAt
    │
    └── settings/
        └── finance/
            ├── monthlyIncome
            ├── currency
            └── updatedAt
```

## Firebase Configuration

The Firebase configuration is stored in `script.js`.

The configuration includes:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_STORAGE_BUCKET",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Do not add Firebase service-account private keys to the frontend code.

## Troubleshooting

### Google sign-in does not work

Check the following:

- Google Authentication is enabled.
- `localhost` is listed in Authorized domains.
- `127.0.0.1` is listed in Authorized domains.
- The application is running through Live Server.
- The browser is not blocking popups.
- The correct Firebase project configuration is in `script.js`.

### Domain is not authorized

If the browser address is:

```text
http://127.0.0.1:5500
```

add this domain:

```text
127.0.0.1
```

If the browser address is:

```text
http://localhost:5500
```

add this domain:

```text
localhost
```

Do not include the protocol or port number.

### Firestore permission denied

Check that:

- The user is signed in.
- Firestore exists.
- Firestore rules are published.
- The rules use `request.auth.uid`.
- The project ID matches the Firebase configuration.

### Firestore index error

If the browser console shows:

```text
The query requires an index
```

click the Firebase link in the error message and create the required index.

### JavaScript is not loading

Confirm that the bottom of `index.html` contains:

```html
<script type="module" src="script.js"></script>
```

Make sure this script appears only once.

## Deployment

Firebase Hosting can be used to deploy the application.

Install the Firebase CLI:

```powershell
npm install -g firebase-tools
```

Log in:

```powershell
firebase login
```

Initialize Firebase Hosting:

```powershell
firebase init hosting
```

Select:

```text
Use an existing project
```

Choose the SpendWise project.

For the public directory, enter:

```text
.
```

Deploy the application:

```powershell
firebase deploy --only hosting
```

After deployment, add the Firebase Hosting domain to:

```text
Authentication → Settings → Authorized domains
```

## Security Notes

- Users must be authenticated before accessing data.
- Firestore rules restrict users to their own records.
- Do not share private service-account keys.
- Do not publish passwords or secret keys to GitHub.
- Review Firestore rules before launching publicly.
- Use a server-side email service for automatic email delivery.

## Future Improvements

Possible future improvements include:

- Automatic email notifications before expenses expire.
- Expense categories.
- Monthly reports.
- Spending charts.
- Budget limits per category.
- Export expenses to CSV.
- Search and filter expenses.
- Recurring expenses.
- Mobile application using Flutter.
- Firebase Cloud Functions for scheduled notifications.
- Admin dashboard.

## Author

Created by:

```text
Ali Omane
```

## License

Copyright © 2026 Ali Omane.

All rights reserved.

This project is for educational and personal use. Permission is not granted to copy, modify, or redistribute the project without authorization.
