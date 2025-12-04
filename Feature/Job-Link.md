# Table of Contents
- [Overview](#overview)
- [Terminology](#terminology)
- [Background](#background)
- [User Experience](#user-experience)
  - [Login Screen](#login-screen)
  - [Registration Screen](#registration-screen)
  - [File Upload Screen](#file-upload-screen)
  - [File Upload Successful Screen](#file-upload-successful-screen)
  - [Dashboard Screen](#dashboard-screen)
  - [Job Search Screen](#job-search-screen)
- [Security Configurations](#security-configurations)
- [Dependencies](#dependencies)
- [Endpoints](#endpoints)
  - [Retrive Job Postings](retrieve-job-postings)
  - [Upload Document On Home Screen](upload-document-on-home-screen)
  - [Get Job Posting Count](get-job-posting-count)
- [Business Logic](#business-logic)
  - [Authentication Flow](#authentication-flow)
  - [Backend Service Flow](#backend-service-flow)
- [Known Issues](#known-issues)
- [Test Data Creation](#test-data-creation)

# Overview
The Job Portal application (Job Link) is a web interface that allows users to upload their resumes and apply for jobs matching their qualifications. Once a document is uploaded to the platform, they are given access to analytics to track job applications sent and interviews scheduled.

# Terminology
- JWT: JSON Web Token

# Background
Whenever we need to look for another role due to being laid off or simply exploring other opportunities, the job portal application is a place to track your application status. Users can apply to as many applications as they desired while keeping a log of application were sent. The Job Portal web interface allows a centralized platform to help monitor your application status within your profile.

# User Experience
  ## Login Screen
  Users are navigated to the login page. They are presented with an email and password field including "Sign In" and "Sign Up" buttons.

  ![job-portal-login](../.attachments/JobPortalLogin.png =700x)

  ## Registration Screen
  If a user needs to create an account, they can do so by clicking the "Sign Up" button where they are navigated to the Sign Up form screen. This is where users are required to add their credentials to create an account.

  ![job-portal-signup](../.attachments/JobPortalSignup.png =700x)

  ## File Upload Screen
  Once a user logs into the web interface successfully, the user is presented with a screen that contains a sidebar with links to the dashboard and job search search screens. The side bar component also has help and setting links at the footer of the screen. Headers can profile image with the name of the user next to the image. In the main screen, there is a component with a icon inside a dotted box to upload a document. A user can upload a pdf or text document that can be stored into the application.

  ![job-portal-file-upload](../.attachments/JobPortalFileUpload.png =700x)

  ## File Upload Successful Screen
  Once a user successfully uploads a document into the application, the icon and the content inside the dotted box is replaced with a check mark, confirming the document has been successfully uploaded. There is a message displayed underneath the box and a button displayed as "View Jobs" indicating that the file has been uploaded into the interface.

  ![job-portal-file-upload-successful](../.attachments/JobPortalFileUploadSuccess.png =700x)

  ## Dashboard Screen
  The dashboard screen provides a comprehensive line chart that includes analytics on the amount of applications are sent, interviews scheduled, and job posting available.

  ![job-portal-dashboard](../.attachments/JobPortalDashboard.png =700x)

  ## Job Search Screen
  The job search link provides a list of job posting for users to apply to. The user can simply click the "Apply Now" button and navigate to the company's career page to the role that is listed on a user's profile. There is also a search bar above the list of roles to search for a specific posting.

  ![job-portal-search](../.attachments/JobPortalSearch.png =700x)

# Security Configurations
- The application is utilizing spring security to authenticate users when they log into the application. Once a user registers and creates a profile in the application, their user credentials are generated with a new generated token and saved in the database. A JWT token is generated and the user can successfully log into the app.
- The OAuth 2.0 is utilized to authenticate and make client calls to the API to retrieve job listings.

# Dependencies
- At the moment, the application depends on the Job Board API to retrieve job postings to display them in the job search screen.

# Endpoints
  ## Login
  - Enter credentials to log into the web page.

  - **Request**
    - POST https://job-portal-service/portal/login
    - Body:
      ```
      {
        "email": "test.user@dev.com"
        "password": "mySecuredPassword"
      }
      ```
  - **Response**
    - Status code: 200
    ```
    Test User has successfully logged in.
    ```
  ## Registration
  - Enter user data to complete registration before logging in.

  - **Request**
    - POST https://job-portal-service/portal/register
    - Body:
      ```
      {
        "firstName": "Test"
        "lastName": "User"
        "email": "test.user@dev.com"
        "password": "mySecuredPassword"
      }
      ```
  - **Response**
    - Status code: 200
    ```
    Test User has successfully completed registration.
    ```
  ## Retrieve Job Postings
  - Retrieve a list of job postings and display the them in the job search screen.

  - **Request**
    - GET https://job-portal-service/portal/jobs

  - **Response**
    - Status code: 200
    ```
    "postings": [
      {
        "id": 1345
        "jobTitle": "Frontend Engineer",
        "jobUrl": "https://www.indeed.com/pagead/frontend%engineer,
        "companyLogo": "https://d2q79iu7y748jz.cloudfront.net/s/_squarelogo/256x256/805b99f2c7caa3e66191a79b1e47d418"
        "companyName": "Intellistack",
        "companyAddress": "Moorepark, CA 93021",
        "companySalary": "$125,000 - $130,000 a year",
        "companyMetadata": ["Full-time", "401(k)","Paid time off"],
        "datePosted": "4 days ago"
      },
      {
        "id": 1346
        "jobTitle": "Senior Software Engineer",
        "jobUrl": "https://www.indeed.com/pagead/senior%software%engineer,
        "companyLogo": "https://d2q79iu7y748jz.cloudfront.net/s/_squarelogo/256x256/805b99f2c7caa3e66191a79b1e47d418"
        "companyName": "Netflix",
        "companyAddress": "Los Angeles, CA 90001",
        "companySalary": "$167,000 - $210,000 a year",
        "companyMetadata": ["Full-time", "401(k)","Paid time off", "Health Insurance],
        "datePosted": "3 days ago"
      },
      {
        "id": 1347
        "jobTitle": "Full Stack Engineer",
        "jobUrl": "https://www.indeed.com/pagead/full%stack%engineer,
        "companyLogo": "https://d2q79iu7y748jz.cloudfront.net/s/_squarelogo/256x256/805b99f2c7caa3e66191a79b1e47d418"
        "companyName": "Snap, Inc.",
        "companyAddress": "Santa Monica, CA 92001",
        "companySalary": "$155,000 - $175,000 a year",
        "companyMetadata": ["Full-time", "401(k)","Benefits" ],
        "datePosted": "10 days ago"
      }
    ]
    ```
  ## Upload Document On Home Screen
  - **Request**
    - POST https://job-portal-service/portal/upload
    - Body:
      ```
      "file": MultipartFile
      ```
  - **Response**
    - Status code: 204

  ## Get Job Posting Count
  - **Request**
    - GET https://job-portal-service/portal/count
  - **Response**
    - Status code: 200
      ```
      400
      ```
# Business Logic
  ## Authentication Flow
  - As described briefly earlier in the wiki, there is a simple authentication process implemented into the login flow that validates a users credentials through generated tokens. Once a user attempts to log in, the authentication process starts by validating the user's password then creates a generated token and saves it in the database. Once the system confirms that the token saved succesfully in the database, a JWT token is generated and sent to the user to confirm a successful login. If the system discovers that the credentials entered is not valid, an error message is sent to the user and a 400 reponse is returned.

  ![job-portal-auth-flow](../.attachments/JobPortalAuthFlow.png =500x)

  ## Backend Service Flow
  - The Job Portal service holds the logic and routes for fetching job listings and sends the data to the frontend. The service makes client calls to the Job Board API, so the service can fetch and store the listings in the database for retrieval. The service also contains the job-portal-service/portal/upload endpoint to upload documents to Google Storage. Once the document uploads successfully to the storage platform, the resume metadata service, which lives in the Job Board API, scrapes the required meta data from the document to make calls to the api/job-portal endpoint. The api/job-portal returns the job postings and service calls are performed to retrieve and store in the loacal database.

  ![job-portal-service-flow](../.attachments/JobPortalServiceFlow.png =500x)

  ### Job Board API
  - The Job Board API contains the CRUD methods to interact with job board data. The Job Portal service makes client calls to the API to retreive updated job board data.

  #### Job Board API Data Model

  ![job-board-api-data-model](../.attachments/JobBoardAPIDataModel.png =300x)

  The model demonstrates that the job board data is stored in the job_postings table in the database at the API level. The service makes client calls to the API to display the data in the UI.

# Known Issues
- N/A
# Test Data Creation
- TBD
