---
layout:       post
title:        "代码风格"
author:       "shensunbo"
header-style: text
catalog:      true
tags:
    - 代码风格
---

# Clean Code
1. Readability: Code should be easy to read and understand. Use clear and concise variable names, formatting, and comments to make the code self-explanatory.
2. Simplicity: Favor simplicity over complexity. Break down complex logic into smaller, manageable functions or modules.
3. Consistency: Follow a consistent coding style, naming conventions, and formatting throughout the codebase.
4. Modularity: Organize code into independent, modular components that can be easily maintained, updated, or replaced.

# Solid Principles

1. Single Responsibility Principle (SRP): A class or function should have only one reason to change, i.e., it should have a single responsibility or purpose.
2. Open-Closed Principle (OCP): A class or function should be open for extension but closed for modification, i.e., new functionality can be added without modifying the existing code.
3. Liskov Substitution Principle (LSP): Derived classes should be substitutable for their base classes, i.e., any code that uses a base class should be able to work with a derived class without knowing the difference.
4. Interface Segregation Principle (ISP): A client should not be forced to depend on interfaces it does not use, i.e., interfaces should be designed to meet the needs of specific clients.
5. Dependency Inversion Principle (DIP): High-level modules should not depend on low-level modules, but both should depend on abstractions, i.e., decouple dependencies to make the code more flexible and maintainable.