---
title: 5. Examples
---

Maintain ground-truth examples of expected prompt results

An eight-factor AI application maintains a comprehensive set of example inputs and outputs that serve as ground truth for system behavior. These *examples* act as test cases, documentation, and few-shot learning data. They should be explicitly maintained and version controlled alongside code and prompts.

Examples in an eight-factor app serve multiple purposes:
* Validation of prompt effectiveness
* Documentation of expected behavior
* Training data for model fine-tuning
* Regression testing of model outputs
* Few-shot learning inputs for inference

A proper example collection contains:
* Input/output pairs
* Edge cases and corner conditions
* Negative examples (what the system should not do)
* Metadata about expected behavior
* Performance benchmarks

Bad practice - implicit or missing examples:
```python
def generate_summary(text):
    prompt = load_prompt("summarization")
    return model.generate(prompt.format(text=text))
    # No way to verify if summary is correct
```

Good practice - explicit example management:
```python
class SummarizationExamples:
    def get_examples(self) -> List[Example]:
        return [
            Example(
                input="The quick brown fox jumps over the lazy dog.",
                expected_output="A fox jumps over a dog.",
                metadata={
                    "type": "basic",
                    "focuses_on": "core_message",
                    "max_length": 30
                }
            ),
            Example(
                input="Due to heavy rainfall...",
                expected_output="Weather caused delays.",
                metadata={
                    "type": "edge_case",
                    "focuses_on": "causality"
                }
            )
        ]

class Example:
    def __init__(self, input, expected_output, metadata=None):
        self.input = input
        self.expected_output = expected_output
        self.metadata = metadata or {}
        self.validate()
    
    def validate(self):
        """Ensure example meets quality standards"""
        if not self.input or not self.expected_output:
            raise InvalidExampleError("Empty input or output")
        
    def matches(self, actual_output) -> SimilarityScore:
        """Compare actual output to expected output"""
        return calculate_similarity(
            self.expected_output, 
            actual_output,
            self.metadata.get("comparison_type", "semantic")
        )
```

Examples should be organized by capability:
```
examples/
  ├── summarization/
  │   ├── basic.yaml
  │   ├── edge_cases.yaml
  │   └── negative.yaml
  ├── classification/
  │   ├── basic.yaml
  │   └── multi_label.yaml
  └── generation/
      ├── creative.yaml
      └── structured.yaml
```

Examples should be used systematically:
```python
class PromptTester:
    def __init__(self, examples: List[Example], threshold=0.8):
        self.examples = examples
        self.threshold = threshold
    
    def test_prompt(self, prompt_template: str) -> TestResult:
        results = []
        for example in self.examples:
            prompt = prompt_template.format(input=example.input)
            output = model.generate(prompt)
            similarity = example.matches(output)
            results.append({
                "example": example,
                "output": output,
                "similarity": similarity,
                "passed": similarity >= self.threshold
            })
        return TestResult(results)
```

This approach enables:
* Systematic prompt testing
* Clear behavior documentation
* Performance benchmarking
* Quality regression detection
* Continuous improvement

Examples should be treated as critical assets:
* Version controlled with the codebase
* Updated when behavior specifications change
* Used in automated testing
* Referenced in documentation
* Used for model evaluation

Some applications may use examples dynamically:
```python
class DynamicExampleSelector:
    def select_examples(self, context, k=3) -> List[Example]:
        """Select most relevant examples for current context"""
        relevant = self.find_similar_examples(context)
        return self.rank_and_select(relevant, k)
```

This pattern enables:
* Dynamic few-shot learning
* Context-aware behavior
* Adaptive system responses
* Clear behavior specifications
* Systematic quality improvement

The example suite should optimize for:
* Coverage - testing all important cases
* Clarity - clear expected behaviors
* Maintainability - easy to update
* Reusability - useful across systems
* Measurability - clear success criteria
