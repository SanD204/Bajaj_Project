
# Mutual Followers Challenge

A Spring Boot application designed to interact with a remote API upon startup, process specific social graph problems, and submit results using secure webhooks.

## Key Highlights

- Automatically triggers a POST request to the `/generateWebhook` endpoint on startup  
- Dynamically solves one of two problems depending on the registration number:
  - **Odd regNo**: Detects mutual follower relationships
  - **Even regNo**: Determines nth-level connections
- Posts results to a webhook using JWT-based authorization
- Built-in retry mechanism with up to 4 attempts for reliable delivery

## Workflow Overview

1. When the application starts, it initiates a request to fetch webhook information.
2. The server response includes a target webhook URL, access token, and a data payload.
3. The app identifies which task to execute based on the registration number's parity (odd/even).
4. After solving the assigned problem, it formats the output as required.
5. The final response is posted to the webhook with the appropriate JWT token in the request header.

## Problem Definitions

### Task 1: Mutual Followers (For Odd regNo)

Finds all user pairs that follow each other. Each pair is presented as `[min, max]` to maintain consistency.

**Example Output:**
```json
{
  "regNo": "REG12347",
  "outcome": [[1, 2], [3, 4]]
}
```

### Task 2: Nth-Level Followers (For Even regNo)

Starting from a given user ID, traverses the graph to return all users who are exactly `n` levels deep in the follower chain.

**Example Output:**
```json
{
  "regNo": "REG12348",
  "outcome": [4, 5]
}
```

## Requirements

- Java 17 or newer  
- Maven 3.6+

## How to Run

Compile and launch the application using the following commands:

```bash
mvn clean install
mvn spring-boot:run
```

On startup, the app will:
1. Contact the webhook service
2. Solve the designated problem
3. Submit the output securely

## Configuration Settings

Set the following properties in `application.properties`:

```properties
# Base API endpoint and registration details
api.base-url=https://bfhldevapigw.healthrx.co.in/hiring
api.registration.name=John Doe
api.registration.regNo=REG12347
api.registration.email=john@example.com

# Retry settings for webhook submission
spring.retry.max-attempts=4
spring.retry.initial-interval=1000
spring.retry.multiplier=1.5
spring.retry.max-interval=10000
```
