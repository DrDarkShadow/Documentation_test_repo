```markdown
## Overview

This file provides the `RepoAnalyzer` class, a tool for analyzing a local Git repository to identify changes at the code object level (functions and classes). It can inspect staged changes or all uncommitted changes (staged, unstaged, and untracked files). The analyzer compares the state of files against the `HEAD` commit to determine which functions or classes have been added, removed, or modified, providing a more granular view of changes than a standard file-level diff.

## Classes

### RepoAnalyzer

Analyzes a Git repository for code changes at the object level (functions and classes).

The class initializes with a path to a Git repository. It provides methods to compare the current working or staged state against the last commit (`HEAD`) and identify specific code constructs that have changed.

#### `__init__(repo_path: str)`

Initializes the `RepoAnalyzer` instance.

-   **Parameters:**
    -   `repo_path` (str): The file system path to the Git repository to be analyzed.
-   **Raises:**
    -   `git.InvalidGitRepositoryError`: If the provided `repo_path` is not a valid Git repository.

---

#### `_is_ignored(file_path: str) -> bool`

Checks if a file path matches any of the ignore patterns defined in the configuration (`CONFIG.ignore_patterns`). This is a private helper method.

-   **Parameters:**
    -   `file_path` (str): The path of the file to check.
-   **Returns:**
    -   `bool`: `True` if the file path matches an ignore pattern, `False` otherwise.

---

#### `_get_file_content(commit_hash: str, file_path: str) -> str`

Retrieves the content of a file from a specific commit in the repository. This is a private helper method.

-   **Parameters:**
    -   `commit_hash` (str): The hash of the commit from which to retrieve the file content.
    -   `file_path` (str): The path of the file within the repository.
-   **Returns:**
    -   `str`: The UTF-8 decoded content of the file. Returns an empty string if the file does not exist in the specified commit.

---

#### `analyze_changes(staged_only: bool = True) -> Dict[str, Dict[str, Any]]`

Analyzes file changes to identify added, removed, and modified code objects (functions and classes).

This is the main method of the class. It determines the diff set based on the `staged_only` flag. For each changed file, it parses the old and new versions to extract function and class definitions, then compares them to categorize the changes.

-   **Parameters:**
    -   `staged_only` (bool, optional): If `True` (default), analyzes only staged changes (index vs. `HEAD`). If `False`, analyzes all uncommitted changes, including staged, unstaged (working tree vs. index), and untracked files.
-   **Returns:**
    -   `Dict[str, Dict[str, Any]]`: A dictionary where each key is a file path that has changed. The corresponding value is another dictionary containing:
        -   `"status"` (str): The change type ('A' for added, 'D' for deleted, 'M' for modified, etc.).
        -   `"added"` (List[str]): A list of names of functions/classes added to the file.
        -   `"removed"` (List[str]): A list of names of functions/classes removed from the file.
        -   `"modified"` (List[str]): A list of names of functions/classes that have been modified.

##### Code Usage Example

```python
from code_monitor.analyzer import RepoAnalyzer

try:
    # Initialize the analyzer with the path to your repo
    analyzer = RepoAnalyzer('/path/to/your/git/repo')

    # Analyze only the staged changes
    staged_changes = analyzer.analyze_changes(staged_only=True)
    print("--- Staged Changes ---")
    for file, changes in staged_changes.items():
        print(f"File: {file} (Status: {changes['status']})")
        if changes['added']:
            print(f"  Added: {', '.join(changes['added'])}")
        if changes['modified']:
            print(f"  Modified: {', '.join(changes['modified'])}")
        if changes['removed']:
            print(f"  Removed: {', '.join(changes['removed'])}")

    # Analyze all uncommitted changes
    all_changes = analyzer.analyze_changes(staged_only=False)
    print("\n--- All Uncommitted Changes ---")
    for file, changes in all_changes.items():
        print(f"File: {file} (Status: {changes['status']})")
        if changes['added']:
            print(f"  Added: {', '.join(changes['added'])}")

except git.InvalidGitRepositoryError:
    print("The specified path is not a valid Git repository.")
except Exception as e:
    print(f"An error occurred: {e}")
```
```