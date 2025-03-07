# SSO and Cloud Application Documentation

This repository contains two Django applications that work together to implement a Single Sign-On (SSO) authentication system:

1. **SSO Server** (Port 8000): OAuth2 provider that handles user authentication
2. **Cloud Application** (Port 8001): Client application that uses the SSO server for authentication

## Project Goals

This section tracks the current goals and their status for the project:

- [X] Set up SSO Server with OAuth2 provider functionality
- [X] Implement Cloud Application as OAuth2 client
- [X] Create authentication flow between SSO and Cloud
- [X] Implement logout functionality

## Project Structure

```
test/
├── sso/                  # SSO Server (OAuth2 Provider)
│   ├── sso/              # Main application
│   │   ├── settings.py   # SSO server configuration
│   │   ├── urls.py       # URL routing
│   │   └── views.py      # Authentication views
│   ├── templates/        # HTML templates
│   │   └── registration/ # Login/register templates
│   └── register_oauth_app.py  # Script to register Cloud as OAuth client
│
├── cloud/                # Cloud Application (OAuth2 Client)
│   ├── cloud/            # Main application
│   │   ├── settings.py   # Cloud app configuration
│   │   ├── urls.py       # URL routing
│   │   └── views.py      # Application views with OAuth integration
│   ├── templates/        # HTML templates
│   └── static/           # Static files (CSS, JS, images)
│
└── run_servers.sh        # Script to start both servers
```

## Configuration Details

### SSO Server (Port 8000)

The SSO server is an OAuth2 provider built with Django OAuth Toolkit.

**Key Settings:**
- `OAUTH2_PROVIDER`: Configuration for OAuth2 provider
- `AUTHORIZATION_VIEW_CLASS`: Custom view that handles authorization requests
- User authentication is email-based

**Important Files:**
- `sso/views.py`: Contains `AutoAuthorizationView`
- `sso/urls.py`: URL routing for OAuth endpoints
- `register_oauth_app.py`: Script to register the Cloud application as an OAuth client

### Cloud Application (Port 8001)

The Cloud application is an OAuth2 client that authenticates users via the SSO server.

**Key Settings:**
- `CLIENT_ID` and `CLIENT_SECRET`: OAuth client credentials
- `AUTHORIZATION_BASE_URL`: SSO server's authorization endpoint
- `TOKEN_URL`: SSO server's token endpoint
- `REDIRECT_URI`: Callback URL after authentication

**Important Files:**
- `cloud/views.py`: Contains OAuth flow implementation with PKCE
- `cloud/middleware.py`: Custom middleware for session management

## Authentication Flow

1. User accesses Cloud application (port 8001)
2. After pressing start button Cloud redirects to SSO server (port 8000) with OAuth parameters
3. SSO server displays login page (even if user was previously authenticated)
4. User enters credentials
5. SSO server validates credentials and redirects back to Cloud with authorization code
6. Cloud exchanges code for access token
7. Cloud uses token to fetch user information and create session

## Logout Flow

1. When user in Cloud application (port 8001) press logout
2. All tokens,sessions will be cleared
3. User will be redirected to (8001) /

## Running the Applications

Use the provided script to start both servers:

```bash
bash run_servers.sh
```

This will:
1. Install required packages if needed
2. Run migrations for both applications
3. Register the Cloud application as an OAuth client
4. Start the SSO server on port 8000
5. Start the Cloud application on port 8001

### Log Files

Both applications use a centralized logging system that directs all output to their respective log files:

- **sso_server.log**: Contains logs from the SSO server (port 8000)
- **cloud_server.log**: Contains logs from the Cloud application (port 8001)

All console logs (print statements) have been replaced with proper Python logging calls, ensuring a single point of entry for writing/reading logs.

You can view these logs in real-time using:

```bash
# For SSO server logs
tail -f sso_server.log

# For Cloud application logs
tail -f cloud_server.log
```

These logs contain valuable information about the authentication flow, including:
- OAuth request/response details
- User authentication events
- Session management
- Error messages and debugging information

The logging configuration is defined in each project's `settings.py` file and includes:
- Formatted log entries with timestamps and log levels
- Both file and console output
- Different log levels for different components (DEBUG for application code, INFO for Django framework)

## Development with Django

### Best Practices for Agent Development with Django

1. **Project Structure Understanding**
   - Always examine `settings.py` to understand configuration
   - Check `urls.py` to understand routing
   - Review middleware for request/response processing

2. **Authentication Flows**
   - OAuth2 flows require careful handling of tokens and sessions
   - PKCE (Proof Key for Code Exchange) enhances security for public clients
   - Session management is critical for maintaining user state

3. **Debugging Tips**
   - Use Django's debug mode (`DEBUG = True`) during development
   - Add print statements in views to track request flow
   - Check browser developer tools for cookies and redirects
   - Review log files (sso_server.log and cloud_server.log) for detailed information
   - Use `tail -f` to monitor logs in real-time during development

4. **Common Issues**
   - Session persistence: Users staying logged in unexpectedly
   - CSRF protection: Ensure forms have CSRF tokens
   - Redirect loops: Carefully manage authentication redirects

5. **Security Considerations**
   - Never expose `CLIENT_SECRET` in client-side code
   - Validate redirect URLs to prevent open redirects
   - Use HTTPS in production environments

### Django Commands Reference

```bash
# Create a new Django project
django-admin startproject projectname

# Create a new app within a project
python manage.py startapp appname

# Run migrations
python manage.py makemigrations
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Run development server
python manage.py runserver [port]

# Shell for testing
python manage.py shell
```

## UI Elements for Automation

The application includes specific HTML element IDs to facilitate automated testing and interaction:

- `start-button`: The main START button on the homepage (http://localhost:8001/) that initiates the application flow

### Interacting with the START Button

When automating the authentication flow, it's important to properly interact with the START button:

1. **Finding the START button by ID**:
   ```javascript
   // The START button has a specific ID that can be used to locate it
   document.querySelector('#start-button')
   ```

2. **Clicking the START button**:
   ```javascript
   // Using Chrome tool or browser automation
   click_at_element 1 "#start-button"
   
   // Using JavaScript
   document.querySelector('#start-button').click()
   ```

3. **Important Notes**:
   - The START button is an anchor (`<a>`) element with ID `start-button` and class `btn btn-main-md`
   - The full HTML of the button is: `<a href="/home/" id="start-button" class="btn btn-main-md">START</a>`
   - When clicking this button, it's crucial to preserve the OAuth parameters in the URL
   - If the page refreshes and loses URL parameters, you need to go back to http://localhost:8001/ and click START again
   ## Important Development Guidelines

### Do Not Modify Example Files

⚠️ **IMPORTANT**: Never modify or patch any files in the `examples_dont_change_this_files` folder. These files serve as reference examples only and should be kept in their original state.

- The `cloud/examples_dont_change_this_files/` directory contains template examples
- The `sso/examples/` directory contains reference implementations
- These files are for learning and reference purposes only
- Any changes to these files may break functionality or cause unexpected behavior

Always create new files or modify existing files outside of these example directories when implementing new features or fixing issues.
