```markdown
## Overview

This file defines the configuration management for the code monitoring tool. It introduces the `MonitorConfig` class, which is responsible for loading settings from a YAML configuration file named `.monitor_config.yaml`. The settings include patterns for files/directories to ignore and specific file extensions to monitor.

A global `CONFIG` instance is created and exported, providing a single, easily accessible point for configuration data throughout the application.

## Classes

### MonitorConfig

The `MonitorConfig` class encapsulates the configuration settings for the monitoring tool. It initializes with default values and attempts to override them by loading from a specified YAML file.

**Attributes:**
- `ignore_patterns` (List[str]): A list of glob patterns for files and directories to be ignored by the monitor. Defaults to `[]`.
- `file_extensions` (List[str]): A list of file extensions (e.g., `.py`, `.js`) that the monitor should check. Defaults to `['.py']`.

#### `__init__(config_path='.monitor_config.yaml')`

Initializes a `MonitorConfig` object. It sets default configuration values and then attempts to load and apply settings from a YAML configuration file if it exists at the specified path.

-   **Parameters:**
    -   `config_path` (str, optional): The path to the YAML configuration file. Defaults to `'.monitor_config.yaml'` in the current working directory.

-   **Returns:**
    -   None.

#### Code Usage Example

The configuration is typically managed through a `.monitor_config.yaml` file in the root of your project.

**Example `.monitor_config.yaml`:**

```yaml
# Patterns to ignore (supports glob patterns)
ignore_patterns:
  - "**/__pycache__/**"
  - "*.tmp"
  - "venv/"
  - ".git/"

# File extensions to check
file_extensions_to_check:
  - ".py"
  - ".md"
  - ".yaml"
```

**Python Usage:**

The module automatically creates a global `CONFIG` instance that can be imported and used across the application.

```python
# In another file, e.g., main.py
from code_monitor.config import CONFIG

def main():
    # Access the loaded configuration
    print("Files to ignore:", CONFIG.ignore_patterns)
    print("Extensions to check:", CONFIG.file_extensions)

if __name__ == "__main__":
    main()

# Expected output (if .monitor_config.yaml exists as above):
# Files to ignore: ['**/__pycache__/**', '*.tmp', 'venv/', '.git/']
# Extensions to check: ['.py', '.md', '.yaml']
```

If `.monitor_config.yaml` does not exist, `CONFIG` will hold the default values:

```python
# Expected output (if .monitor_config.yaml does not exist):
# Files to ignore: []
# Extensions to check: ['.py']
```
```