# WhatsApp Notification GitHub Action

This repository contains a reusable GitHub workflow for sending WhatsApp notifications.

## Usage

To use this reusable workflow in your GitHub Actions, create a workflow file like this:

```yaml
name: Send WhatsApp Notification
on:
  push:
    branches: [ main ]
  # Or any other trigger

jobs:
  notify:
    uses: cds-id/notification-whatsapp/.github/workflows/whatsapp-notification.yml@main
    with:
      message: "Your message here"
      phone: "120363400580213063@g.us"  # For group chat
      host: "https://your-whatsapp-api-host.com"
    secrets:
      basic_auth: ${{ secrets.WHATSAPP_BASIC_AUTH }}
```

## Step-by-Step Setup Guide

1. Add the workflow to your repository
   Create a new workflow file (e.g., `.github/workflows/notify.yml`) in your repository:

```yaml
name: Send Notification
on:
  # Choose your trigger
  push:
    branches: [ main ]
  # Or manual trigger
  workflow_dispatch:
    inputs:
      message:
        description: 'Message to send'
        required: true
        type: string

jobs:
  notify:
    uses: cds-id/notification-whatsapp/.github/workflows/whatsapp-notification.yml@main
    with:
      message: ${{ github.event_name == 'workflow_dispatch' && inputs.message || 'New commit pushed!' }}
      phone: "120363400580213063@g.us"  # Replace with your group ID
      host: "https://example.com"  # Replace with your host
    secrets:
      basic_auth: ${{ secrets.WHATSAPP_BASIC_AUTH }}
```

2. Set up repository secrets
   - Go to your repository settings
   - Navigate to "Secrets and variables" → "Actions"
   - Add `WHATSAPP_BASIC_AUTH` with your base64 encoded credentials

3. Customize the workflow
   - Modify the trigger events as needed
   - Update the message content
   - Change the phone number/group ID
   - Adjust the host URL if needed

## Inputs

- `message`: The message to be sent (required)
- `phone`: The recipient phone number or group ID (required)
  - For group chats, use format: `xxx@g.us`
  - For individual chats, use the phone number
- `host`: The WhatsApp API host URL (required)

## Secrets

- `basic_auth`: Basic authentication credentials in base64 format (required)

## Common Use Cases

### Deployment Notification
```yaml
name: Deploy and Notify
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy
        run: |
          # Your deployment steps
          
  notify:
    needs: deploy
    uses: cds-id/notification-whatsapp/.github/workflows/whatsapp-notification.yml@main
    with:
      message: "🚀 Deployment successful!"
      phone: "120363400580213063@g.us"
      host: "https://maid-sender.ciptadusa.com"
    secrets:
      basic_auth: ${{ secrets.WHATSAPP_BASIC_AUTH }}
```

### Error Notification
```yaml
name: Test and Notify
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        id: tests
        run: |
          # Your test commands
          
    # Only notify on failure
    if: failure()
    uses: cds-id/notification-whatsapp/.github/workflows/whatsapp-notification.yml@main
    with:
      message: "❌ Tests failed! Please check."
      phone: "120363400580213063@g.us"
      host: "https://example.com"
    secrets:
      basic_auth: ${{ secrets.WHATSAPP_BASIC_AUTH }}
```

### Easypanel Deployment
```yaml
name: Deploy to Easypanel
jobs:
  deploy:
    uses: cds-id/notification-whatsapp/.github/workflows/easypanel-deploy.yml@main
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
      EASYPANEL_API_URL: ${{ secrets.EASYPANEL_API_URL }}
      EASYPANEL_API_KEY: ${{ secrets.EASYPANEL_API_KEY }}
      EASYPANEL_PROJECT_ID: ${{ secrets.EASYPANEL_PROJECT_ID }}
      EASYPANEL_SERVICE_NAME: ${{ secrets.EASYPANEL_SERVICE_NAME }}
      WHATSAPP_GROUP_ID: ${{ secrets.WHATSAPP_GROUP_ID }}
      WHATSAPP_API_HOST: ${{ secrets.WHATSAPP_API_HOST }}
      WHATSAPP_BASIC_AUTH: ${{ secrets.WHATSAPP_BASIC_AUTH }}
```

Required secrets for Easypanel deployment:
- `DOCKERHUB_USERNAME`: Your Docker Hub username
- `DOCKERHUB_TOKEN`: Docker Hub access token
- `EASYPANEL_API_URL`: Your Easypanel instance URL
- `EASYPANEL_API_KEY`: Easypanel API key
- `EASYPANEL_PROJECT_ID`: ID of your Easypanel project
- `EASYPANEL_SERVICE_NAME`: Name of your service in Easypanel
- `WHATSAPP_GROUP_ID`: WhatsApp group ID for notifications
- `WHATSAPP_API_HOST`: WhatsApp API host URL
- `WHATSAPP_BASIC_AUTH`: Basic auth credentials for WhatsApp API

The workflow will:
1. Build your Docker image
2. Push it to Docker Hub
3. Trigger Easypanel deployment
4. Send WhatsApp notification about the deployment status

## Testing Workflows

You can test the workflows using our test workflow. Go to the "Actions" tab in your repository and select "Test Workflows".

### Available Tests

1. **WhatsApp Only Test**
   - Tests only the WhatsApp notification functionality
   - Sends a test message to your configured WhatsApp group

2. **Secrets Verification**
   - Verifies all required secrets are properly configured
   - Tests connection to Easypanel API
   - Sends success notification via WhatsApp

3. **Docker Build Test**
   - Tests Docker image building process
   - Verifies Docker Hub credentials
   - Does not push the image
   - Sends build status via WhatsApp

4. **Test All**
   - Runs all the above tests in sequence

### How to Run Tests

1. Go to your repository on GitHub
2. Navigate to the "Actions" tab
3. Select "Test Workflows" from the workflows list
4. Click "Run workflow"
5. Select the type of test you want to run
6. Click "Run workflow"

You'll receive WhatsApp notifications about the test results.

### Troubleshooting Tests

If tests fail, check:
1. Secret configurations in GitHub repository settings
2. Docker Hub credentials and permissions
3. Easypanel API access and connectivity
4. WhatsApp API connectivity and group ID

## Troubleshooting

1. Authentication errors:
   - Verify your `WHATSAPP_BASIC_AUTH` secret is correctly set
   - Check if the base64 encoding is correct

2. Message delivery issues:
   - Verify the `phone` number/group ID format
   - Check if the `host` URL is accessible
   - Ensure your workflow has permission to access secrets

Need help? Open an issue at [github.com/cds-id/notification-whatsapp](https://github.com/cds-id/notification-whatsapp)
