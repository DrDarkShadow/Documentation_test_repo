```markdown
## Overview

This file provides utility functions for statically analyzing Python code. It uses Python's built-in `ast` (Abstract Syntax Tree) module to parse code content and extract structural information about its components, such as functions and classes, without executing the code.

## Functions

### `get_functions_and_classes()`

Parses a string of Python code to identify and extract metadata about all defined functions (synchronous and asynchronous) and classes.

The function walks through the Abstract Syntax Tree (AST) of the provided code and collects details for each function and class definition it finds. If the input code contains syntax errors, the function will gracefully handle the exception and return an empty dictionary to prevent breaking the analysis process.

#### Parameters

-   `code_content (str)`: A string containing the Python source code to be analyzed.

#### Returns

-   `Dict[str, Dict[str, Any]]`: A dictionary where each key is the name of a discovered function or class. The corresponding value is another dictionary containing the following metadata:
    -   `type (str)`: The type of the object, either "Function" or "Class".
    -   `name (str)`: The name of the function or class.
    -   `start_line (int)`: The line number where the definition of the object begins.
    -   `end_line (int)`: The line number where the definition of the object ends.

#### Usage Example

```python
import ast
from typing import List, Tuple, Dict, Any

def get_functions_and_classes(code_content: str) -> Dict[str, Dict[str, Any]]:
    """
    Parses Python code content to extract information about functions and classes.
    """
    try:
        tree = ast.parse(code_content)
    except SyntaxError:
        # If file has syntax errors, return empty so it doesn't break analysis
        return {}
    objects = {}

    for node in ast.walk(tree):
        if isinstance(node, (ast.FunctionDef, ast.ClassDef, ast.AsyncFunctionDef)):
            obj_type = "Class" if isinstance(node, ast.ClassDef) else "Function"
            objects[node.name] = {
                "type": obj_type,
                "name": node.name,
                "start_line": node.lineno,
                "end_line": node.end_lineno
            }
    return objects

# Example usage:
code = """
import os

class MyClass:
    def __init__(self):
        pass

    def my_method(self):
        return "Hello"

def my_function():
    print("World")
"""

analysis_result = get_functions_and_classes(code)
import json
print(json.dumps(analysis_result, indent=2))

# Expected Output:
# {
#   "MyClass": {
#     "type": "Class",
#     "name": "MyClass",
#     "start_line": 3,
#     "end_line": 9
#   },
#   "my_function": {
#     "type": "Function",
#     "name": "my_function",
#     "start_line": 11,
#     "end_line": 12
#   },
#   "__init__": {
#     "type": "Function",
#     "name": "__init__",
#     "start_line": 4,
#     "end_line": 6
#   },
#   "my_method": {
#     "type": "Function",
#     "name": "my_method",
#     "start_line": 8,
#     "end_line": 9
#   }
# }
```
```