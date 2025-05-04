# Koii Task Auto-Funder: Slack-Powered Blockchain Task Funding Service

## Project Overview

An Express.js-based Slack integration for automated task funding on the Koii Network, designed to streamline blockchain task support through a secure Slack command interface.

### Key Features
- Slack-based task funding mechanism
- Secure request verification using HMAC signatures
- Support for both standard and KPL (Koii Permanent Library) task funding
- User authorization through a predefined user ID whitelist
- Real-time funding status notifications via Slack

### Core Functionality
The application provides a Slack webhook endpoint that allows authorized users to fund Koii Network tasks directly through a Slack command. It handles task funding by:
- Verifying the integrity of incoming Slack requests
- Parsing task ID and funding amount from the command
- Checking user authorization
- Executing task funding on the Koii testnet
- Sending back funding confirmation or error messages to Slack

### Key Benefits
- Simplifies blockchain task funding process
- Provides a user-friendly Slack integration
- Ensures secure and controlled access to funding capabilities
- Supports both standard and KPL token tasks
- Offers immediate feedback on funding operations

## Getting Started, Installation, and Setup

### Prerequisites

- Node.js (version 16 or later)
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
   - Copy the `.env.example` file to `.env`
   - Fill in the required environment variables:
     ```
     funder_keypair = [YOUR_KEYPAIR]
     SIGNING_SECRET = [YOUR_SIGNING_SECRET]
     ```

### Running the Application

#### Development Mode
To run the application in development:
```bash
node index.js
```

#### Production Deployment
For production, you can use the provided Dockerfile:

```bash
# Build the Docker image
docker build -t auto-funder-express .

# Run the Docker container
docker run -p 3000:3000 auto-funder-express
```

### Configuration Notes
- Ensure all required environment variables are set before running the application
- The application uses Express.js as the web framework
- Dependencies include Axios for HTTP requests and dotenv for environment configuration

### Troubleshooting
- Verify Node.js and npm are correctly installed
- Check that all dependencies are installed successfully
- Confirm environment variables are correctly set

## API Documentation

### Available Endpoints

#### Fund Task Endpoint

**Method**: POST

**Path**: `/fundtask`

**Authentication**: 
- Requires Slack request signature verification
- User must be in an approved user ID list

**Parameters**:
- `text`: Contains two space-separated values
  1. First value: Task ID to fund
  2. Second value: Amount to fund
- `response_url`: Slack response URL for sending follow-up messages
- `user_id`: Slack user ID (must be in approved list)

**Request Example**:
```
POST /fundtask
Content-Type: application/x-www-form-urlencoded
X-Slack-Signature: [signature]
X-Slack-Request-Timestamp: [timestamp]

text=TASK_ID_HERE 100&response_url=https://slack.com/response&user_id=U06NM9A2VC1
```

**Possible Responses**:
- 200 OK: Successful task funding
  ```json
  {
    "response_type": "in_channel",
    "text": "Congrats! You funded 100 to task TASK_ID_HERE successfully."
  }
  ```
- 400 Bad Request: Invalid request signature or unauthorized user
  ```json
  {
    "response_type": "in_channel", 
    "text": "Sorry, please tag @authorized_user to add you to the list!"
  }
  ```

#### Notes
- The endpoint supports funding both standard and KPL (Koii Peripheral Layer) tasks
- Funding is performed on the Koii testnet
- Only specific Slack users are authorized to use this endpoint

## Deployment

The service can be deployed using Docker, which provides a consistent and portable deployment environment.

### Docker Deployment

To deploy the service using Docker:

1. Ensure Docker is installed on your target system
2. Build the Docker image:
   ```bash
   docker build -t service-image .
   ```
3. Run the Docker container:
   ```bash
   docker run -p 3000:3000 \
     -e funder_keypair='YOUR_KEYPAIR' \
     -e SIGNING_SECRET='YOUR_SECRET' \
     service-image
   ```

#### Environment Configuration

The service requires two key environment variables:
- `funder_keypair`: Keypair for funding operations
- `SIGNING_SECRET`: Secret used for signing processes

These can be set via environment variables or a `.env` file when deploying.

### Scaling Considerations

The containerized architecture supports horizontal scaling through:
- Multiple container instances
- Load balancing across container replicas
- Stateless design facilitating easy horizontal scaling

Recommended container orchestration platforms include:
- Kubernetes
- Docker Swarm
- Amazon ECS
- Google Cloud Run

#### Port Configuration
- Default exposed port: 3000
- Configurable through Docker port mapping

### Production Deployment Recommendations

- Use a process manager like PM2 for Node.js applications
- Implement robust logging and monitoring
- Configure secure, environment-specific credentials
- Use HTTPS and implement proper network security measures

## Project Structure

The project is a Node.js Express application structured as follows:

#### Root Directory
- `index.js`: Main application entry point, containing the core Express server and route handlers
- `package.json`: Project configuration and dependency management
- `package-lock.json`: Exact dependency versions for consistent installations
- `.env.example`: Template for environment configuration
- `Dockerfile`: Container configuration for deployment
- `.gitignore`: Specifies intentionally untracked files to ignore

#### Key Configuration Files
- `package.json` defines the project's dependencies, including:
  - Express for web server
  - `@_koii/create-task-cli` for blockchain interactions
  - Axios for HTTP requests
  - Crypto for security functions
  - Dotenv for environment variable management

#### Main Application Logic
The `index.js` file serves multiple primary functions:
- Sets up an Express server listening on port 3000
- Implements a `/fundtask` endpoint for task funding
- Includes Slack request verification
- Provides blockchain task funding mechanisms for different token types

#### Security Considerations
- Implements request signature verification
- Uses environment variables for sensitive configurations
- Supports both standard and KPL (Koii Portable Liquidity) task funding

## Technologies Used

### Backend
- **Node.js**: Runtime environment for server-side JavaScript
- **Express.js**: Web application framework for building APIs and web services

### Libraries and Dependencies
- **Axios**: Promise-based HTTP client for making network requests
- **Dotenv**: Environment variable management
- **Crypto**: Node.js built-in cryptography module

### Development and Deployment
- **Docker**: Containerization platform for consistent deployment
- **npm**: Package management and dependency resolution

### Blockchain / Web3
- **@_koii/create-task-cli**: Koii Network development toolkit

## Additional Notes

### Environment Configuration

The project uses environment variables for configuration. Refer to the `.env.example` file for required configuration parameters:
- `funder_keypair`: Appears to be a cryptographic key for funding operations
- `SIGNING_SECRET`: A secret key used for signing purposes

### Docker Deployment

The project includes a Dockerfile for containerized deployment:
- Uses the latest Node.js official image
- Installs production dependencies
- Exposes port 3000 for application access
- Runs the application using `node index.js`

### Dependency Considerations

Key dependencies include:
- Express.js for web server functionality
- Axios for HTTP requests
- Koii Task CLI for potential blockchain-related operations
- Dotenv for environment variable management

### Performance and Scaling

The application is lightweight and can be easily containerized, making it suitable for various deployment environments. Consider implementing additional scaling strategies for production use.

### Troubleshooting

- Ensure all required environment variables are properly configured
- Verify network connectivity for external service integrations
- Check Node.js and npm versions for compatibility with project dependencies

## Contributing

We welcome contributions from the community! By contributing, you help improve and grow this project.

### How to Contribute

1. **Fork the Repository**: Create a personal fork of the project on GitHub.

2. **Clone Your Fork**: 
   ```bash
   git clone https://github.com/your-username/repo.git
   cd repo
   ```

3. **Create a Branch**: 
   ```bash
   git checkout -b feature/your-feature-name
   ```

### Development Setup

- Ensure you have Node.js installed (version compatible with the project's dependencies)
- Install dependencies:
  ```bash
  npm install
  ```

### Contribution Guidelines

#### Code Standards
- Follow existing code style and conventions in the project
- Use clear, descriptive variable and function names
- Write clean, readable, and well-commented code

#### Dependency Management
- When adding new dependencies, use `npm install --save` or `npm install --save-dev`
- Ensure dependencies are necessary and provide clear value

#### Testing
- Currently, no test suite is specified
- Manual testing of new features and changes is crucial
- Include steps to test your changes in the pull request description

### Pull Request Process

1. Ensure your code follows the project's guidelines
2. Update documentation to reflect your changes
3. Open a pull request with a clear title and description
4. Your code will be reviewed by project maintainers

#### Reporting Issues
- Use GitHub Issues to report bugs or suggest enhancements
- Provide detailed information, including:
  - Steps to reproduce
  - Expected behavior
  - Actual behavior
  - Environment details (OS, Node.js version)

### Code of Conduct
Treat all contributors with respect. Harassment and discrimination are not tolerated.

## License

This project is licensed under the ISC License. 

#### License Details
The ISC License is a permissive free software license published by the Internet Systems Consortium (ISC). It is functionally equivalent to the MIT license, but with simplified language.

Key characteristics of the ISC License include:
- Allows commercial use
- Allows modification
- Allows distribution
- Provides a warranty disclaimer

#### Licensing Terms
For the full license text, please refer to the license details specified in the `package.json` file. 

#### Usage
By using this software, you agree to the terms of the ISC License.