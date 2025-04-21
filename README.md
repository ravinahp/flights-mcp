# Find Flights MCP Server ![Option #1](https://github.com/user-attachments/assets/95f0c9fb-f8d8-4290-8b70-26b683707173)


[![smithery badge](https://smithery.ai/badge/@ravinahp/travel-mcp)](https://smithery.ai/server/@ravinahp/travel-mcp)
MCP server for searching and retrieving flight information using Duffel API.

## How it Works
![Flight](https://github.com/user-attachments/assets/3ee342a4-c2da-4d4e-a43c-79ae4590d893)

## Video Demo
https://github.com/user-attachments/assets/c111aa4c-9559-4d74-a2f6-60e322c273d4

## Why This is Helpful
While tools like Google Flights work great for simple trips, this tool shines when dealing with complex travel plans. Here's why:

- **Contextual Memory**: Claude remembers all your previous flight searches in the chat, so you don't need to keep multiple tabs open to compare prices
- **Flexible Date Search**: Easily search across multiple days to find the best prices without manually checking each date
- **Complex Itineraries**: Perfect for multi-city trips, one-stop flights, or when you need to compare different route options you can just ask!
- **Natural Conversation**: Just describe what you're looking for - no more clicking through calendar interfaces or juggling search parameters down to parsing city names, dates, and times.

Think of it as having a travel agent in your chat who remembers everything you've discussed and can instantly search across dates and routes.

## Features
- Search for flights between multiple destinations
- Support for one-way, round-trip, and multi-city flight queries
- Detailed flight offer information
- Flexible search parameters (departure times, cabin class, number of passengers)
- Automatic handling of flight connections
- Search for flights within multiple days to find the best flight for your trip (slower)
## Prerequisites
- Python 3.x
- Duffel API Live Key

## Getting Your Duffel API Key
Duffel requires account verification and payment information setup, but this MCP server only uses the API for searching flights - no actual bookings or charges will be made to your account.

Try using duffel_test first to see the power of this tool. If you end up liking it, you can go through the verification process below to use the live key.

### Test Mode First (Recommended)
You can start with a test API key (`duffel_test`) to try out the functionality with simulated data before going through the full verification process:
1. Visit [Duffel's registration page](https://app.duffel.com/join)
2. Create an account (you can select "Personal Use" for Company Name)
3. Navigate to More > Developer to find your test API key (one is already provided)

### Getting a Live API Key
To access real flight data, follow these steps:
1. In the Duffel dashboard, toggle "Test Mode" off in the top left corner
2. The verification process requires multiple steps - you'll need to toggle test mode off repeatedly:
   - First toggle: Verify your email address
   - Toggle again: Complete company information (Personal Use is fine)
   - Toggle again: Add payment information (required by Duffel but NO CHARGES will be made by this MCP server)
   - Toggle again: Complete any remaining verification steps
   - Final toggle: Access live mode after clicking "Agree and Submit"
3. Once fully verified, go to More > Developer > Create Live Token
4. Copy your live API key

💡 TIP: Each time you complete a verification step, you'll need to toggle test mode off again to proceed to the next step. Keep toggling until you've completed all requirements.

⚠️ IMPORTANT NOTES:
- Your payment information is handled directly by Duffel and is not accessed or stored by the MCP server
- This MCP server is READ-ONLY - it can only search for flights, not book them
- No charges will be made to your payment method through this integration
- All sensitive information (including API keys) stays local to your machine
- You can start with the test API key (`duffel_test`) to evaluate the functionality
- The verification process may take some time - this is a standard Duffel requirement

### Security Note
This MCP server only uses Duffel's search endpoints and cannot make bookings or charges. Your payment information is solely for Duffel's verification process and is never accessed by or shared with the MCP server.

### Note on API Usage Limits
- Check Duffel's current pricing and usage limits
- Different tiers available based on your requirements
- Recommended to review current pricing on their website

## Installation

### Installing via Smithery

To install Find Flights for Claude Desktop automatically via [Smithery](https://smithery.ai/server/@ravinahp/travel-mcp):

```bash
npx -y @smithery/cli install @ravinahp/travel-mcp --client claude
```

### Manual Installation
Clone the repository:
```bash
git clone https://github.com/ravinahp/flights-mcp
cd flights-mcp
```

Install dependencies using uv:
```bash
uv sync
```
Note: We use uv instead of pip since the project uses pyproject.toml for dependency management.

## Configure as MCP Server
To add this tool as an MCP server, modify your Claude desktop configuration file.

Configuration file locations:
- MacOS: `~/Library/Application\ Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%/Claude/claude_desktop_config.json`

Add the following configuration to your JSON file:
```json
{
    "flights-mcp": {
        "command": "uv",
        "args": [
            "--directory",
            "/Users/YOUR_USERNAME/Code/flights-mcp",
            "run",
            "flights-mcp"
        ],
        "env": {
            "DUFFEL_API_KEY_LIVE": "your_duffel_live_api_key_here"
        }
    }
}
```

⚠️ IMPORTANT:
- Replace `YOUR_USERNAME` with your actual system username
- Replace `your_duffel_live_api_key_here` with your actual Duffel Live API key
- Ensure the directory path matches your local installation

## Deployment
### Building
Prepare the package:
```bash
# Sync dependencies and update lockfile
uv sync

# Build package
uv build
```
This will create distributions in the `dist/` directory.

## Debugging
For the best debugging experience, use the MCP Inspector:
```bash
npx @modelcontextprotocol/inspector uv --directory /path/to/find-flights-mcp run flights-mcp
```

The Inspector provides:
- Real-time request/response monitoring
- Input/output validation
- Error tracking
- Performance metrics

## Available Tools

### 1. Search Flights
```python
@mcp.tool()
async def search_flights(params: FlightSearch) -> str:
    """Search for flights based on parameters."""
```
Supports three flight types:
- One-way flights
- Round-trip flights
- Multi-city flights

Parameters include:
- `type`: Flight type ('one_way', 'round_trip', 'multi_city')
- `origin`: Origin airport code
- `destination`: Destination airport code
- `departure_date`: Departure date (YYYY-MM-DD)
- Optional parameters:
  - `return_date`: Return date for round-trips
  - `adults`: Number of adult passengers
  - `cabin_class`: Preferred cabin class
  - `departure_time`: Specific departure time range
  - `arrival_time`: Specific arrival time range
  - `max_connections`: Maximum number of connections

### 2. Get Offer Details
```python
@mcp.tool()
async def get_offer_details(params: OfferDetails) -> str:
    """Get detailed information about a specific flight offer."""
```
Retrieves comprehensive details for a specific flight offer using its unique ID.

### 3. Search Multi-City Flights
```python
@mcp.tool(name="search_multi_city")
async def search_multi_city(params: MultiCityRequest) -> str:
    """Search for multi-city flights."""
```
Specialized tool for complex multi-city flight itineraries.

Parameters include:
- `segments`: List of flight segments
- `adults`: Number of adult passengers
- `cabin_class`: Preferred cabin class
- `max_connections`: Maximum number of connections

## Use Cases
### Some Example (But try it out yourself!)
You can use these tools to find flights with various complexities:
- "Find a one-way flight from SFO to NYC on Jan 7 for 2 adults in business class"
- "Search for a round-trip flight from LAX to London, departing Jan 8 and returning Jan 15"
- "Plan a multi-city trip from New York to Paris on Jan 7, then to Rome on Jan 10, and back to New York on Jan 15"
- "What is the cheapest flight from SFO to LAX from Jan 7 to Jan 15 for 2 adults in economy class?"
- You can even search for flights within multiple days to find the best flight for your trip. Right now, the reccomendation is to only search for one-way or round-trip flights this way. Example: "Find the cheapest flight from SFO to LAX from Jan 7 to Jan 10 for 2 adults in economy class"

## Response Format
The tools return JSON-formatted responses with:
- Flight offer details
- Pricing information
- Slice (route) details
- Carrier information
- Connection details

## Error Handling
The service includes robust error handling for:
- API request failures
- Invalid airport codes
- Missing or invalid API keys
- Network timeouts
- Invalid search parameters

## Contributing
[Add guidelines for contribution, if applicable]

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Performance Notes
- Searches are limited to 50 offers for one-way/round-trip flights
- Multi-city searches are limited to 10 offers
- Supplier timeout is set to 15-30 seconds depending on the search type

### Cabin Classes
Available cabin classes:
- `economy`: Standard economy class
- `premium_economy`: Premium economy class
- `business`: Business class
- `first`: First class

Example request with cabin class:
```json
{
  "params": {
    "type": "one_way",
    "adults": 1,
    "origin": "SFO",
    "destination": "LAX",
    "departure_date": "2025-01-12",
    "cabin_class": "business"  // Specify desired cabin class
  }
}
```
