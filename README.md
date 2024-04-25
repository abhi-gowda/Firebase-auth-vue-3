# Vue.js 3 + Firebase 

This project demonstrates the integration of Firebase with Vue.js for building a dynamic web application. Firebase provides a comprehensive set of tools for building web and mobile applications, including authentication, real-time database, cloud storage, and hosting.

## Prerequisites

Before getting started, ensure you have the following installed:

    Node.js and npm (Node Package Manager)
    Vue CLI (Command Line Interface)

## Getting Started

Follow these steps to get the project up and running:

### 1. Clone the Repository

```sh
git clone https://github.com/abhi-gowda/Firebase-with-vue-3.git
```

### 2. Navigate to the Project Directory

```sh
cd Firebase-with-vue-3
```

### 3. Install Dependencies

```sh
npm install
```

### 4. Configure Firebase

* Create a Firebase project on the Firebase Console
* Add a web app to your Firebase project
* Copy your Firebase project's configuration (apiKey, authDomain, databaseURL, projectId, storageBucket, messagingSenderId, appId) from the Firebase Console and replace the placeholders in src/backend/firebase.js

### 5. Configure Firebase

```sh
npm run serve
```

### 6. View the Application

Open your web browser and navigate to http://localhost:5173 to view the application.

## Present Features

* Authentication: Use Firebase Authentication to sign up, sign in, and manage user accounts.

## Upcoming Features

* Cloud Firestore: Utilize Firebase Cloud Firestore Database to store and sync data.
* Cloud Storage: Store and serve user-generated content with Firebase Cloud Storage.
* Hosting: Deploy the application using Firebase Hosting for fast and secure web hosting.

## Contributing

Contributions are welcome! Feel free to submit pull requests or open issues for any improvements or bug fixes.

## License

This project is licensed under the GNU General Public License (GPL) - see the LICENSE file for details.

## Acknowledgments

Special thanks to Firebase and Vue.js communities for their amazing documentation and support.
