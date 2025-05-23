# How To: Quickly Add Content to Task Files from Clipboard

## Introduction

This tutorial explains how to use the `pew paste tasks` command in `pew-pew-cli` to quickly add content from your clipboard to your markdown task files. It covers the different pasting modes, how to configure a default paste target, and how to override the target using command-line options.

## Basic Usage

The `pew paste tasks` command reads the content of your system clipboard and writes it to a specified task file.

1.  **Copy Content:** First, copy the text you want to paste into your task file to your clipboard.
2.  **Run Command:** Execute the `pew paste tasks` command in your terminal within your project directory.

    ```bash
    pew paste tasks
    ```

3.  **Choose Mode (if prompted):** If you don't specify a mode (like `--append` or `--overwrite`), you will be prompted to choose how you want the content to be pasted:
    *   `append`: Adds the clipboard content to the end of the file.
    *   `overwrite`: Replaces the entire content of the file with the clipboard content.
    *   `insert`: Adds the clipboard content to the beginning of the file.

    ```
    ? Choose paste mode: (Use arrow keys)
    > append
      overwrite
      insert
    ```
    [Screenshot: Interactive prompt showing the paste mode selection]

## Paste Modes

You can specify the paste mode directly using flags:

*   **Append:** Adds content to the end of the file.
    ```bash
    pew paste tasks --append
    ```

*   **Overwrite:** Replaces the entire file content.
    ```bash
    pew paste tasks --overwrite
    # You can also use --force as an alias
    pew paste tasks --force
    ```

*   **Insert:** Adds content to the beginning of the file.
    ```bash
    pew paste tasks --insert
    ```

## Configuring the Default Paste Target

By default, `pew paste tasks` needs to know *which* file to paste into. You can configure a default target file in your `pew.yaml` file using the `tasks.paste` value.

1.  **Initialize (if needed):** Ensure you have run `pew init`. This command automatically sets up the `pew.yaml` file and adds the `tasks` section with values for `all`, `primary`, and `paste`, usually all pointing to the same default file (`tasks.md`).

    **Default `pew.yaml` after `pew init`:**
    ```yaml
    tasks:
      all:
        - tasks.md
      primary: tasks.md
      paste: tasks.md
    updates:
      lastUpdateCheckTimestamp: 0
    ```

2.  **Modify (Optional):** You can manually edit `pew.yaml` to change the `tasks.paste` value to point to a different default file if needed. The path can be relative to your project root or an absolute path.

    **Example Custom Configuration:**
    ```yaml
    tasks:
      all:
        - main_tasks.md
        - qa_checklist.md
      primary: main_tasks.md
      # Set a different default file for pasting
      paste: inbox.md
    updates:
      lastUpdateCheckTimestamp: 0
    ```

**Fallback Behavior:** If the `tasks.paste` value is missing or invalid in your configuration, `pew paste tasks` will fall back to using the `tasks.primary` value. If that is also missing or invalid, it will default to `tasks.md` relative to your current working directory.

## Overriding the Target with `--path`

You can temporarily paste into a different file without changing your configuration by using the `--path` option.

*   **Specify Path:** Provide the path to the desired target file after the `--path` flag.

    ```bash
    # Copy content to clipboard first
    # echo "- [ ] Paste to specific file" | pbcopy

    # Paste into a specific file, appending the content
    pew paste tasks --path path/to/another/file.md --append
    ```
    *   **Expected Output:**
        ```
        Pasted content to path/to/another/file.md (append).
        ```

*   **Handling Non-Existent Paths:** If the file specified with `--path` does not exist, `pew paste tasks` will prompt you, asking if you want to paste into the configured default file instead.

    ```bash
    # Attempt to paste to a file that doesn't exist
    pew paste tasks --path non-existent/tasks.md --append
    ```
    *   **Expected Output Prompt:**
        ```
        ? Path 'non-existent/tasks.md' does not exist. Paste into default 'tasks.md' instead? (y/N)
        ```
        [Screenshot: Prompt asking whether to use the default path when the override path doesn't exist]

    If you answer `y` (yes), it will paste to the default file. If you answer `n` (no) or press Enter, the operation will be aborted.

## Conclusion

The `pew paste tasks` command offers a flexible way to add content to your task files from the clipboard. By understanding the different paste modes, configuring the `tasks.paste` setting in your `pew.yaml`, and utilizing the `--path` override, you can efficiently manage where your pasted content goes within your project workflows. 