# E-learning Platform Using MERN

## Overview
This project is a full-stack E-learning platform built using the MERN stack (MongoDB, Express.js, React.js, Node.js). It enables users to enroll in courses, watch educational videos, and manage their learning progress. The platform supports different user roles such as Admin, Teacher, and Student, each with specific functionalities.

## Tech Stack
- **MongoDB**: NoSQL database for storing users, courses, videos, and other data.
- **Express.js**: Web framework for Node.js, used to build RESTful APIs for backend operations.
- **React.js**: Frontend library for building interactive user interfaces.
- **Node.js**: JavaScript runtime for executing backend code and handling server-side logic.
- **Redux**: State management library for React, used to manage global state across the frontend.
- **Tailwind CSS**: Utility-first CSS framework for styling the frontend.

## Backend Structure
The backend is located in the `backend/` directory and consists of:
- **index.js**: Entry point for the backend server. Sets up Express, connects to MongoDB, and configures middleware and routes.
- **db.js**: Handles MongoDB connection logic.
- **middlewares/**: Contains middleware functions for authentication, authorization, and user management.
- **models/**: Mongoose models for Users, Courses, Videos, and Blacklist (for JWT token invalidation).
- **routes/**: Express route handlers for users, courses, and videos. Each route file defines API endpoints for CRUD operations and business logic.

### Backend Workflow
1. **User Authentication**: Users sign up or log in. JWT tokens are issued for session management. Blacklist is used to invalidate tokens on logout.
2. **Role-Based Access**: Middleware checks user roles (Admin, Teacher, Student) to restrict access to certain endpoints.
3. **Course Management**: Admins and Teachers can create, edit, and delete courses. Students can view and enroll in courses.
4. **Video Management**: Videos are associated with courses. Teachers and Admins can upload and manage videos.
5. **User Management**: Admins can manage users, including adding, editing, and removing users.
6. **API Endpoints**: RESTful APIs handle requests for user actions, course enrollment, video streaming, and more.

## Frontend Structure
The frontend is in the `frontend/` directory and consists of:
- **src/**: Main source code for React components, pages, Redux store, and routes.
- **components/**: Reusable UI components for authentication, navigation, course display, etc.
- **Pages/**: Main pages like Landing, Profile, Dashboard, Payment, etc.
- **Redux/**: State management for users, courses, admin, teacher, and product data.
- **routes/**: Route protection and navigation logic for different user roles.
- **asset/**: Static assets like images and videos.

### Frontend Workflow
1. **User Interface**: React components render the UI based on user role and state.
2. **State Management**: Redux manages global state for authentication, courses, and user data.
3. **API Integration**: Frontend communicates with backend APIs to fetch and update data.
4. **Routing**: React Router handles navigation between pages and protects routes based on user roles.
5. **Styling**: Tailwind CSS is used for responsive and modern UI design.

## Project Working (End-to-End)
1. **User Registration/Login**: Users sign up or log in. JWT tokens are used for authentication.
2. **Role Assignment**: Users are assigned roles (Admin, Teacher, Student) which determine their access and UI.
3. **Course Browsing/Enrollment**: Students browse available courses and enroll. Admins/Teachers manage course content.
4. **Video Streaming**: Enrolled students can watch course videos. Teachers/Admins upload and manage videos.
5. **Admin Dashboard**: Admins have access to dashboards for managing users, courses, statistics, and settings.
6. **Teacher Dashboard**: Teachers manage their courses, videos, and student progress.
7. **User Dashboard**: Students view enrolled courses, progress, and profile information.
8. **Payment Integration**: Payment gateway for course enrollment (if implemented).
9. **Logout/Token Blacklisting**: Secure logout by blacklisting JWT tokens.

## Summary
This MERN-based E-learning platform provides a scalable and secure solution for online education. It leverages modern web technologies for a seamless user experience and robust backend operations, supporting multiple user roles and comprehensive course management features.

# Blacklisting, RESTful APIs, API Fetching, and Role Assignment in MERN E-learning Platform

## What is Blacklisting?
Blacklisting in authentication systems refers to the process of invalidating tokens (such as JWTs) so that they can no longer be used for accessing protected resources. In this project, when a user logs out, their JWT token is added to a blacklist collection in the database. Any subsequent request with a blacklisted token is denied, ensuring secure logout and preventing unauthorized access.

**How it is used:**
- On logout, the user's JWT token is stored in the `blacklist` collection (see `backend/models/blacklist.js`).
- Middleware checks incoming requests for blacklisted tokens and blocks them if found.
- This prevents reuse of tokens after logout or when a token is compromised.

## What are RESTful APIs?
RESTful APIs (Representational State Transfer) are a set of rules for building web services that allow communication between client and server using HTTP methods (GET, POST, PUT, DELETE). Each endpoint represents a resource (such as users, courses, or videos) and supports standard operations for creating, reading, updating, and deleting data.

**In this project:**
- The backend (Express.js) exposes RESTful endpoints for users, courses, and videos (see `backend/routes/`).
- Example endpoints:
  - `POST /api/users/login` (user login)
  - `GET /api/courses` (fetch all courses)
  - `POST /api/videos` (add a new video)

## How APIs are Fetched in This Project
The frontend (React.js) communicates with the backend using HTTP requests, typically via the `fetch` API or libraries like `axios`.

**Process:**
1. User interacts with the UI (e.g., clicks a button to enroll in a course).
2. The frontend sends an HTTP request to the backend RESTful API endpoint.
3. The backend processes the request, interacts with the database, and returns a response (JSON data).
4. The frontend updates the UI based on the response.

**Example:**
```js
fetch('/api/courses', {
  method: 'GET',
  headers: { 'Authorization': `Bearer ${token}` }
})
  .then(res => res.json())
  .then(data => setCourses(data));
```

## How Roles are Assigned and Used
Roles (Admin, Teacher, Student) determine what actions a user can perform. Role assignment typically happens during user registration or is set by an Admin.

**Where and How:**
- The user model (`backend/models/users.models.js`) includes a `role` field.
- On registration, a default role (e.g., Student) is assigned. Admins can change roles via the dashboard or API.
- Middleware checks the user's role from their JWT token and restricts access to certain endpoints (see `backend/middlewares/users.middleware.js`).
- Role-based UI: The frontend displays different components and pages based on the user's role.

**Example Role Assignment:**
```js
const user = new User({
  username,
  password,
  role: 'student' // default role
});
```

**Role Check in Middleware:**
```js
if (req.user.role !== 'admin') {
  return res.status(403).json({ message: 'Access denied' });
}
```

---
This document explains blacklisting, RESTful APIs, API fetching, and role assignment in your MERN E-learning platform for interview preparation.
