# pyragify: Unlock the Power of Your Code with NotebookLM  

**pyragify** is a Python-based tool designed to **transform your Python code repositories into a format that's ready for analysis with large language models (LLMs), specifically NotebookLM.** It breaks down complex code structures into manageable semantic chunks, making it easier to understand, analyze, and extract insights from your code.

## Why pyragify?

*   **Boost Code Comprehension:**  pyragify makes it easier to digest large codebases by dividing them into smaller, logical units. 
*   **Effortless Analysis:** The structured output simplifies the process of analyzing code, identifying patterns, and extracting knowledge. 
*   **Unlock the Power of NotebookLM:** pyragify prepares your code for use with NotebookLM, allowing you to leverage the power of LLMs for tasks like code summarization, documentation generation, and question answering. 

## Key Features

*   **Semantic Chunking:** pyragify intelligently extracts functions, classes, and comments from Python files, as well as headers and sections from Markdown files, preserving the context and meaning.
*   **Wide Format Support:** It handles Python (.py), Markdown (.md, .markdown), and other common file types, ensuring all your repository content is processed. 
*   **Seamless Integration with NotebookLM:** The output format is specifically designed for compatibility with NotebookLM, making it easy to analyze your code with powerful LLMs.
*   **Flexible Configuration:** Tailor the processing through a YAML file or command-line arguments to fit your specific needs. 
*   **File Skipping:** Respect your `.gitignore` and `.dockerignore` files, and define custom skip patterns for even more control. 
*   **Word Limit Control:** Automatically chunks output files based on a configurable word limit to ensure manageable file sizes.
*   **File-Based Chunking (Optional):** Set `split_on_files: true` in your config to output each file as a separate chunk (recommended for LLM/embedding workflows with strict token limits).

## Chunk Structure (Output Schema)

Each chunk is a dictionary with a type-specific structure. Here are the main chunk types:

### Python Files
- **Function chunk:**
  ```json
  {
    "type": "function",
    "name": "function_name",
    "code": "def function_name(...):\n    ..."
  }
  ```
- **Class chunk:**
  ```json
  {
    "type": "class",
    "name": "ClassName",
    "methods": [{"name": "method1"}, ...],
    "code": "class ClassName(...):\n    ..."
  }
  ```
- **Comments chunk:**
  ```json
  {
    "type": "comments",
    "comments": [
      {"type": "comment", "line": 10, "text": "This is a comment"},
      {"type": "comment", "line": 20, "text": "Another comment"}
    ]
  }
  ```

### Markdown Files
- **Markdown chunk:**
  ```json
  {
    "header": "# Section Title",
    "content": "Section content here..."
  }
  ```

### Other Files
- **File chunk:**
  ```json
  {
    "type": "file",
    "name": "filename.ext",
    "content": "Full file content as string"
  }
  ```

> **Note:** Only the comments chunk uses a `comments` field (list of dicts). All other chunk types use `content` (string) or `code` (string).

## Getting Started

### Installation

1.  **Using uv (Recommended):**
    ```bash
    uv pip install pyragify
    ```

    `uv` is blazing fast dependencies and projects manager and will handle the creation of the virtual environment, installation of python and dependencies in a bling of an eye for you. 

2.  **Using pip:**
    ```bash
    pip install pyragify
    ```

### Usage

1.  **Best Practice with uv:**
    ```bash
    uv run python -m pyragify --config-file config.yaml
    ```
See below for details about the configuration file.

2.  **Direct CLI Execution:**
    ```bash
    python -m pyragify.cli process-repo
    ```

#### Arguments and Options

See `python -m pyragify.cli --help` for a full list of options.

*   `--config-file`: Path to the YAML configuration file (default: config.yaml).
*   `--repo-path`: Override the repository path.
*   `--output-dir`: Override the output directory. 
*   `--max-words`: Override the maximum words per output file.
*   `--max-file-size`: Override the maximum file size (in bytes) to process. 
*   `--skip-patterns`: Override file patterns to skip. 
*   `--skip-dirs`: Override directories to skip.
*   `--split-on-files`: If true, output each file as a separate chunk (default: false).
*   `--verbose`: Enable detailed logging for debugging. 

### Configuration (config.yaml)

```yaml
repo_path: /path/to/repository
output_dir: /path/to/output
max_words: 200000
max_file_size: 10485760 # 10 MB
skip_patterns:
 - "*.log"
 - "*.tmp"
skip_dirs:
 - "__pycache__"
 - "node_modules"
split_on_files: false  # If true, each file is output as a separate chunk
verbose: false
```

## Example Workflow

1.  **Prepare Your Repository:** Make sure your repository contains the code you want to process. Utilize `.gitignore` or `.dockerignore` to exclude unwanted files or directories.
2.  **Configure pyragify:** Create a `config.yaml` file with your desired settings or use the default configuration.
3.  **Process the Repository:** Run pyragify using uv (recommended): 
    ```bash
    uv run python -m pyragify --config-file config.yaml 
    ```
4.  **Check the Output:** Your processed content is neatly organized by file type in the specified output directory.

## Chat with Your Codebase (with NotebookLM)

1.  Navigate to NotebookLM.
2.  Upload the `chunk_0.txt` file (or other relevant chunks) from the pyragify output directory to a new notebook.
3.  Start asking questions and get insights with precise citations! You can even generate a podcast from your code. 
    ![code_chat](chat_code_base.png "Chat with your code base")

## Output Structure

The processed content is saved as `.txt` files and categorized into subdirectories based on the file type:

*   `python/`:  Contains chunks of Python functions, classes, and their code. 
*   `markdown/`:  Contains sections of Markdown files split by headers. 
*   `other/`:  Contains plain-text versions of unsupported file types. 

## Advanced Features

*   **Respect for Ignore Files:** pyragify automatically honors `.gitignore` and `.dockerignore` patterns. 
*   **Incremental Processing:** MD5 hashes are used to efficiently skip unchanged files during subsequent runs. 
*   **Configurable Chunking:** Use `split_on_files: true` to output each file as a separate chunk (recommended for LLM/embedding workflows with strict token limits). Default is false (semantic chunking with word limit).

## Contributing

We welcome contributions! To contribute to pyragify:

1.  Clone the repository.
2.  Install dependencies.
3.  Run tests. (Test suite is under development).

## Support

Feel free to create a GitHub issue for any questions, bug reports, or feature requests.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Example Usages

**Process a Repository with Default Settings:**

```bash
uv run python -m pyragify --config-file config.yaml
```

**Process a Specific Repository with Custom Settings:**

```bash
uv run python -m pyragify.cli process-repo \
 --repo-path /my/repo \
 --output-dir /my/output \
 --max-words 100000 \
 --max-file-size 5242880 \
 --skip-patterns "*.log,*.tmp" \
 --skip-dirs "__pycache__,node_modules" \
 --split-on-files true \
 --verbose 
```

This revised README emphasizes the key benefits and features of Pyragify, provides clear instructions for installation and usage, and includes example use cases to help users get started quickly. 