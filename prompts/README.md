# I. Prompts
All prompts tracked separately from code

An eight-factor AI application stores all its prompts in dedicated files, separate from application code. A prompt file can be a simple text file, a structured format like YAML, or a specialized prompt template format. A collection of these files forms a *prompt library*.

There is always a clear separation between prompts and the code that uses them:
* If prompts are embedded in code, it's not an eight-factor app – it's mixing concerns. Prompts should be externalized and loaded by the application at runtime.
* Multiple applications sharing the same prompts is encouraged – this promotes reuse of well-tested prompts and enables central management of prompt libraries.

A *prompt* consists of several possible elements:
* The core instruction text
* Variable placeholders for dynamic content
* System context or role definitions
* Example interactions or few-shot learning cases

There can be multiple prompt variations for different use cases, but they should all be tracked in version control. For example:
* Production-optimized prompts vs. development/testing prompts
* Prompts optimized for different model providers
* Variations for different languages or user contexts

The prompt library should be treated as a critical application asset:
* All changes should be tracked in version control
* Prompts should have clear versioning
* Changes should be tested against saved examples (Factor V)
* Prompt effectiveness should be measured through traces (Factor VIII)

Example of a proper prompt file structure:
```
prompts/
  ├── analysis/
  │   ├── text_analysis.txt
  │   └── sentiment_analysis.txt
  ├── generation/
  │   ├── email_composer.txt
  │   └── code_generator.txt
  └── system/
      ├── error_handler.txt
      └── user_helper.txt
```

Bad practice - embedding prompts in code:
```python
def analyze_text(text):
    prompt = f"Analyze the following text and provide key insights: {text}"
    return call_model(prompt)
```

Good practice - loading prompts from files:
```python
def analyze_text(text):
    prompt_template = load_prompt('analysis/text_analysis')
    prompt = prompt_template.format(input_text=text)
    return call_model(prompt)
```

This approach enables:
* Independent iteration on prompts without code changes
* A/B testing different prompt versions
* Clear prompt versioning and history
* Easier collaboration between prompt engineers and developers
* Systematic prompt optimization using examples and traces

Some applications may compile prompts into an optimized format during the build process, similar to how template files are processed in web applications. However, the source prompts should always be maintained in a human-readable format in the prompt library.
