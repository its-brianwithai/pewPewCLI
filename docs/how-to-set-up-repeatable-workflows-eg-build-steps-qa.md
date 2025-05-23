# How To: Set Up Repeatable Workflows (e.g., Build Steps, QA)

## Introduction

This tutorial explains how to use the enhanced `pew next task` command in `pew-pew-cli` to manage tasks across multiple markdown files. This feature allows you to separate different workflows, such as main development tasks, QA checklists, or preparation steps, while still iterating through them sequentially using a single command.

## Basic Usage (Single File Review)

Before diving into multiple files, let's quickly review how `pew next task` works with a single default file (`tasks.md`):

1.  **Finds Task:** It looks for the first line starting with `- [ ]` (an unchecked task).
2.  **Adds Pointer:** If no `👉` symbol exists in the file, it adds it to the beginning of that first unchecked task line.
3.  **Displays Task:** It shows you the task content, along with any relevant headers above it, and a summary of tasks in that file.
4.  **Completes Task:** When you run `pew next task` again, it finds the line with `👉`, marks it as complete (`- [x]`), removes the `👉`, finds the *next* available unchecked task (`- [ ]`), adds the `👉` there, and displays that next task.

```bash
# Run the command to get the next task
pew next task
```

## Configuration for Multiple Files

To use multiple task files, you need to list them in the `pew.yaml` configuration file.

1.  **Locate or Create Config:** If you haven't run `pew init` yet, do so first. This creates the `pew.yaml` file in your project root.
2.  **Edit `pew.yaml`:** Open `pew.yaml` in your editor.
3.  **List Files:** Under the `tasks.all` key, list the paths to your markdown task files. The paths can be relative to the project root or absolute. `pew next task` will process these files *in the order they are listed*.

**Example `pew.yaml`:**

```yaml
tasks:
  all:
    - tasks.md           # Main development tasks
    - qa_checklist.md    # Separate QA checklist
    # - prep_steps.md    # Another optional file
    # - /Users/you/global_tasks.md # Absolute path example
  primary: tasks.md      # Primary task file
  paste: tasks.md        # Default target for 'pew paste tasks'

updates:
  lastUpdateCheckTimestamp: 0
```

## Multi-File Workflow Example (Dev Tasks + QA Checklist)

Let's set up an example with main development tasks and a separate QA checklist.

1.  **Create Files:**
    *   Ensure `tasks.md` exists.
    *   Create a new file: `qa_checklist.md`.

2.  **Add Content:**
    *   **`tasks.md`:**
        ```markdown
        # Project Setup
        - [ ] Initialize project
        - [ ] Install dependencies

        # Feature A
        - [ ] Implement core logic
        - [ ] Add basic UI
        ```
    *   **`qa_checklist.md`:**
        ```markdown
        # QA Steps
        - [ ] Verify Feature A UI renders
        - [ ] Test Feature A core logic with valid input
        - [ ] Test Feature A core logic with invalid input
        ```

3.  **Configure `pew.yaml`:** Make sure your `pew.yaml` lists these files in the desired order:
    ```yaml
    tasks:
      all:
        - tasks.md
        - qa_checklist.md
      primary: tasks.md
      paste: tasks.md
    updates:
      lastUpdateCheckTimestamp: 0
    ```

4.  **Run `pew next task`:**
    *   **First Run:**
        ```bash
        pew next task
        ```
        *   **Expected Output:** It will find `- [ ] Initialize project` in `tasks.md`, add the `👉`, and display it. The summary and file path will refer to `tasks.md`.
        ```
        👉 - [ ] Initialize project
        (Total: 4 | Completed: 0 | Remaining: 4)
        (File: tasks.md)
        ```
    *   **Subsequent Runs (within `tasks.md`):** Keep running `pew next task`. It will mark tasks complete in `tasks.md` and move the `👉` to the next one within that file.
        ```bash
        pew next task # Completes 'Initialize project', shows 'Install dependencies'
        pew next task # Completes 'Install dependencies', shows 'Implement core logic'
        pew next task # Completes 'Implement core logic', shows 'Add basic UI'
        ```
    *   **Transitioning to `qa_checklist.md`:** Run `pew next task` after completing the last task in `tasks.md` (`Add basic UI`).
        ```bash
        pew next task # Completes 'Add basic UI'
        ```
        *   **Expected Output:** It will find no more unchecked tasks in `tasks.md`. It will then read `qa_checklist.md`, find the first unchecked task (`- [ ] Verify Feature A UI renders`), add the `👉` there, and display it. The summary and file path will now refer to `qa_checklist.md`.
        ```
        👉 - [ ] Verify Feature A UI renders
        (Total: 3 | Completed: 0 | Remaining: 3)
        (File: qa_checklist.md)
        ```
    *   **Runs within `qa_checklist.md`:** Continue running `pew next task` to work through the QA checklist.
    *   **Wrapping Around:** Once all tasks in `qa_checklist.md` are completed, the next `pew next task` run will wrap around and look for the next available task starting from the beginning of the list (`tasks.md`). If all tasks in all files are complete, it will display the "All tasks complete" message.

## Using `pew paste tasks` with Multiple Files

The `pew paste tasks` command also interacts with your configuration:

*   **Default Target:** By default, `pew paste tasks` will paste into the file specified by the `tasks.paste` value in your `pew.yaml`. If `tasks.paste` is not set, it defaults to the value of `tasks.primary`.
*   **Overriding Target:** You can paste into any file, regardless of the configuration, by using the `--path` option:
    ```bash
    # Paste clipboard content into the QA checklist instead of the default
    pew paste tasks --path qa_checklist.md --append
    ```

This allows you to quickly add tasks to specific files in your workflow without changing the configuration.

## Use Case: Resetting Secondary Files (e.g., QA Checklist)

You might want to keep your main `tasks.md` evolving while reusing a standard checklist (like `qa_checklist.md`) for each feature or release. `pew-pew-cli` iterates through the files as they are, but it **does not automatically reset them**.

Resetting a secondary file is a **manual process** you perform outside the tool when needed.

**How to Manually Reset:**

1.  **Have a Template:** Keep a clean copy of your checklist, perhaps named `qa_checklist_template.md`.
2.  **Copy Over:** When you want to reset the QA process, simply copy the template content over the active checklist file.

    *   **Example (Mac/Linux):**
        ```bash
        cp qa_checklist_template.md qa_checklist.md
        ```
    *   **Example (Windows):**
        ```bash
        copy qa_checklist_template.md qa_checklist.md
        ```

Now, the next time `pew next task` finishes with `tasks.md`, it will start from the beginning of your refreshed `qa_checklist.md`.

## Conclusion

The multi-file capability of `pew next task` provides flexibility in organizing your workflows. By configuring `pew.yaml`, you can seamlessly transition between different sets of tasks using the same simple command, while the tool keeps track of the current focus and progress within each file context. Remember that resetting recurring checklists is a manual step integrated into your workflow. 