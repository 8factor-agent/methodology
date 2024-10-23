# 6. Reasoning
Divide processes into deliberative & impromptu reasoning

An eight-factor AI application explicitly distinguishes between different types of reasoning processes. The primary distinction is between *deliberative reasoning* (systematic, multi-step thinking) and *impromptu reasoning* (quick, context-based responses). Each type should be clearly identified and handled appropriately.

Reasoning types in an eight-factor app are explicitly defined:
* If all queries are handled the same way, it's not an eight-factor app – it wastes resources on simple tasks or underperforms on complex ones
* If reasoning patterns aren't explicit, the application can't optimize for different types of tasks

The two primary reasoning modes have distinct characteristics:

Deliberative Reasoning:
* Multiple steps of thought
* Explicit intermediate results
* Verifiable logic chain
* Resource intensive
* Used for complex tasks

Impromptu Reasoning:
* Single-step responses
* Direct context to output
* Quick turnaround
* Resource efficient
* Used for simple tasks

Bad practice - one-size-fits-all reasoning:
```python
def handle_request(request):
    prompt = f"Please help with this request: {request}"
    return model.generate(prompt)
    # All requests treated the same way
```

Good practice - differentiated reasoning paths:
```python
class ReasoningRouter:
    def route_request(self, request: Request) -> Response:
        complexity = self.assess_complexity(request)
        
        if complexity.is_simple():
            return self.impromptu_handler.respond(request)
        
        return self.deliberative_handler.process(request)

class DeliberativeHandler:
    def process(self, request: Request) -> Response:
        plan = self.create_reasoning_plan(request)
        context = self.initial_context(request)
        
        for step in plan.steps:
            result = self.execute_step(step, context)
            context.update(result)
            
            if self.should_revise_plan(result):
                plan = self.revise_plan(plan, context)
        
        return self.synthesize_response(context)

class ImpromptuHandler:
    def respond(self, request: Request) -> Response:
        template = self.select_response_template(request)
        return self.generate_direct_response(template, request)
```

Complexity assessment should be explicit:
```python
class ComplexityAssessor:
    def assess(self, request: Request) -> Complexity:
        factors = {
            "multi_step": self.requires_multiple_steps(request),
            "tool_use": self.requires_tools(request),
            "context_depth": self.context_complexity(request),
            "verification": self.needs_verification(request)
        }
        
        return Complexity.from_factors(factors)
```

This approach enables:
* Efficient resource utilization
* Appropriate handling of different task types
* Clear reasoning paths
* Verifiable decision processes
* Optimized response times

Reasoning modes should be explicitly tracked:
* Mode selection should be logged
* Reasoning steps should be traced
* Outcomes should be measured
* Performance should be monitored
* Resources should be allocated appropriately

Applications may implement hybrid reasoning:
```python
class HybridReasoner:
    def process(self, request: Request) -> Response:
        # Start with quick assessment
        quick_response = self.impromptu_handler.assess(request)
        
        if quick_response.confidence >= self.threshold:
            return quick_response
            
        # Fall back to deliberative if needed
        return self.deliberative_handler.process(request)
```

This pattern enables:
* Optimal resource allocation
* Appropriate depth of reasoning
* Clear processing paths
* Measurable outcomes
* Adaptive response strategies

Reasoning implementations should optimize for:
* Appropriateness - matching reasoning to task
* Efficiency - using resources wisely
* Clarity - explicit reasoning paths
* Verifiability - traceable decisions
* Adaptability - adjusting to task needs

Each reasoning mode should have:
* Clear entry criteria
* Defined success metrics
* Resource budgets
* Error handling strategies
* Performance benchmarks
