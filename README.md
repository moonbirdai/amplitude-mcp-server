[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/moonbirdai-amplitude-mcp-server-badge.png)](https://mseep.ai/app/moonbirdai-amplitude-mcp-server)

# Amplitude MCP Server

A [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server for integrating Amplitude analytics into AI workflows. This server allows AI assistants like Claude to track events, page views, user signups, set user properties, and track revenue in Amplitude.

**Version 1.0.1 Note:** Tool names now include the `amplitude_` prefix to prevent namespace collisions with other analytics MCP servers.

## Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Detailed Usage](#detailed-usage)
  - [With Claude Desktop](#with-claude-desktop)
  - [With Other MCP Clients](#with-other-mcp-clients)
- [Tool Reference](#tool-reference)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Security Notes](#security-notes)
- [Contributing](#contributing)
- [License](#license)

## Features

- Track custom events in Amplitude
- Track page views with properties
- Track user signups and create user profiles
- Update existing user properties
- Track revenue and purchases
- Simple integration with Claude Desktop and other MCP clients

## Installation

### Prerequisites

- Node.js 16 or higher
- An Amplitude API key (get one by signing up at [Amplitude](https://amplitude.com))

### NPM Installation (Recommended)

```bash
# Install globally
npm install -g amplitude-mcp-server

# Or use directly with npx
npx amplitude-mcp-server --api-key YOUR_AMPLITUDE_API_KEY
```

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/ciaraadkins/amplitude-mcp-server.git
cd amplitude-mcp-server

# Install dependencies
npm install

# Run the server
node index.js --api-key YOUR_AMPLITUDE_API_KEY
```

## Quick Start

To start using the Amplitude MCP server with Claude Desktop:

1. Make sure Claude Desktop is installed on your machine (download from [claude.ai/download](https://claude.ai/download))

2. Create or edit the Claude Desktop configuration file:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%AppData%\Claude\claude_desktop_config.json`

3. Add the Amplitude MCP server configuration:

```json
{
  "mcpServers": {
    "amplitude-analytics": {
      "command": "npx",
      "args": [
        "-y",
        "amplitude-mcp-server",
        "--api-key",
        "YOUR_AMPLITUDE_API_KEY"
      ]
    }
  }
}
```

4. Restart Claude Desktop and start tracking analytics!

## Detailed Usage

### With Claude Desktop

After setting up the configuration file as shown in the Quick Start section, you can use the Amplitude analytics capabilities directly in your conversations with Claude.

Examples of prompts you can use:

- "Track a custom event in Amplitude named 'document_generated' for user 'user123'"
- "Track a page view for the pricing page in Amplitude"
- "Create a new user profile in Amplitude for John Doe with email john@example.com"
- "Update user profile information in Amplitude for user 'user123'"
- "Track a purchase of Product X for $49.99"

Claude will use the appropriate Amplitude MCP tool based on your request.

### With Other MCP Clients

This server uses the standard Model Context Protocol and can be integrated with any MCP client:

1. Start the server:
   ```bash
   npx amplitude-mcp-server --api-key YOUR_AMPLITUDE_API_KEY
   ```

2. Connect your MCP client to the server using stdio transport

3. The client can discover and use the available tools (track_event, track_pageview, track_signup, set_user_properties, track_revenue)

## Tool Reference

The Amplitude MCP server provides the following tools:

### amplitude_track_event

Tracks a custom event in Amplitude.

**Parameters:**
- `event_name` (string, required): The name of the event to track
- `user_id` (string, optional): User identifier
- `device_id` (string, optional): Device identifier
- `properties` (object, optional): Additional properties to track with the event
- `user_properties` (object, optional): User properties to update with this event

**Note:** Either `user_id` or `device_id` must be provided.

**Example:**
```json
{
  "event_name": "button_clicked",
  "user_id": "user123",
  "properties": {
    "button_id": "submit_form",
    "page": "checkout"
  },
  "user_properties": {
    "last_action": "form_submit"
  }
}
```

### amplitude_track_pageview

Tracks a page view event in Amplitude.

**Parameters:**
- `page_name` (string, required): The name of the page viewed
- `user_id` (string, optional): User identifier
- `device_id` (string, optional): Device identifier
- `properties` (object, optional): Additional properties to track with the event

**Note:** Either `user_id` or `device_id` must be provided.

**Example:**
```json
{
  "page_name": "homepage",
  "user_id": "user123",
  "properties": {
    "referrer": "google.com",
    "duration": 120
  }
}
```

### amplitude_track_signup

Tracks a signup event and creates a user profile in Amplitude.

**Parameters:**
- `user_name` (string, required): User's full name
- `email` (string, required): User's email address
- `plan` (string, optional): Signup plan. Defaults to 'free'

**Example:**
```json
{
  "user_name": "John Doe",
  "email": "john@example.com",
  "plan": "premium"
}
```

### amplitude_set_user_properties

Updates a user's profile properties in Amplitude.

**Parameters:**
- `user_id` (string, required): User identifier
- `properties` (object, required): Profile properties to set

**Example:**
```json
{
  "user_id": "user123",
  "properties": {
    "name": "John Doe",
    "email": "john@example.com",
    "plan": "premium",
    "company": "Acme Inc"
  }
}
```

### amplitude_track_revenue

Tracks a revenue event in Amplitude.

**Parameters:**
- `user_id` (string, required): User identifier
- `product_id` (string, required): Identifier for the product purchased
- `price` (number, required): Price of the item purchased
- `quantity` (number, optional): Quantity of items purchased. Defaults to 1
- `revenue_type` (string, optional): Type of revenue (e.g., 'purchase', 'refund', 'subscription')

**Example:**
```json
{
  "user_id": "user123",
  "product_id": "premium_plan",
  "price": 49.99,
  "quantity": 1,
  "revenue_type": "subscription"
}
```

## Examples

Here are some practical examples of using the Amplitude MCP server with Claude:

### Tracking a Button Click

```
You: Can you track an event in Amplitude when a user clicks the submit button?

Claude: I'll track that event for you. Let me use the Amplitude analytics tool.

[Claude uses the amplitude_track_event tool with appropriate parameters]

Claude: I've successfully tracked the 'button_clicked' event in Amplitude with the properties you specified.
```

### Creating a User Profile

```
You: Create a new user profile in Amplitude for Sarah Johnson who signed up with sarah@example.com on the premium plan.

Claude: I'll create that user profile in Amplitude.

[Claude uses the amplitude_track_signup tool with appropriate parameters]

Claude: I've successfully tracked the signup for Sarah Johnson and created a profile in Amplitude with the premium plan.
```

### Tracking Revenue

```
You: Track a purchase of our premium plan for $99.99 by user12345.

Claude: I'll track that revenue event in Amplitude.

[Claude uses the amplitude_track_revenue tool with appropriate parameters]

Claude: I've successfully tracked revenue of $99.99 for the premium plan purchase by user12345.
```

## Troubleshooting

### Common Issues

1. **Server not starting:**
   - Ensure you've provided a valid Amplitude API key
   - Check that Node.js is installed and is version 16 or higher

2. **Claude Desktop not showing Amplitude tools:**
   - Verify your claude_desktop_config.json file syntax
   - Make sure you've restarted Claude Desktop after editing the config
   - Check that the path to the server is correct

3. **Events not appearing in Amplitude:**
   - Verify your Amplitude API key is correct
   - Check your Amplitude project settings to ensure data is being received
   - Events may take a few minutes to appear in the Amplitude interface

### Debug Mode

To run the server in debug mode for more verbose logging:

```bash
npx amplitude-mcp-server --api-key YOUR_AMPLITUDE_API_KEY --debug
```

## Security Notes

- Your Amplitude API key is stored in the Claude Desktop configuration file. Ensure this file has appropriate permissions
- Consider using environment variables for sensitive tokens in production environments
- This server does not implement rate limiting - consider adding additional security measures for production use
- Events are sent directly to Amplitude's API - review Amplitude's privacy policy for data handling practices

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
