# Auto Funder: Streamlined Slack-Integrated Task Funding for Koii Network

## Project Overview

Auto Funder is a specialized Express.js application designed for funding Koii Network tasks through a Slack-integrated service. The application provides a secure mechanism for authorized users to fund specific tasks via Slack command interactions.

### Key Features
- Slack integration for task funding requests
- Secure request verification using Slack signatures
- Support for funding both standard and KPL (Koii Provisioning Layer) tasks
- User authorization validation
- Error handling and response tracking

### Core Functionality
The application enables authorized Slack users to fund Koii Network tasks by:
- Receiving funding requests via a Slack slash command
- Verifying the request's authenticity using cryptographic signatures
- Checking user authorization against a predefined list
- Executing task funding on the Koii testnet
- Providing real-time feedback through Slack responses

### Benefits
- Streamlined task funding process
- Enhanced security through request signature verification
- Centralized, controlled task funding mechanism
- Easy integration with Slack workflow
- Support for multiple token types and task funding scenarios

## Getting Started, Installation, and Setup

### Prerequisites

- Node.js (version 16.x or higher)
- npm (Node Package Manager)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo/auto-funder-express.git
   cd auto-funder-express
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure environment variables:
   Copy the `.env.example` file to `.env` and fill in the required values:
   ```bash
   cp .env.example .env
   ```
   Edit the `.env` file and provide values for:
   - `funder_keypair`: Your funding keypair
   - `SIGNING_SECRET`: Your signing secret

### Running the Application

#### Development Mode
To run the application in development mode:
```bash
node index.js
```

#### Production Deployment

##### Using Node.js
```bash
npm start
```

##### Using Docker
1. Build the Docker image:
   ```bash
   docker build -t auto-funder-express .
   ```

2. Run the Docker container:
   ```bash
   docker run -p 3000:3000 auto-funder-express
   ```

### Configuration

Modify the `.env` file to customize your application's configuration. Ensure all required environment variables are set before running the application.

### Notes
- Ensure you have the correct permissions and access keys before deployment
- Keep your `.env` file secure and do not commit it to version control

## API Documentation

The API provides a single endpoint for funding tasks through a Slack integration.

#### Endpoints

##### Fund Task
- **Method:** POST
- **Path:** `/fundtask`
- **Content Type:** `application/x-www-form-urlencoded`

###### Request Parameters
- `text`: A string containing the Task ID and funding amount (space-separated)
- `response_url`: Slack response webhook URL
- `user_id`: Slack user identifier

###### Authentication
- Requires Slack request verification using:
  - `x-slack-signature` header
  - `x-slack-request-timestamp` header
- Only specific user IDs are allowed to use the endpoint

###### Request Example
```
POST /fundtask
Content-Type: application/x-www-form-urlencoded

text=TASK_ID 100
user_id=U06NM9A2VC1
```

###### Successful Response
- Status: 200 OK
- Body: `Request received and verified integrity.`
- Slack notification: `Congrats! @user You funded 100 to task TASK_ID successfully.`

###### Error Responses
- 400 Bad Request: Invalid request signature
- Slack notification for unauthorized users or funding failures

#### Notes
- Supports funding both standard and KPL (Koii Portable Liquidity) tasks
- Funding uses Koii network testnet
- Requires environment variables for Slack signing secret and funder keypair

## Authentication

The application uses Slack request verification for authentication, providing a secure mechanism to validate incoming requests.

### Authentication Mechanism

Authentication is implemented through a Slack request signature verification process that includes several security measures:

- **Signature Verification**: Requests must include a valid Slack signature in the `x-slack-signature` header
- **Timestamp Validation**: Requests are checked to prevent replay attacks by verifying the request timestamp
- **Allowed User List**: Only specific Slack user IDs are permitted to perform certain actions

#### Verification Process

1. The server checks the request timestamp to ensure it's within a 5-minute window
2. A cryptographic HMAC-SHA256 signature is generated using a secret key
3. The generated signature is compared against the provided Slack signature
4. A constant-time comparison is used to prevent timing attacks
5. User ID is validated against a predefined list of allowed users

#### Security Considerations

- Requests older than 5 minutes are automatically rejected
- A secret signing key is required for signature generation
- Only whitelisted Slack user IDs can trigger specific actions

#### Authentication Headers

Required headers for authenticated requests:
- `x-slack-signature`: Cryptographic signature of the request
- `x-slack-request-timestamp`: Timestamp of the request
- Requests must use `application/x-www-form-urlencoded` content type

## Deployment

### Docker Deployment

The project supports containerized deployment using Docker. To deploy the service:

1. Ensure Docker is installed on your target system
2. Build the Docker image:
   ```bash
   docker build -t project-image .
   ```
3. Run the container:
   ```bash
   docker run -p 3000:3000 -e SIGNING_SECRET=your_secret project-image
   ```

### Environment Configuration

The service requires environment variables for proper configuration. Use the `.env.example` as a template to set up your environment:

- `funder_keypair`: Specify the keypair for funding operations
- `SIGNING_SECRET`: Set a secure secret for signing

### Deployment Considerations

#### Port Configuration
- The service is configured to run on port 3000
- Expose and map this port when deploying in containerized or cloud environments

#### Scaling
- The current Docker configuration supports single-instance deployment
- For horizontal scaling, consider:
  - Using container orchestration platforms like Kubernetes
  - Implementing load balancing
  - Managing stateless service design

### Production Recommendations

- Always use environment-specific configurations
- Secure and rotate the `SIGNING_SECRET`
- Implement proper logging and monitoring
- Use volume mounts for persistent data if required

## Technologies Used

### Backend
- **Node.js**: Primary runtime environment for the application
- **Express.js**: Web application framework for building the API

### Libraries and Dependencies
- **axios**: HTTP client for making external API requests
- **dotenv**: Environment variable management
- **crypto**: Built-in Node.js cryptography module

### Development and Deployment
- **Docker**: Containerization platform for application deployment
- **npm**: Package management and dependency resolution

### Blockchain and Web3
- **@_koii/create-task-cli**: Koii Network task development CLI

## Additional Notes

### Security Considerations

- The application implements secure Slack request verification using cryptographic techniques:
  - Prevents replay attacks by checking request timestamps
  - Uses constant-time comparison to mitigate timing attacks
  - Requires a valid Slack signing secret for request authentication

### Slack Integration Specifics

- The application is designed to work with a predefined list of authorized Slack user IDs
- Only users in the `user_id_list` can trigger task funding operations
- Responses are sent back to Slack via a webhook response URL

### Funding Mechanism

- Supports two types of task funding:
  1. Standard task funding
  2. Koii Portable Liquidity (KPL) task funding
- Dynamically determines funding method based on the task's token type
- Requires a funder keypair for executing funding transactions

### Environment Configuration

- Critical credentials are managed through environment variables
- Required environment variables:
  - `funder_keypair`: Keypair for funding tasks (must be a JSON-formatted array)
  - `SIGNING_SECRET`: Secret used for Slack request verification

### Network Interactions

- Connects to Koii testnet at `https://testnet.koii.network`
- Uses confirmed transaction commitment level
- Interacts with Koii task management infrastructure via `@_koii/create-task-cli`

### Logging and Error Handling

- Provides detailed console logging for funding operations
- Sends descriptive error messages back to Slack when funding fails
- Captures and reports both technical and user-facing error scenarios

## Contributing

We welcome contributions from the community! Here's how you can help improve the project:

### Contribution Process

1. Fork the repository
2. Create a new branch for your feature or bugfix
3. Make your changes
4. Ensure all tests pass
5. Submit a pull request

### Development Setup

- Requires Node.js and npm
- Clone the repository
- Run `npm install` to install dependencies
- Use `.env.example` as a template for environment configuration

### Code Guidelines

- Follow existing code style in the project
- Write clear, concise commit messages
- Include comments for complex logic
- Ensure code is well-formatted

### Reporting Issues

- Use GitHub Issues to report bugs or suggest enhancements
- Provide detailed information about the issue
- Include steps to reproduce, expected behavior, and actual results

### Pull Request Process

- Describe the purpose of your changes in the PR description
- Link any related issues
- Ensure all CI checks pass
- Wait for code review and approval

### Development Environment

- Recommended: Latest LTS version of Node.js
- Use `.gitignore` to avoid committing sensitive files
- Respect the existing project dependencies

### Notes

- All contributions are subject to review
- By contributing, you agree to the project's license terms

## License

This project is licensed under the ISC License. 

#### License Details
The ISC License is a permissive free software license written by the Internet Systems Consortium (ISC). It is functionally equivalent to the MIT license, with language simplified to be more legally precise.

Key characteristics of the ISC License include:
- Allows commercial use
- Allows modification
- Allows distribution
- Provides a warranty disclaimer
- Requires preservation of copyright and license notices

For the full license text, please refer to the standard ISC License terms.