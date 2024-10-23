---
title: 8. Traces
---

Save traces of model executions for debugging and distillation

An eight-factor AI application maintains comprehensive traces of all model interactions and process executions. A *trace* captures the complete context, execution path, and results of each operation. Traces should be structured, searchable, and suitable for both debugging and improvement.

Traces in an eight-factor app serve multiple purposes:
* Debugging complex interactions
* Performance optimization
* Cost monitoring
* Quality assurance
* Training data collection
* Process improvement

A complete trace includes:
* Input context and prompts used
* Model responses and parameters
* Tool calls and results
* Reasoning steps taken
* Resource usage metrics
* Timing information
* Final outcomes

Bad practice - minimal or unstructured logging:
```python
def process_request(request):
    logging.info(f"Processing request: {request}")
    result = model.generate(prompt)
    logging.info(f"Got result: {result}")
    return result
```

Good practice - structured tracing:
```python
class Tracer:
    def __init__(self, storage: TraceStorage):
        self.storage = storage
        self.current_trace = None
    
    @contextmanager
    def trace(self, operation_type: str, metadata: dict = None):
        trace = Trace(
            operation_type=operation_type,
            metadata=metadata,
            timestamp=datetime.now()
        )
        
        try:
            self.current_trace = trace
            yield trace
            trace.status = "success"
        except Exception as e:
            trace.status = "error"
            trace.error = str(e)
            raise
        finally:
            trace.end_timestamp = datetime.now()
            self.storage.store(trace)

class ModelInteractionTracer(Tracer):
    async def traced_generate(
        self, 
        prompt: str, 
        context: Context,
        **params
    ) -> TraceResult:
        with self.trace("model_generation") as trace:
            trace.record_input(prompt, context, params)
            
            result = await self.model.generate(prompt, **params)
            
            trace.record_output(
                result,
                token_usage=self.count_tokens(prompt, result),
                latency=trace.duration
            )
            
            return TraceResult(result, trace)
```

Traces should be structured hierarchically:
```python
class WorkflowTrace(Trace):
    def __init__(self):
        self.steps = []
        self.current_step = None
    
    @contextmanager
    def trace_step(self, step: Step):
        step_trace = StepTrace(step)
        self.current_step = step_trace
        self.steps.append(step_trace)
        
        try:
            yield step_trace
        finally:
            step_trace.complete()
    
    def to_timeline(self) -> Timeline:
        return Timeline([
            TimelineEvent(
                step=step.name,
                start=step.start_time,
                end=step.end_time,
                details=step.details
            )
            for step in self.steps
        ])
```

This approach enables:
* Process visualization
* Performance analysis
* Cost attribution
* Quality monitoring
* Continuous improvement

Traces should be treated as valuable data:
* Stored in a queryable format
* Retained according to policy
* Protected for privacy
* Indexed for search
* Analyzed for patterns

Applications should support trace analysis:
```python
class TraceAnalyzer:
    def analyze_traces(
        self, 
        timeframe: TimeRange, 
        filters: Dict[str, any]
    ) -> Analysis:
        traces = self.storage.query(timeframe, filters)
        
        return Analysis(
            performance=self.analyze_performance(traces),
            costs=self.analyze_costs(traces),
            quality=self.analyze_quality(traces),
            patterns=self.detect_patterns(traces),
            anomalies=self.detect_anomalies(traces)
        )
    
    def analyze_performance(self, traces: List[Trace]) -> Metrics:
        return Metrics({
            "p50_latency": self.percentile(traces, "duration", 50),
            "p95_latency": self.percentile(traces, "duration", 95),
            "token_usage": self.average(traces, "tokens_used"),
            "success_rate": self.success_rate(traces)
        })
```

This pattern enables:
* Performance optimization
* Cost optimization
* Quality improvement
* Pattern discovery
* Anomaly detection

Trace implementation should optimize for:
* Completeness - capturing all relevant data
* Structure - organized for analysis
* Accessibility - easy to query
* Privacy - protecting sensitive data
* Efficiency - managing storage costs

Each trace should capture:
* Full execution context
* All model interactions
* Resource usage
* Timing information
* Process decisions
* Final outcomes

Traces can also feed back into other factors:
* Examples - identifying good cases for the example suite
* Workflows - optimizing process flows
* Reasoning - improving decision patterns
* Context - refining context selection
* Tools - measuring tool effectiveness
