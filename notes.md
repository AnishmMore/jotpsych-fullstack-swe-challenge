# Project Documentation

## Backend Changes

### Models

- **User Model**
  - Updated to include additional fields for `name`, `bio`, `profile_pic_url`, and `motto`.
  - These fields store the user's name, bio, profile picture URL, and encrypted motto.
  - **Encryption**: Integrated the `cryptography` library to encrypt and decrypt the `motto` field.

### Endpoints

#### User Route

- **Path**: `/user`
  - Added to return user information based on JWT identity.
  - Returns a 404 status if the user is not found.
  - Returns the username and other user data if the user is found.

#### Register Endpoint

- **Path**: `/register`
  - Modified to handle additional user data (`name`, `bio`, `profile_pic_url`, `motto`) in the request body.
  - Encrypted the `motto` before storing it in the database.
  - Created a new instance of the `User` model with the additional fields populated from the request data.
  - Committed the new user to the database and generated an access token for the user.

#### Login Endpoint

- **Path**: `/login`
  - Authenticates user credentials and generates an access token upon successful login.
  - Stores the access token in the session.

#### User Endpoint

- **Path**: `/user`
  - Updated to return additional user data (`name`, `bio`, `profile_pic_url`, and decrypted `motto`) in the response JSON.
  - Decrypted the `motto` before sending it in the response.

#### Middleware

- **Version Check**
  - Implemented in `app.before_request` to ensure the `app-version` header is >= 1.2.0.
  - If the version is less, returns a message prompting the user to update their client application.

#### Transcription Endpoint

- **Path**: `/upload`
  - Added to handle the audio file upload.
  - Implemented a function to mock sending the audio to a third-party transcription service and return a transcription.
  - Saved the transcription as the user's `motto` and encrypted it before storing it in the database.

### Asynchronous Endpoints

- Updated relevant endpoints (`/register`, `/login`, `/user`, and `/upload`) to be asynchronous.
- Asynchronous endpoints ensure that multiple users can submit recordings simultaneously without blocking other requests.

## Frontend Changes

### APIService

- **APIService.ts**
  - Updated to include the `app-version` header with every request.
  - Added methods to handle version updates and token storage.
  - Ensured APIService retrieves the token from `localStorage` for authenticated requests.

### Components

#### Register Component

- **Register.tsx**
  - Updated to include input fields for `name`, `bio`, `profilePicUrl`, and `motto`.
  - Added state variables and event handlers for these new fields.
  - Sent these values along with `username` and `password` in the POST request to the `/register` endpoint.

#### Home Component

- **Home.tsx**
  - Updated to fetch and display the user's profile data, including the decrypted `motto`.
  - Handled loading and error states:
    - If the user data is being fetched, displayed a loading spinner.
    - If there is no user data (user not authenticated), displayed a "User not found" message.
  - Displayed the user's profile data using Material-UI components like `Avatar`, `Typography`, and `Container`.

#### Profile Component

- **Profile.tsx**
  - Created to display the user's profile data.
  - Included the Recorder component to allow users to record up to 15 seconds of audio.
  - Fetched the user data from the `/user` endpoint using the access token stored in `localStorage`.
  - Displayed the user's profile picture, name, username, bio, and self-recorded motto using Material-UI components like `Avatar`, `Typography`, and `Container`.

#### Recorder Component

- **Recorder.tsx**
  - Created using the MediaRecorder API to record audio up to a maximum of 15 seconds.
  - Sent the recorded audio blob to the `/upload` endpoint on the backend.
  - Handled recording, stopping, and uploading of the audio file.

### Centralized Authentication Token Management

#### Step 1: Create an Auth Context

- **AuthContext.tsx**
  - Created to manage authentication tokens centrally.
  - Provided methods to set and get the token.

#### Step 2: Provide the Auth Context

- **App.tsx**
  - Wrapped the application with `AuthProvider` to ensure the authentication context is available throughout the app.

#### Step 3: Consume the Auth Context in Components

- **Login.tsx**

  - Updated to set the token in the context after a successful login.
  - Stored the token in `localStorage`.

- **Home.tsx**
  - Updated to fetch user information using the token from the context.

### Summary

- Created `AuthContext.tsx` to manage authentication tokens.
- Wrapped the application with `AuthProvider` in `App.tsx`.
- Updated `Login.tsx` to set the token in the context after login.
- Updated `Home.tsx` to fetch user information using the token from the context.
- Ensured the backend `/user` endpoint returns the necessary user data (`username`, `name`, `bio`, `profile_pic_url`, and decrypted `motto`) in the response.
- Handled storing the access token in `localStorage` after successful login or registration on the client-side.
- Displayed a message in the UI if the server responds with a version update prompt (426 status code).

## Scaling to Enterprise Level

### Database Scaling

- **Horizontal Scaling**: Use a distributed database system like Amazon RDS, Google Cloud SQL, or Azure SQL Database to handle increased load and ensure high availability.
- **Database Sharding**: Split the database into smaller, faster, more easily managed pieces, called shards.

### Load Balancing

- Implement load balancers to distribute incoming traffic across multiple servers, ensuring no single server is overwhelmed. Tools like AWS Elastic Load Balancing (ELB) or NGINX can be used.

### Microservices Architecture

- Break down the monolithic application into microservices. Each service handles a specific function (e.g., user management, authentication, transcription) and communicates with other services via APIs.
- Use containerization (Docker) and orchestration (Kubernetes) to manage these microservices efficiently.

### Caching

- Implement caching strategies using tools like Redis or Memcached to store frequently accessed data in memory, reducing database load and improving response times.

### Asynchronous Processing

- Use message brokers like RabbitMQ or Apache Kafka to handle background tasks and asynchronous processing (e.g., audio transcription), allowing the system to handle multiple requests concurrently without blocking.

### Security

- Implement robust security measures, including OAuth2 for authentication, HTTPS for secure communication, and regular security audits.
- Use environment variables to manage sensitive data and secrets securely.

### CI/CD Pipeline

- Set up a Continuous Integration/Continuous Deployment (CI/CD) pipeline using tools like Jenkins, GitLab CI, or GitHub Actions to automate testing, integration, and deployment processes, ensuring quick and reliable releases.

### Monitoring and Logging

- Use monitoring tools like Prometheus, Grafana, and ELK Stack (Elasticsearch, Logstash, Kibana) to track application performance, detect issues, and analyze logs for debugging and insights.

### Auto-scaling

- Configure auto-scaling groups in cloud providers (AWS, Azure, GCP) to automatically increase or decrease the number of running instances based on the current demand, ensuring optimal resource utilization.

### Documentation and API Management

- Use tools like Swagger or Postman to document APIs, making it easier for developers to understand and use them.
- Implement API gateways (like AWS API Gateway) to manage and monitor API traffic.

By following these steps and using these tools, you can ensure that the application is scalable, resilient, and maintainable at an enterprise level.
