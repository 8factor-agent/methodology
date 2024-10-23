# 3. Model Providers
Model APIs should be treated as external, replaceable resources

An eight-factor AI application treats language models as attached resources, accessed through abstraction layers that enable provider flexibility. A *model provider* is any service offering language model capabilities, whether cloud-based APIs or local deployments. The interface to these providers should be clearly defined and independent of specific implementations.

Good provider abstraction has several key characteristics:
* No provider-specific code in core business logic
* Unified interface for model interactions
* Consistent handling of provider capabilities and limitations
* Clear separation between model access and model use

The application should require no code changes to switch between providers:
```python
# Configuration (environment or config file)
MODEL_PROVIDER=openai
MODEL_CONFIG={
    "model": "gpt-4",
    "temperature": 0.7
}

# Different providers, same interface
providers = {
    "openai": OpenAIProvider,
    "anthropic": AnthropicProvider,
    "local": LocalModelProvider
}

provider = providers[MODEL_PROVIDER](MODEL_CONFIG)
```

Bad practice - tight coupling to provider:
```python
import openai

def generate_text(prompt):
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}],
        temperature=0.7
    )
    return response.choices[0].message.content
```

Good practice - provider abstraction:
```python
class BaseModelProvider:
    def generate(self, 
                prompt: str,
                context: Optional[List[Message]] = None,
                **kwargs) -> GenerationResult:
        """Generate text from the model."""
        raise NotImplementedError

class OpenAIProvider(BaseModelProvider):
    def generate(self, prompt, context=None, **kwargs):
        messages = self._format_messages(prompt, context)
        response = self.client.chat.completions.create(
            model=self.config.model,
            messages=messages,
            **self._translate_params(kwargs)
        )
        return GenerationResult.from_openai(response)

class AnthropicProvider(BaseModelProvider):
    def generate(self, prompt, context=None, **kwargs):
        messages = self._format_messages(prompt, context)
        response = anthropic.messages.create(
            model=self.config.model,
            messages=messages,
            **self._translate_params(kwargs)
        )
        return GenerationResult.from_anthropic(response)
```

Provider abstraction should handle:
* Authentication and credentials management
* Rate limiting and quotas
* Error handling and retries
* Response parsing and normalization
* Parameter translation between providers
* Capability detection and fallbacks

A proper abstraction layer enables:
* Seamless provider switching
* Multi-provider strategies
* A/B testing between providers
* Graceful fallback handling
* Cost optimization
* Provider-specific optimizations behind a common interface

Providers should be treated as attached resources:
* Provider configuration should be external to application code
* Credentials should be managed through environment variables
* Provider health should be monitored
* Usage should be tracked and logged
* Costs should be monitored and controlled

Some applications may use multiple providers simultaneously:
```python
class MultiProvider(BaseModelProvider):
    def generate(self, prompt, **kwargs):
        for provider in self.providers:
            try:
                return provider.generate(prompt, **kwargs)
            except ProviderError:
                continue
        raise AllProvidersFailedError()
```

This pattern enables:
* Load balancing between providers
* Cost-based routing
* Capability-based provider selection
* High availability through redundancy
* Progressive enhancement based on provider capabilities

The abstraction layer should be treated as critical infrastructure:
* All provider interactions should be traced
* Performance metrics should be collected
* Costs should be tracked per provider
* Errors should be monitored and analyzed
* Usage patterns should be studied for optimization
