# Koii Task Funder: Slack-Powered Blockchain Task Funding Service

## Project Overview

A specialized Express.js application designed to automate task funding via Slack integration for the Koii Network. This service provides a secure, streamlined mechanism for funding blockchain tasks directly through Slack commands.

### Key Features
- Slack-based task funding interface
- Secure request verification using HMAC signatures
- Support for both standard and KPL (Koii Programmable Liquidity) task funding
- User access control with predefined user ID whitelist
- Comprehensive error handling and response mechanisms

### Core Functionality
The application enables authorized users to fund Koii Network tasks by sending specific Slack commands. It verifies the request's integrity, validates the user's permissions, and executes the task funding process using the Koii task CLI.

### Technical Highlights
- Integrates with Slack's webhook system
- Utilizes cryptographic request verification
- Supports multiple blockchain token types
- Provides real-time funding status updates via Slack
- Runs on a configurable Express.js server

## Getting Started, Installation, and Setup

### Prerequisites

- Node.js (latest version recommended)
- npm (Node Package Manager)

### Quick Start

To quickly get started with the project:

```bash
# Clone the repository
git clone <repository-url>

# Navigate to the project directory
cd auto-funder-express

# Install dependencies
npm install

# Copy the example environment file
cp .env.example .env

# Edit the .env file with your specific configuration
# Add your funder keypair and signing secret

# Start the application
npm start
```

### Installation

#### Local Development

1. Ensure you have Node.js and npm installed on your system.

2. Clone the repository:
   ```bash
   git clone <repository-url>
   cd auto-funder-express
   ```

3. Install project dependencies:
   ```bash
   npm install
   ```

4. Configure Environment Variables:
   - Copy the `.env.example` file to `.env`
   - Open the `.env` file and set the following variables:
     - `funder_keypair`: Your funding keypair
     - `SIGNING_SECRET`: Your signing secret

#### Docker Deployment

1. Ensure Docker is installed on your system.

2. Build the Docker image:
   ```bash
   docker build -t auto-funder-express .
   ```

3. Run the Docker container:
   ```bash
   docker run -p 3000:3000 --env-file .env auto-funder-express
   ```

### Configuration

The application uses environment variables for configuration. Modify the `.env` file to customize:
- `funder_keypair`: Set your funding keypair
- `SIGNING_SECRET`: Set your signing secret

### Running the Application

#### Development Mode
```bash
npm start
```

#### Production Considerations
- For production, use the Docker container
- Ensure all environment variables are securely configured
- Use a process manager like PM2 for Node.js applications

### Troubleshooting

- Verify all dependencies are correctly installed
- Check that your `.env` file is properly configured
- Ensure Node.js and npm are up to date

## API Documentation

The application provides a single API endpoint for funding tasks via a Slack integration.

#### Endpoints

##### `/fundtask`
- **Method**: POST
- **Authentication**: Slack Request Verification
- **Request Parameters**:
  - `text`: A string containing the Task ID and Funding Amount
  - `response_url`: Slack response webhook
  - `user_id`: Slack user identifier

#### Request Requirements
- Only specific user IDs are authorized to fund tasks
- Request must include a valid Slack signature
- Must provide a Task ID and funding amount in the request body

#### Example Request
```
POST /fundtask
Content-Type: application/x-www-form-urlencoded
X-Slack-Signature: v0=<signature>
X-Slack-Request-Timestamp: <timestamp>

text=TASK_ID 100
response_url=https://hooks.slack.com/example
user_id=U06NM9A2VC1
```

#### Response
- **Success Response**: 
  - Status Code: 200
  - Body: "Request received and verified integrity."
  - Slack message confirming task funding

- **Error Responses**:
  - 400 Bad Request (Invalid signature or unauthorized user)
  - Slack message indicating failure reason

#### Notes
- Funds can be transferred to both standard and KPL (Koii Programming Library) tasks
- Requires environment variables for authentication and funding

## Authentication

### Authentication Mechanism

The application uses Slack Request Verification for authentication, implementing a secure request validation process:

#### Verification Process
- Requests are authenticated using HMAC-SHA256 signature verification
- Two critical headers are required:
  - `x-slack-signature`: A cryptographic signature of the request
  - `x-slack-request-timestamp`: Timestamp of the request

#### Security Features
- Replay attack prevention through timestamp validation
  - Requests older than 5 minutes are automatically rejected
- Constant-time signature comparison to prevent timing attacks
- User whitelist for additional access control

#### Authentication Steps
1. Generate a signature using the request body and a secret key
2. Compare the generated signature with the provided Slack signature
3. Verify the request timestamp is within the acceptable time window
4. Check if the user is in the predefined user ID list

#### Required Environment Variables
- `SIGNING_SECRET`: Secret key used for request signature verification
- `funder_keypair`: Keypair used for task funding operations

**Note**: Secure management of these secrets is crucial for maintaining the application's security.

## Deployment

### Docker Deployment

The project includes a Dockerfile for containerized deployment. To deploy using Docker:

1. Ensure Docker is installed on your system
2. Build the Docker image:
   ```bash
   docker build -t auto-funder-service .
   ```
3. Run the container, mapping the internal port 3000:
   ```bash
   docker run -p 3000:3000 -e SIGNING_SECRET=your_secret auto-funder-service
   ```

### Environment Configuration

The service requires environment variables for configuration. Create a `.env` file based on the `.env.example` with the following variables:
- `funder_keypair`: Cryptographic keypair for funding operations
- `SIGNING_SECRET`: Secret key for authentication and signing

### Scaling Considerations

#### Container Scaling
- The application can be horizontally scaled using container orchestration platforms like Kubernetes
- Recommended minimum resources:
  - CPU: 1 core
  - Memory: 512MB

#### Production Deployment
- Use a process manager like PM2 for managing Node.js application instances
- Consider implementing load balancing for high-traffic scenarios

### Cloud Platform Deployment

Compatible with major cloud platforms:
- AWS Elastic Beanstalk
- Google Cloud Run
- Azure App Service

Ensure environment variables are properly configured in the cloud platform's settings.

## Project Structure

The project is a Node.js Express application designed for task funding through a Slack integration. The key files and their purposes are as follows:

### Root Directory
- `index.js`: The main application entry point, containing the core Express server logic and task funding functionality
- `package.json`: Defines project dependencies, scripts, and metadata
- `package-lock.json`: Locks dependency versions for consistent installations
- `.env.example`: Template for environment configuration
- `Dockerfile`: Containerization configuration for the application
- `.gitignore`: Specifies files and directories to be ignored by version control

### Key Application Components
- **Server Configuration**: Implemented in `index.js`, the application sets up an Express server listening on port 3000
- **Slack Integration**: Includes a `/fundtask` route with Slack request verification
- **Task Funding Mechanisms**: 
  - `generic_fund_task()`: Handles both standard and KPL (Koii Protocol Layer) task funding
  - `fund_a_task()`: Funds standard tasks
  - `fund_a_KPL_task()`: Funds KPL-specific tasks

### Configuration and Security
- Utilizes environment variables for sensitive information
- Implements Slack request signature verification
- Supports funding tasks on the Koii testnet

The project follows a simple, flat file structure typical of small to medium-sized Node.js applications, with all core functionality centralized in the `index.js` file.

## Technologies Used

### Backend
- **Node.js**: Primary runtime environment
- **Express.js**: Web application framework for building the API

### Libraries and Dependencies
- **Axios**: HTTP client for making external API requests
- **Dotenv**: Environment variable management
- **Crypto**: Native Node.js cryptography module

### Development and Build Tools
- **npm**: Package management and dependency resolution

### Containerization
- **Docker**: Containerization platform for consistent deployment

### Runtime Environment
- **Node.js Latest**: Container base image for application deployment

## Additional Notes

### Security Considerations

The application implements several security measures:

- **Request Verification**: Incoming Slack requests are verified using a time-based signature check to prevent replay attacks.
- **User Whitelist**: Only specific user IDs are authorized to trigger task funding operations.
- **Secure Environment**: Sensitive information like keypairs and signing secrets are managed through environment variables.

### Slack Integration

The application provides a Slack-based interface for funding tasks:
- Requires a specific Slack user ID to be whitelisted
- Uses a `/fundtask` endpoint that accepts task ID and funding amount
- Provides real-time feedback through Slack response URLs

### Task Funding Mechanism

Supports two primary funding methods:
- Standard task funding for non-KPL (Koii Program Library) tasks
- KPL-specific task funding with token type support

### Environment Configuration

Critical configuration parameters are managed through environment variables:
- `funder_keypair`: Funding wallet's secret key
- `SIGNING_SECRET`: Used for Slack request verification

### Error Handling

The application includes comprehensive error handling:
- Validates request signatures
- Checks user authorization
- Provides detailed error messages via Slack
- Handles both standard and KPL task funding scenarios

### Network Compatibility

Connects to the Koii testnet network (`https://testnet.koii.network`) for task interactions

## Contributing

We welcome contributions to this project! By participating, you help improve and grow this community effort.

### Contribution Process

1. **Fork the Repository**: Create your own fork of the project.
2. **Create a Branch**: Make a new branch for your feature or bugfix.
3. **Make Changes**: Implement your changes following the project's coding standards.
4. **Commit Guidelines**:
   - Use clear, descriptive commit messages
   - Keep commits focused and atomic
   - Include relevant context in your commit description

### Development Setup

- Ensure you have Node.js installed
- Use the `.env.example` as a template for your local environment configuration
- Install dependencies with `npm install`

### Code Standards

- Follow existing code style in the project
- Add/update tests for new functionality
- Ensure all tests pass before submitting a pull request

### Submitting a Pull Request

1. Push your changes to your fork
2. Create a pull request to the main repository's `main` branch
3. Provide a clear description of your changes
4. Be prepared to discuss and refine your implementation

### Notes

- Sensitive files like `.env` and `.pem` files are gitignored and should never be committed
- The project uses `npm` for package management
- Currently, no specific test suite is configured (per `package.json`)

Thank you for contributing!

## License

The project is licensed under the ISC License. 

For the full license text, please refer to the standard ISC License terms. The ISC License is a permissive free software license written by the Internet Software Consortium (ISC), functionally equivalent to the MIT and BSD licenses, but with simpler language.

Key characteristics of the ISC License include:
- Allows for free use, modification, and distribution
- Requires preservation of copyright and license notices
- Provides the software "as is" with no warranties

#### Permissions
- Commercial use
- Modification
- Distribution
- Private use

#### Limitations
- No liability
- No warranty