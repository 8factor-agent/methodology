---
title: 4. Context 
---

Explicitly define processes that reduce application & user state

An eight-factor AI application has explicit processes for transforming application and user state into model-digestible context. A *context processor* defines how raw application state is filtered, formatted, and bundled for model consumption. Context processing should be isolated, reproducible, and version-controlled.

Context in an eight-factor app has clear boundaries:
* If context processing is scattered through application code, it's not an eight-factor app – it creates inconsistency and makes behavior unpredictable
* If context is passed without explicit processing, it risks overwhelming models or leaking sensitive information

A *context processor* handles several key responsibilities:
* Selecting relevant information from application state
* Formatting data for model consumption
* Enforcing context length limits
* Protecting sensitive information
* Maintaining consistency across interactions

Bad practice - implicit context handling:
```python
def handle_user_request(user, request):
    # Context mixed with business logic
    prompt = f"""User {user.name} (member since {user.joined_date})
    with preferences {user.preferences}
    and history {user.past_interactions}
    asks: {request}"""
    return get_model_response(prompt)
```

Good practice - explicit context processing:
```python
class UserContextProcessor:
    def __init__(self, max_history_items=5):
        self.max_history_items = max_history_items
    
    def process(self, user, request) -> Context:
        return Context({
            "user": self.get_user_context(user),
            "request": request,
            "history": self.get_relevant_history(user)
        })
    
    def get_user_context(self, user):
        return {
            "name": user.name,
            "preferences": self.filter_preferences(user.preferences),
            "expertise_level": self.determine_expertise(user)
        }
    
    def get_relevant_history(self, user):
        return user.get_recent_interactions(
            limit=self.max_history_items,
            relevant_to=request
        )

# Usage
context_processor = UserContextProcessor()
context = context_processor.process(user, request)
response = agent.process(context)
```

Context processors should handle various types of context:
```python
class ContextRegistry:
    def __init__(self):
        self.processors = {}
    
    def register(self, name, processor):
        self.processors[name] = processor
    
    def process(self, context_type, data):
        if context_type not in self.processors:
            raise UnknownContextTypeError(context_type)
        return self.processors[context_type].process(data)

# Different context types
registry.register("user", UserContextProcessor())
registry.register("document", DocumentContextProcessor())
registry.register("conversation", ConversationContextProcessor())
```

This approach provides several benefits:
* Consistent context processing across the application
* Clear boundaries for what information models can access
* Easier testing and validation of context processing
* Better privacy and security controls
* Reproducible model inputs

Context should be treated as a first-class concept:
* Context processors should be version controlled
* Context transformations should be logged
* Context size should be monitored
* Privacy rules should be enforced
* Context relevance should be measured

Some applications may need dynamic context selection:
```python
class DynamicContextProcessor:
    def process(self, data, request_type):
        relevant_contexts = self.determine_relevant_contexts(request_type)
        return Context.merge([
            self.process_context(context_type, data)
            for context_type in relevant_contexts
        ])
```

This pattern enables:
* Controlled information flow to models
* Consistent handling of application state
* Privacy by design
* Reproducible model behavior
* Efficient context management
* Clear boundaries for model knowledge

Context processors should be optimized for:
* Relevance - including only necessary information
* Efficiency - minimizing context size
* Privacy - protecting sensitive data
* Consistency - maintaining predictable format
* Traceability - logging context transformations
