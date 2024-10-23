# The 8-Factor Agent

## Introduction

In the era of artificial intelligence, software increasingly incorporates AI agents as core components, called *AI applications* or *AI-native services*. The eight-factor agent methodology is a methodology for building AI applications that:

* Use **dedicated prompts** as first-class citizens, to maintain clarity and enable systematic improvements;
* Have **clear interfaces** for tool interactions, offering **maximum flexibility** in capability integration;
* Are suitable for deployment with **multiple model providers**, avoiding vendor lock-in and enabling model switching;
* **Separate reasoning types** between deliberative and impromptu processes, enabling optimal resource utilization;
* And can **scale complexity** through workflows while maintaining traceability and reproducibility.

The eight-factor methodology can be applied to AI applications built with any language model provider, and which use any combination of tools and capabilities.

## Background

This methodology builds upon the foundation laid by the [Twelve-Factor App Methodology](https://12factor.net), which has been instrumental in shaping modern web application development. While the twelve-factor methodology remains excellent for traditional web applications, AI agents present unique challenges that require their own set of best practices.

The contributors to this document have been directly involved in the development and deployment of AI applications across various scales and complexities. This document synthesizes our experience and observations on a wide variety of AI agents in production.

Our motivation is to address the emerging challenges in AI application development, provide a shared vocabulary for discussing these challenges, and offer a set of broad conceptual solutions with accompanying terminology. The format pays homage to the twelve-factor methodology while focusing on the specific needs of AI agent development.

## Who should read this document?

Any developer building applications that incorporate AI agents. Machine learning engineers who deploy or manage such agents. Teams working on integrating language models into their applications.

## The Eight Factors

### 1. [Prompts](https://8factor.net/prompts)
> All prompts tracked separately from code

### 2. [Tools](https://8factor.net/tools)
> Explicitly define & isolate interfaces that models will call

### 3. [Model Providers](https://8factor.net/model-providers)
> Model APIs should be treated as external, replaceable resources

### 4. [Context](https://8factor.net/context)
> Explicitly define processes that reduce application & user state

### 5. [Examples](https://8factor.net/examples)
> Maintain ground-truth examples of expected prompt results

### 6. [Reasoning](https://8factor.net/reasoning)
> Divide processes into deliberative & impromptu reasoning

### 7. [Workflows](https://8factor.net/workflows)
> Use workflows to model and run deliberative processes

### 8. [Traces](https://8factor.net/traces)
> Save traces of model executions for debugging and distillation
