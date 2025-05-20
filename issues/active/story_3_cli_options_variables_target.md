---
name: 📒 Story
about: User-focused feature with clear goals and outcomes
title: "📒 Implement CLI option parsing for template variables and target directory"
labels: 📒 story
---
# 🔖 Description
> 💡 *This story enhances the `pew create <templateName>` command by allowing users to provide values for template variables and specify an output directory directly via command-line options. This provides a non-interactive way to customize template generation.*
---

# 🗣 User Story
> 💡 ***As a*** *CLI User* ***I want*** *to pass template variable values (e.g., `--ViewName=TestView`) and a target output directory (e.g., `--target=./output`) as options to the `pew create <templateName>` command* ***so that*** *I can automate and customize template generation without interactive prompts.*
---

# ⚙️ Requirements
> 💡 *What are the requirements for this story? What should be in scope and what should be out of scope?*
---
*   **In Scope:**
    *   Modifying the `pew create <templateName>` command to accept arbitrary `--<key>=<value>` options for variables.
    *   Adding a specific `--target=<PATH>` option.
    *   Parsing these options and making their values available to the template generation logic.
    *   Updating help documentation for the command.
*   **Out of Scope:**
    *   The actual use of these parsed variables and target path in file generation (covered in later stories).
    *   Interactive prompting if variables are not provided (covered in Story 4).

# ✅ Acceptance Criteria
> 💡 *Specific conditions that must be met for the story to be considered complete. Each criterion should be testable and unambiguous.*
---

* [ ] Criterion 1: The `pew create <templateName>` command accepts dynamic options in the format `--<VariableName>=<Value>` (e.g., `pew create myView --ViewName=MyComponent --Version=1.0`).
* [ ] Criterion 2: Variable names (`<VariableName>`) passed via CLI options are case-sensitive and are intended to match keys defined in the `variables` map of the specified template in `pew.yaml`.
* [ ] Criterion 3: The command accepts a `--target=<PATH>` option to specify the directory where the templated files will be generated.
* [ ] Criterion 4: If the `--target` option is not provided, the output directory defaults to the current working directory (from which `pew` was executed). This default is handled internally and made available.
* [ ] Criterion 5: All parsed variable values from dynamic CLI options and the resolved target path are collected and stored (e.g., in an options object or map) to be used by subsequent template processing logic.
* [ ] Criterion 6: The help documentation for `pew create <templateName>` is updated to explain the `--target` option and the pattern for variable options (e.g., "Use --<VarName>=<Value> to set template variables.").

# 💾 Data Model
> 💡 *Old and new data models that will be created and/or altered when this feature is added.*
---
*   No changes to persistent data models (`pew.yaml`).
*   In-memory: The command handler in `CliService` will need to manage a data structure (e.g., a `Record<string, string>`) to store the parsed variable key-value pairs from CLI options, and a string for the target path.

# 🔒 Security Rules / Row Level Security
> 💡 *Old and new security rules with roles and access that should be created and/or altered. Include create, read, update and delete.*
---
Not Applicable. Path validation for `--target` might be considered for security (e.g., preventing writing outside intended areas), but that's more related to file output in Story 7.

# 🐒 API
> 💡 *Old and new API calls that should be created and/or altered.*
---
Not Applicable (CLI tool).

# 📊 Analytics
> 💡 *Old and new analytics that should be created and/or altered when this feature is added. Include a name, when it's fired and optional properties.*
---
Not Applicable for this story.

# ☎️ Impact Communication
> 💡 *Who / which teams should we inform about the impact of releasing this ticket? Sales, marketing, data, CS, other?*
---
*   Users of `pew-pew-cli` (via release notes).
*   Documentation update.

# 🧪 Tests
> 💡 *Components/flows/code that would benefit from tests and which scenario's should be tested.*
---
*   `CliService` command handler for `pew create`:
    *   Test parsing of `--target=<PATH>`.
    *   Test parsing of one or more dynamic variable options (e.g., `--VarOne=val1 --VarTwo=val2`).
    *   Test with no options provided (defaults should apply for target path, variables map empty).
    *   Test with mixed options.
    *   Test that variable names and values are correctly extracted.
    *   Test case sensitivity of variable names.
*   Commander.js configuration for allowing arbitrary options or a specific parsing strategy for them.

# 🤝 Acceptance Test
> 💡 *Which scenario's should we test in the acceptance test? So that we can make sure that this ticket does what it is supposed to do without any unexpected errors.*
---
1.  Run `pew create myTemplate --User=Alice --Role=Admin`. Verify internally that `User: "Alice"` and `Role: "Admin"` are captured.
2.  Run `pew create myTemplate --target=./custom_output`. Verify internally that the target path is resolved to `./custom_output`.
3.  Run `pew create myTemplate --User=Bob --target=../another_dir --Age=30`. Verify all options are captured.
4.  Run `pew create myTemplate`. Verify internally that the target path defaults to `process.cwd()` and the variables map is empty.
5.  Run `pew create myTemplate --help`. Verify the help output includes information about `--target` and variable options.

# 🎨 UI/UX Behaviour
> 💡 *Anything to take note of regarding the behaviour of UI/UX elements (if applicable). Think of position, behaviour when elements do not fit the screen, feedback on elements and properties of animations.*
---
*   The help text for the command should clearly explain how to use these options.
*   If an option is malformed (e.g., `--target` without a value, if the parser doesn't handle it), standard Commander.js error handling should apply.

# ⏱️ Effort Breakdown & Estimates
> 💡 *Detailed breakdown of estimated effort required for each aspect of the user story implementation.*
---

*   **Design:** [0.5] hours
    *   _Reasoning: Deciding how Commander.js will handle dynamic/arbitrary options for variables._
*   **Refinement:** [0.5] hours
    *   _Reasoning: Finalizing ACs and option parsing details._
*   **Front-end:** [0] hours
    *   _Reasoning: Not applicable (CLI tool)._
*   **Backend:** [2.5] hours
    *   _Reasoning: Modifying Commander.js setup in `src/index.ts`, updating `CliService` handler to parse and store these options._
*   **General Work:** [0.5] hours
    *   _Reasoning: Updating help documentation._

# 🧪 QA, Testing & Delay Margin
> 💡 *Estimates for quality assurance, testing efforts, and buffer time for potential delays.*
---

*   **QA:** [0.5] hours ([~12]%)
    *   _Reasoning: Manual testing of various CLI option combinations._
*   **Testing:** [1.5] hours ([~37]%)
    *   _Reasoning: Unit tests for option parsing logic in `CliService`._
*   **Delay Margin:** [0.5] hours ([~12]%)
    *   _Reasoning: Commander.js configuration for dynamic options can sometimes be tricky._

# 📝 Suggested High Level Approach
> 💡 *With knowledge of the current codebase, try to define a best suggested approach. Think of current components used, flow of data and UI elements. Include mermaid diagrams to illustrate flows and connections.*
---
1.  Modify the `pew create` command definition in `src/index.ts` using Commander.js.
    *   Add a specific `.option('--target <path>', 'Specify the output directory')`.
    *   Investigate how Commander.js can parse arbitrary options like `--key=value`. One way is to parse `process.argv` manually for options starting with `--` that are not known options, or use `program.parseOptions(process.argv)`. Commander's `passThroughOptions()` might be relevant, or collecting unknown options.
2.  In `CliService.handleCreateCommand(templateName: string, options: any)`:
    *   The `options` object from Commander will contain known options like `target`.
    *   Iterate over all provided options (or `process.argv`) to extract key-value pairs for variables. Store them in a `Record<string, string>`.
    *   Resolve the target path (default to `process.cwd()` if `options.target` is not set).
    *   Pass the collected variables map and target path to the next stage of template processing (which will be implemented in later stories).
3.  Update the command's `.description()` or add `.addHelpText('after', ...)` to document the new options.

```mermaid
graph TD
    A[User runs `pew create tpl --Var1=Val1 --target=./out`] --> B[index.ts: Commander.js parsing];
    B --> C[CliService.handleCreateCommand("tpl", parsedOptions)];
    C --> D[Extract `target` from parsedOptions (default to cwd)];
    C --> E[Iterate options/argv to find `--Key=Value` pairs];
    E --> F[Store extracted variables: {Var1: "Val1"}];
    F --> G[Store resolved targetPath: "./out"];
    G --> H[Proceed to next step (variable prompting/file processing - future stories)];
```

# 🎯 Roles & Todo's
> *Backend Dev · Front-end Dev · Ui/Ux Designer · DevOps Engineer*
---

* 📌 **Project Manager**:
    - [ ] Review and approve the story.
* 🔧 **Backend Developer**:
    - [ ] Update `pew create` command in `src/index.ts` to define `--target` and handle dynamic variable options.
    - [ ] Modify `CliService.handleCreateCommand` to parse and collect these options.
    - [ ] Implement default target path logic.
    - [ ] Update command help text.
    - [ ] Write unit tests for option parsing.
* 🖥️ **Front-end Developer**:
    - [ ] Not Applicable
* 🎨 **UI/UX Designer**:
    - [ ] Not Applicable
* 🚀 **DevOps Engineer**:
    - [ ] Not Applicable
* 📊 **Data Engineer**:
    - [ ] Not Applicable
* 📣 **Marketeer**:
    - [ ] Not Applicable

# 👉️ Final Remarks
> 💡 *Anything to take note off that is not properly defined yet. Think of out of scope notes, dependencies, anything to be extra cautious about and/or information about related issues.*
---
*   This story depends on **Story 2** for the basic `pew create <templateName>` command structure.
*   The strategy for parsing dynamic `--key=value` options with Commander.js needs careful implementation. It might involve accessing `program.args` or `process.argv` if Commander doesn't directly support arbitrary named options in a simple way. A common approach is to collect all options and then filter out the known ones, treating the rest as dynamic variables.