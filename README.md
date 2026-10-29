# L5 Language AST Interpreter & Type System

An Abstract Syntax Tree (AST) interpreter, static type checker, and type inference engine for **L5**—a strongly-typed, Scheme-like functional programming language. Built with TypeScript and Jest.

---

## 📌 Overview

**L5** extends functional programming semantics with strong static typing and advanced type inference capabilities. This repository contains the complete frontend, type checker, equational type inference engine, and evaluation pipeline for the L5 AST.

### Key Features
* **Static Type Checking**: Computes and validates expression types across programs, functions, variable definitions (`define`), and primitive applications.
* **Hindley-Milner Type Inference**: Uses equational type generation and Robinson's Unification Algorithm to infer types without requiring explicit annotations.
* **Algebraic & Compound Types**: Support for polymorphic lists `(list T)`, sum types `(T1 + T2)` with `left`/`right` constructs, and function signatures `(T1 * ... * Tn -> TR)`.
* **Environment-Based Interpreter**: AST evaluation supporting lexical scope, closure creation, and primitive runtime operations.
* **Type Unification Engine**: Substitution ADT and occurrence checking (`checkNoOccurrence`) to prevent circular references during type resolution.

---

## 🏗 System Architecture

The project is structured into three main layers: **AST Syntax & Representation**, **Type System & Inference Engine**, and **Evaluation/Execution**.

```
src/
├── L5/
│   ├── L5-ast.ts              # AST node definitions and AST parser
│   ├── L5-typecheck.ts        # Static type checker (`typeofExp`, `typeofProgram`, etc.)
│   ├── L5-type-equations.ts   # Type variable pool & equational generator for inference
│   ├── L5-substitution-adt.ts # Type substitutions and unification ADT
│   ├── L5-eval.ts             # AST evaluation engine
│   ├── L5-value.ts            # Environment and runtime value representations
│   └── TExp.ts                # Type Expression AST (Base types, Arrow, ListTExp, etc.)
└── shared/
    ├── result.ts              # Type-safe Result monad (`Ok` / `Failure`)
    ├── optional.ts            # Optional monad utilities
    └── list.ts                # Functional array/list helpers
```

---

## 🔬 Type System & Inference Details

### 1. Static Type Checking
The type checker recursively analyzes AST expressions:
* **Primitive Operators**: Enforces type contracts for built-in operations, including list constructors and accessors:
  $$\text{cons} : (T \times \text{List}(T) \to \text{List}(T))$$
  $$\text{car} : (\text{List}(T) \to T)$$
  $$\text{cdr} : (\text{List}(T) \to \text{List}(T))$$
* **Program Boundaries**: Evaluates typed sequencing `(L5 <exp>+)`, ensuring defined variable types remain consistent across global environments.

### 2. Equational Type Inference
The type inference engine generates type equations from untyped L5 AST expressions and solves them via unification:
1. **Pool Generation (`expToPool`)**: Assigns fresh type variables to all sub-expressions.
2. **Equation Generation (`makeEquationsFromExp`)**: Constructs equations based on operational rules (e.g., function application $T_\text{app} = T_\text{arg} \to T_\text{res}$, homogeneous lists).
3. **Unification & Substitution**: Solves equations while verifying occurrence constraints (`checkNoOccurrence`).

---

## 🚀 Getting Started

### Prerequisites
* **Node.js** (v16.0 or higher)
* **npm**

### Installation

```bash
# Clone the repository
git clone https://github.com/your-username/l5-ast-interpreter.git
cd l5-ast-interpreter

# Install dependencies
npm install
```

---

## 🧪 Testing

The repository includes test suites covering AST parsing, type checking, type inference, and evaluation.

```bash
# Run all tests using Jest
npm test

# Run tests in watch mode
npx jest --watch
```

---

## 💻 Example Usage (TypeScript)

```typescript
import { parseL5 } from './src/L5/L5-ast';
import { typeofExp } from './src/L5/L5-typecheck';
import { makeEmptyTEnv } from './src/L5/TEnv';
import { isOk } from './src/shared/result';

// Parse an L5 lambda expression with type annotations
const parsed = parseL5("(lambda ((x : number) (y : number)) : number (+ x y))");

if (isOk(parsed)) {
  const typeResult = typeofExp(parsed.value, makeEmptyTEnv());
  if (isOk(typeResult)) {
    console.log("Inferred Type:", typeResult.value);
  }
}
```

---

## 🛠 Tech Stack

* **Language**: TypeScript (Strict Mode enabled)
* **Testing Framework**: Jest (`jest-extended`)
* **Paradigm**: Functional Programming, Monads (`Result`, `Optional`), AST Transformation