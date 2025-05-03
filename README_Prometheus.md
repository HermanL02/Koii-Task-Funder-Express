# Koii Task Funder: Slack-Integrated Blockchain Task Funding Service

## Project Overview

An automated task funding service built on the Koii Network, designed to simplify and streamline the process of funding tasks through a Slack integration.

### Core Functionality
The application provides a Slack-based interface for funding tasks on the Koii Network, offering a convenient and secure method for task funding. It supports two primary funding mechanisms:
- Standard task funding
- KPL (Koii Programmable Liquidity) task funding

### Key Features
- Slack Command Integration: Allows users to fund tasks directly through Slack commands
- Security-First Design: 
  - Implements robust request verification using Slack's signature verification
  - Prevents replay attacks with timestamp checks
  - Supports constant-time signature comparison
- Multi-Token Support: Can handle both standard and KPL token-based task funding
- User Whitelist: Restricts task funding to pre-approved Slack user IDs
- Error Handling: Provides detailed feedback on funding success or failure

### Technical Capabilities
- Connects to Koii Network's testnet
- Supports dynamic task funding with variable amounts
- Handles different token types and funding scenarios
- Provides real-time response via Slack webhook

### Use Cases
Ideal for blockchain developers, project managers, and teams looking to:
- Quickly fund Koii Network tasks
- Manage task funding through a familiar Slack interface
- Implement secure, controlled task funding mechanisms

## Getting Started, Installation, and Setup

### Prerequisites

- Node.js (version 14 or later)
- npm (Node Package Manager)

### Quick Start

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd <repository-name>
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
   - `SIGNING_SECRET`: A secret key for signing

### Running the Application

#### Development Mode
To run the application in development mode:
```bash
node index.js
```

### Building for Production

The project doesn't have a specific build script. For production deployment, ensure all dependencies are installed:
```bash
npm install --production
```

### Docker Support

A Dockerfile is provided for containerized deployment:

```bash
# Build the Docker image
docker build -t auto-funder-express .

# Run the Docker container
docker run -p 3000:3000 auto-funder-express
```

### Dependencies

Key dependencies include:
- Express.js: Web application framework
- Axios: HTTP client
- Dotenv: Environment variable management
- @_koii/create-task-cli: Koii-specific task CLI

### Troubleshooting

- Ensure all environment variables are correctly set
- Verify Node.js and npm are installed correctly
- Check that all dependencies are installed successfully

## API Documentation

### Endpoints

#### Fund Task
- **Method**: POST
- **Path**: `/fundtask`
- **Authentication**: Slack Request Verification
- **Request Body**: 
  - Content-Type: `application/x-www-form-urlencoded`
  - Required Parameters:
    - `text`: Contains the Task ID and Amount to fund (space-separated)
    - `response_url`: Slack response URL for async feedback
    - `user_id`: Slack User ID

#### Request Requirements
- Request must include a valid Slack signature
- User ID must be in a predefined authorized list
- Task ID and Amount must be provided in the `text` parameter

#### Request Example
```http
POST /fundtask
Content-Type: application/x-www-form-urlencoded
X-Slack-Signature: v0=<generated_signature>
X-Slack-Request-Timestamp: <current_timestamp>

text=TASK_ID 100&user_id=U06NM9A2VC1&response_url=https://slack.com/response
```

#### Response Example
- Success Response:
  ```json
  {
    "response_type": "in_channel",
    "text": "Congrats! <@U06NM9A2VC1> You funded 100 to task TASK_ID successfully."
  }
  ```
- Error Response:
  ```json
  {
    "response_type": "in_channel", 
    "text": "Failed to fund 100 to TASK_ID. <error_details>"
  }
  ```

#### Notes
- The endpoint supports funding both standard and KPL (Koii Program Library) tasks
- Funds are transferred using the predefined funder wallet
- Requires environment variables: `SIGNING_SECRET`, `funder_keypair`

## Authentication

The application uses a Slack-based request verification mechanism for authentication. Authentication is implemented through the following key components:

### Request Signature Verification

Request authenticity is ensured using Slack's request signature verification:
- Signature is verified using an HMAC SHA-256 algorithm
- Requires a `SIGNING_SECRET` environment variable
- Validates two critical aspects of each request:
  1. Request timestamp (prevents replay attacks)
  2. Request signature integrity

#### Verification Process
- Checks if the request timestamp is within a 5-minute window
- Generates a signature base string: `v0:{timestamp}:{request_body}`
- Creates an HMAC signature using the `SIGNING_SECRET`
- Performs a constant-time comparison to prevent timing attacks

### User Authorization
- A predefined list of authorized Slack user IDs controls access
- Only users in the `user_id_list` can perform specific actions
- Unauthorized users receive a message to contact an administrator

### Authentication Requirements
- `SIGNING_SECRET`: A secret key for Slack request verification
- `funder_keypair`: A Keypair used for task funding operations

#### Security Notes
- Always keep `SIGNING_SECRET` and `funder_keypair` confidential
- Rotate secrets periodically
- Use environment variables to manage sensitive credentials

## Deployment

### Docker Deployment

The project supports Docker containerization for easy deployment. To deploy using Docker:

1. Ensure Docker is installed on your target system
2. Build the Docker image:
   ```bash
   docker build -t project-image .
   ```
3. Run the container:
   ```bash
   docker run -p 3000:3000 -e SIGNING_SECRET=your_secret -e funder_keypair=your_keypair project-image
   ```

#### Environment Configuration

The application requires two environment variables:
- `SIGNING_SECRET`: A secret key for authentication
- `funder_keypair`: Keypair configuration for funding operations

Use the `.env.example` file as a template to configure your environment variables securely.

#### Scaling Considerations

The application is containerized and can be scaled horizontally using container orchestration platforms like Kubernetes or Docker Swarm. Consider the following when scaling:
- Use load balancers to distribute traffic
- Implement stateless design to support multiple container instances
- Monitor container resource utilization

#### Recommended Deployment Platforms

- Docker containers
- Cloud platforms with container support (AWS ECS, Google Cloud Run, Azure Container Instances)
- Kubernetes clusters for advanced orchestration

#### Port Configuration

The service runs on port 3000, which should be mapped appropriately during deployment.

## Project Structure

The project is a Node.js Express application designed for funding tasks, with a specific focus on Koii network task funding. The repository contains the following key files:

#### Main Application File
- `index.js`: The core application logic, which sets up an Express server with a `/fundtask` endpoint. It handles Slack request verification and task funding mechanisms, supporting both standard and KPL (Koii Permanent Liquidity) task funding.

#### Configuration and Dependency Management
- `package.json`: Defines project metadata, dependencies, and scripts. Key dependencies include Express, Axios, and Koii task CLI tools.
- `.env.example`: Provides a template for environment variables used in the application.

#### Containerization
- `Dockerfile`: Enables containerized deployment of the application.

#### Version Control
- `.gitignore`: Specifies files and directories to be ignored by Git version control.

#### Dependency Lockfile
- `package-lock.json`: Ensures consistent dependency versions across different development environments.

The project is structured as a compact, focused microservice for task funding with integrated Slack request handling and Koii network interactions.

## Technologies Used

### Programming Language
- JavaScript/Node.js

### Backend Framework
- Express.js (Web application framework)

### Dependencies and Libraries
- axios: HTTP client for making API requests
- dotenv: Environment variable management
- crypto: Built-in Node.js cryptography module

### Development and Build Tools
- npm (Package manager)
- Docker (Containerization)

### Runtime Environment
- Node.js (Latest version)

### Notable SDKs
- @_koii/create-task-cli: Koii Network task development CLI

## Additional Notes

### Security Considerations

The application includes several security measures:
- Slack request verification using HMAC SHA-256 signature validation
- Timestamp-based protection against replay attacks
- Constant-time signature comparison to prevent timing attacks

### User Access Control

The application implements a strict user access list. Only specific Slack user IDs are permitted to use the funding functionality. Unauthorized users receive a message directing them to contact an administrator.

### Environment Configuration

The application relies on environment variables for critical configurations:
- `funder_keypair`: Stores the funding wallet's secret key
- `SIGNING_SECRET`: Used for Slack request signature verification

### Funding Mechanism

The service supports two primary funding mechanisms:
- Standard task funding for non-KPL (Koii Primitive Layer) tasks
- KPL-specific task funding with additional token type handling

### Integration Points

- Slack Webhook Integration: Receives funding requests via Slack slash commands
- Koii Network Testnet: Connects to the Koii testnet for task funding operations

### Limitations

- Currently configured for a specific set of user IDs
- Operates exclusively on the Koii testnet
- Requires manual user ID management

### Monitoring and Logging

Basic console logging is implemented to track:
- Incoming request details
- Funding process start and completion
- Error scenarios during task funding

## Contributing

We welcome contributions to this project! Here are some guidelines to help you get started:

### Contribution Process

1. **Fork the Repository**: Create a fork of the project on GitHub.
2. **Clone Your Fork**: Clone your forked repository to your local machine.
3. **Create a Branch**: Create a new branch for your feature or bugfix.
   ```
   git checkout -b feature/your-feature-name
   ```

### Development Setup

- Ensure you have Node.js installed (compatible with the project's dependencies)
- Install dependencies:
  ```
  npm install
  ```

### Code Guidelines

#### Code Style
- Follow consistent code formatting
- Use meaningful variable and function names
- Write clean, readable, and well-commented code

#### Commit Messages
- Use clear and descriptive commit messages
- Prefix commits with their type (e.g., `feat:`, `fix:`, `docs:`)

### Testing

Currently, no test suite is specified. When contributing:
- Manually test your changes thoroughly
- Ensure no existing functionality is broken

### Pull Request Process

1. Ensure your code follows the project's guidelines
2. Update the README or documentation if your changes require it
3. Open a pull request with a clear description of your changes

### Reporting Issues

- Use the GitHub Issues section
- Provide a clear description of the issue
- Include steps to reproduce, expected behavior, and actual behavior

### Dependencies

Project dependencies include:
- Express.js
- Axios
- Dotenv
- Crypto

### Notes

- All contributions are subject to review
- Be respectful and collaborative
- By contributing, you agree to the project's licensing terms

## License

The project is licensed under the ISC License. 

### License Details

The ISC License is a permissive free software license written by the Internet Software Consortium (ISC). It is functionally equivalent to the MIT license but with simpler language.

Key points of the ISC License:
- Allows commercial and non-commercial use
- Permits modification and distribution
- Requires preservation of copyright and license notices
- Provides the software "as is" with no warranties

For the full license text, please refer to the standard ISC License terms.