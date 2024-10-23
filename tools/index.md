---
title: 2. Tools
---

Explicitly define & isolate interfaces that models will call

An eight-factor AI application treats tools as clearly defined interfaces through which models can affect the outside world. A *tool* is any function, API, or capability that extends the model's abilities beyond pure text generation. The collection of these interfaces forms the application's *tool registry*.

Every tool in an eight-factor app must have an explicit interface declaration:
* If tool implementations are directly exposed to models, it's not an eight-factor app – it creates tight coupling and security risks.
* Tools shared between multiple agents should be defined once and registered consistently across applications.

A *tool interface* consists of several key elements:
* Name and description that models can understand
* Clear input/output specifications
* Usage examples
* Permissions and constraints
* Error handling patterns

Tools should be registered with complete metadata:
```python
tool_registry.register({
    "name": "weather_lookup",
    "description": "Get current weather for a location",
    "parameters": {
        "location": {
            "type": "string",
            "description": "City name or coordinates"
        }
    },
    "returns": {
        "type": "object",
        "properties": {
            "temperature": "number",
            "conditions": "string"
        }
    },
    "examples": [
        {
            "input": {"location": "New York"},
            "output": {"temperature": 72, "conditions": "sunny"}
        }
    ]
})
```

Bad practice - direct implementation exposure:
```python
def get_weather(location):
    # Model can directly call APIs and handle raw data
    api_key = os.getenv('WEATHER_API_KEY')
    response = requests.get(f'https://api.weather.com/{location}?key={api_key}')
    return response.json()
```

Good practice - tool interface abstraction:
```python
class WeatherTool(BaseTool):
    def execute(self, location: str) -> WeatherInfo:
        """Get weather information for a location."""
        try:
            weather_service = self.get_weather_service()
            raw_data = weather_service.fetch(location)
            return WeatherInfo.from_raw(raw_data)
        except WeatherServiceError as e:
            raise ToolError(f"Weather lookup failed: {e}")
```

This approach provides several critical benefits:
* Security through controlled access to underlying services
* Consistent error handling and rate limiting
* Easy replacement of tool implementations
* Clear documentation for both developers and models
* Ability to mock tools for testing
* Usage analytics and monitoring

Some applications may dynamically discover or load tools at runtime, but the interface definitions should still be explicit and version-controlled. Tools may be implemented in different ways (API calls, local functions, external services), but the interface presented to models should remain consistent.

The tool registry should be treated as a security boundary:
* All tool access should be audited
* Tool permissions should be clearly defined
* Tool usage should be monitored and rate-limited
* Tool responses should be validated before returning to models

This pattern enables:
* Safe and controlled access to external capabilities
* Easy addition of new tools without model changes
* Clear separation between tool interface and implementation
* Systematic testing and validation of tool interactions
* Comprehensive monitoring of tool usage and performance
