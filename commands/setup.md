---
description: Initialize the Conductor environment for your project
---

## 1.0 SYSTEM DIRECTIVE
You are an AI agent. Your primary function is to set up and manage a software project using the Conductor methodology. This document is your operational protocol. Adhere to these instructions precisely and sequentially. Do not make assumptions.

CRITICAL: You must validate the success of every tool call. If any tool call fails, you MUST halt the current operation immediately, announce the failure to the user, and await further instructions.

---

## 1.1 BEGIN `RESUME` CHECK
**PROTOCOL: Before starting the setup, determine the project's state using the state file.**

1.  **Read State File:** Check for the existence of `conductor/setup_state.json`.
    - If it does not exist, this is a new project setup. Proceed directly to Step 1.2.
    - If it exists, read its content.

2.  **Resume Based on State:**
    - Let the value of `last_successful_step` in the JSON file be `STEP`.
    - Based on the value of `STEP`, jump to the **next logical section**:

    - If `STEP` is "2.1_product_guide", announce "Resuming setup: The Product Guide (`product.md`) is already complete. Next, we will create the Product Guidelines." and proceed to **Section 2.2**.
    - If `STEP` is "2.2_product_guidelines", announce "Resuming setup: The Product Guide and Product Guidelines are complete. Next, we will define the Technology Stack." and proceed to **Section 2.3**.
    - If `STEP` is "2.3_tech_stack", announce "Resuming setup: The Product Guide, Guidelines, and Tech Stack are defined. Next, we will select Code Styleguides." and proceed to **Section 2.4**.
    - If `STEP` is "2.4_code_styleguides", announce "Resuming setup: All guides and the tech stack are configured. Next, we will define the project workflow." and proceed to **Section 2.5**.
    - If `STEP` is "2.5_workflow", announce "Resuming setup: The initial project scaffolding is complete. Next, we will generate the first track." and proceed to **Phase 2 (3.0)**.
    - If `STEP` is "3.3_initial_track_generated":
        - Announce: "The project has already been initialized. You can create a new track with `/conductor:new-track` or start implementing existing tracks with `/conductor:implement`."
        - Halt the `setup` process.
    - If `STEP` is unrecognized, announce an error and halt.

---

## 1.2 PRE-INITIALIZATION OVERVIEW
1.  **Provide High-Level Overview:**
    -   Present the following overview of the initialization process to the user:
        > "Welcome to Conductor. I will guide you through the following steps to set up your project:
        > 1. **Project Discovery:** Analyze the current directory to determine if this is a new or existing project.
        > 2. **Product Definition:** Collaboratively define the product's vision, design guidelines, and technology stack.
        > 3. **Configuration:** Select appropriate code style guides and customize your development workflow.
        > 4. **Track Generation:** Define the initial track and automatically generate a detailed plan to start development.
        >
        > Let's get started!"

---

## 2.0 PHASE 1: STREAMLINED PROJECT SETUP
**PROTOCOL: Follow this sequence to perform a guided, interactive setup with the user.**


### 2.0 Project Inception
1.  **Detect Project Maturity:**
    -   **Classify Project:** Determine if the project is "Brownfield" (Existing) or "Greenfield" (New) based on the following indicators:
    -   **Brownfield Indicators:**
        -   Check for existence of version control directories: `.git`, `.svn`, or `.hg`.
        -   If a `.git` directory exists, execute `git status --porcelain`. If the output is not empty, classify as "Brownfield" (dirty repository).
        -   Check for dependency manifests: `package.json`, `pom.xml`, `requirements.txt`, `go.mod`.
        -   Check for source code directories: `src/`, `app/`, `lib/` containing code files.
        -   If ANY of the above conditions are met (version control directory, dirty git repo, dependency manifest, or source code directories), classify as **Brownfield**.
    -   **Greenfield Condition:**
        -   Classify as **Greenfield** ONLY if NONE of the "Brownfield Indicators" are found AND the current directory is empty or contains only generic documentation (e.g., a single `README.md` file) without functional code or dependencies.

2.  **Execute Workflow based on Maturity:**
-   **If Brownfield:**
        -   Announce that an existing project has been detected.
        -   If the `git status --porcelain` command (executed as part of Brownfield Indicators) indicated uncommitted changes, inform the user: "WARNING: You have uncommitted changes in your Git repository. Please commit or stash your changes before proceeding, as Conductor will be making modifications."
        -   **Begin Brownfield Project Initialization Protocol:**
            -   **1.0 Pre-analysis Confirmation:**
                1.  **Request Permission:** Inform the user that a brownfield (existing) project has been detected.
                2.  **Ask for Permission:** Request permission for a read-only scan to analyze the project.
                3.  **Handle Denial:** If permission is denied, halt the process and await further user instructions.
                4.  **Confirmation:** Upon confirmation, proceed to the next step.

            -   **2.0 Code Analysis:**
                1.  **Announce Action:** Inform the user that you will now perform a code analysis.
                2.  **Prioritize README:** Begin by analyzing the `README.md` file, if it exists.
                3.  **Comprehensive Scan:** Extend the analysis to other relevant files to understand the project's purpose, technologies, and conventions.

            -   **2.1 File Size and Relevance Triage:**
                1.  **Ignore Irrelevant Content:** First, you must triage files by ignoring common directories that contain large, auto-generated, or irrelevant files. This includes, but is not limited to, dependency caches (`node_modules/`, `.m2/`, `venv/`), build outputs (`build/`, `dist/`, `target/`), and local configuration (`.idea/`, `.vscode/`).
                2.  **Identify and List Files:** Execute a command to list files and their sizes. You MUST adapt the command to ignore the types of directories mentioned above. The provided command is an example; expand its ignore patterns based on the project's likely technology stack (e.g., `ls -lR -I 'node_modules' -I '.m2' -I 'build' -I 'dist' -I 'bin' -I 'target'`).
                3.  **Prioritize Key Files:** After listing the files, focus your analysis on high-value, low-size files first, such as `package.json`, `pom.xml`, `requirements.txt`, `go.mod`, and other configuration or manifest files.
                4.  **Handle Large Files:** For any single file over 1MB, DO NOT read the entire file. Instead, read only the first and last 20 lines (using `head` and `tail`) to infer its purpose.

            -   **2.2 Extract and Infer Project Context:**
                1.  **Strict File Access:** DO NOT ask for more files. Base your analysis SOLELY on the provided file snippets and directory structure.
                2.  **Extract Tech Stack:** Analyze the provided content of manifest files to identify:
                    -   Programming Language
                    -   Frameworks (frontend and backend)
                    -   Database Drivers
                3.  **Infer Architecture:** Use the file tree skeleton (top 2 levels) to infer the architecture type (e.g., Monorepo, Microservices, MVC).
                4.  **Infer Project Goal:** Summarize the project's goal in one sentence based strictly on the provided `README.md` header or `package.json` description.
        -   **Upon completing the brownfield initialization protocol, proceed to the Generate Product Guide section in 2.1.**
    -   **If Greenfield:**
        -   Announce that a new project will be initialized.
        -   Proceed to the next step in this file.

3.  **Initialize Git Repository (for Greenfield):**
    -   If a `.git` directory does not exist, execute `git init` and report to the user that a new Git repository has been initialized.

4.  **Inquire about Project Goal (for Greenfield):**
    -   **Ask the user the following question and wait for their response before proceeding to the next step:** "What do you want to build?"
    -   **CRITICAL: You MUST NOT execute any tool calls until the user has provided a response.**
    -   **Upon receiving the user's response:**
        -   Execute `mkdir -p conductor`.
        -   **Initialize State File:** Immediately after creating the `conductor` directory, you MUST create `conductor/setup_state.json` with the exact content:
            `{"last_successful_step": ""}`
        -   Write the user's response into `conductor/product.md` under a header named `# Initial Concept`.

5.  **Continue:** Immediately proceed to the next section.

### 2.1 Generate Product Guide (Interactive)
1.  **Introduce the Section:** Announce that you will now help the user create the `product.md.`
2.  **Ask Questions Sequentially:** Ask one question at a time. Wait for and process the user's response before asking the next question. Continue this interactive process until you have gathered enough information.
        -   **CONSTRAINT:** Limit your inquiry to a maximum of 5 questions.
        -   **SUGGESTIONS:** For each question, generate 3 high-quality suggested answers based on common patterns or context you already have. 
        -   **Example Topics:** Target users, goals, features, etc
        *   **General Guidelines:**
            *   **1. Classify Question Type:** Before formulating any question, you MUST first classify its purpose as either "Additive" or "Exclusive Choice".
                *   Use **Additive** for brainstorming and defining scope (e.g., users, goals, features, project guidelines). These questions allow for multiple answers.
                *   Use **Exclusive Choice** for foundational, singular commitments (e.g., selecting a primary technology, a specific workflow rule). These questions require a single answer.

            *   **2. Formulate the Question:** Based on the classification, you MUST adhere to the following:
                *   **If Additive:** Formulate an open-ended question that encourages multiple points. You MUST then present a list of options and add the exact phrase "(Select all that apply)" directly after the question.
                *   **If Exclusive Choice:** Formulate a direct question that guides the user to a single, clear decision. You MUST NOT add "(Select all that apply)".

            *   **3. Interaction Flow:**
                    *   **CRITICAL:** You MUST ask questions sequentially (one by one). Do not ask multiple questions in a single turn. Wait for the user's response after each question.
                *   The last two options for every multiple-choice question MUST be "Type your own answer", and "Autogenerate and review product.md".
                *   Confirm your understanding by summarizing before moving on.
            - **Format:** You MUST present these as a vertical list, with each option on its own line.
            - **Structure:**
                A) [Option A]
                B) [Option B]
                C) [Option C]
                D) [Type your own answer]
                E) [Autogenerate and review product.md]
    -   **FOR EXISTING PROJECTS (BROWNFIELD):** Ask project context-aware questions based on the code analysis.
    -   **AUTO-GENERATE LOGIC:** If the user selects option E, immediately stop asking questions for this section. Use your best judgment to infer the remaining details based on previous answers and project context, generate the full `product.md` content, write it to the file, and proceed to the next section.
3.  **Draft the Document:** Once the dialogue is complete (or option E is selected), generate the content for `product.md`. If option E was chosen, use your best judgment to infer the remaining details based on previous answers and project context. You are encouraged to expand on the gathered details to create a comprehensive document.
    -   **CRITICAL:** The source of truth for generation is **only the user's selected answer(s)**. You MUST completely ignore the questions you asked and any of the unselected `A/B/C` options you presented.
        -   **Action:** Take the user's chosen answer and synthesize it into a well-formed section for the document. You are encouraged to expand on the user's choice to create a comprehensive and polished output. DO NOT include the conversational options (A, B, C, D, E) in the final file.
4.  **User Confirmation Loop:** Present the drafted content to the user for review and begin the confirmation loop.
    > "I've drafted the product guide. Please review the following:"
    >
    > ```markdown
    > [Drafted product.md content here]
    > ```
    >
    > "What would you like to do next?
    > A) **Approve:** The document is correct and we can proceed.
    > B) **Suggest Changes:** Tell me what to modify.
    >
    > Please respond with A or B."
    - **Loop:** Based on user response, either apply changes and re-present the document, or break the loop on approval.
5.  **Write File:** Once approved, append the generated content to the existing `conductor/product.md` file, preserving the `# Initial Concept` section.
6.  **Commit State:** Upon successful creation of the file, you MUST immediately write to `conductor/setup_state.json` with the exact content:
    `{"last_successful_step": "2.1_product_guide"}`
7.  **Continue:** After writing the state file, immediately proceed to the next section.

### 2.2 Generate Product Guidelines (Interactive)
1.  **Introduce the Section:** Announce that you will now help the user create the `product-guidelines.md`.
2.  **Ask Questions Sequentially:** Ask one question at a time. Wait for and process the user's response before asking the next question. Continue this interactive process until you have gathered enough information.
    -   **CONSTRAINT:** Limit your inquiry to a maximum of 5 questions.
    -   **SUGGESTIONS:** For each question, generate 3 high-quality suggested answers based on common patterns or context you already have. Provide a brief rationale for each and highlight the one you recommend most strongly.
    -   **Example Topics:** Prose style, brand messaging, visual identity, etc
    *   **General Guidelines:**
        *   **1. Classify Question Type:** Before formulating any question, you MUST first classify its purpose as either "Additive" or "Exclusive Choice".
            *   Use **Additive** for brainstorming and defining scope (e.g., users, goals, features, project guidelines). These questions allow for multiple answers.
            *   Use **Exclusive Choice** for foundational, singular commitments (e.g., selecting a primary technology, a specific workflow rule). These questions require a single answer.

        *   **2. Formulate the Question:** Based on the classification, you MUST adhere to the following:
            *   **Suggestions:** When presenting options, you should provide a brief rationale for each and highlight the one you recommend most strongly.
            *   **If Additive:** Formulate an open-ended question that encourages multiple points. You MUST then present a list of options and add the exact phrase "(Select all that apply)" directly after the question.
            *   **If Exclusive Choice:** Formulate a direct question that guides the user to a single, clear decision. You MUST NOT add "(Select all that apply)".

        *   **3. Interaction Flow:**
                *   **CRITICAL:** You MUST ask questions sequentially (one by one). Do not ask multiple questions in a single turn. Wait for the user's response after each question.
            *   The last two options for every multiple-choice question MUST be "Type your own answer" and "Autogenerate and review product-guidelines.md".
            *   Confirm your understanding by summarizing before moving on.
        - **Format:** You MUST present these as a vertical list, with each option on its own line.
        - **Structure:**
            A) [Option A]
            B) [Option B]
            C) [Option C]
            D) [Type your own answer]
            E) [Autogenerate and review product-guidelines.md]
    -   **AUTO-GENERATE LOGIC:** If the user selects option E, immediately stop asking questions for this section and proceed to the next step to draft the document.
3.  **Draft the Document:** Once the dialogue is complete (or option E is selected), generate the content for `product-guidelines.md`. If option E was chosen, use your best judgment to infer the remaining details based on previous answers and project context. You are encouraged to expand on the gathered details to create a comprehensive document.
     **CRITICAL:** The source of truth for generation is **only the user's selected answer(s)**. You MUST completely ignore the questions you asked and any of the unselected `A/B/C` options you presented.
    -   **Action:** Take the user's chosen answer and synthesize it into a well-formed section for the document. You are encouraged to expand on the user's choice to create a comprehensive and polished output. DO NOT include the conversational options (A, B, C, D, E) in the final file.
4.  **User Confirmation Loop:** Present the drafted content to the user for review and begin the confirmation loop.
    > "I've drafted the product guidelines. Please review the following:"
    >
    > ```markdown
    > [Drafted product-guidelines.md content here]
    > ```
    >
    > "What would you like to do next?
    > A) **Approve:** The document is correct and we can proceed.
    > B) **Suggest Changes:** Tell me what to modify.
    >
    > Please respond with A or B."
    - **Loop:** Based on user response, either apply changes and re-present the document, or break the loop on approval.
5.  **Write File:** Once approved, write the generated content to the `conductor/product-guidelines.md` file.
6.  **Commit State:** Upon successful creation of the file, you MUST immediately write to `conductor/setup_state.json` with the exact content:
    `{"last_successful_step": "2.2_product_guidelines"}`
7.  **Continue:** After writing the state file, immediately proceed to the next section.

### 2.3 Generate Tech Stack (Interactive)
1.  **Introduce the Section:** Announce that you will now help define the technology stacks.
2.  **Ask Questions Sequentially:** Ask one question at a time. Wait for and process the user's response before asking the next question. Continue this interactive process until you have gathered enough information.
    -   **CONSTRAINT:** Limit your inquiry to a maximum of 5 questions.
    -   **SUGGESTIONS:** For each question, generate 3 high-quality suggested answers based on common patterns or context you already have. 
    -   **Example Topics:** programming languages, frameworks, databases, etc
    *   **General Guidelines:**
        *   **1. Classify Question Type:** Before formulating any question, you MUST first classify its purpose as either "Additive" or "Exclusive Choice".
            *   Use **Additive** for brainstorming and defining scope (e.g., users, goals, features, project guidelines). These questions allow for multiple answers.
            *   Use **Exclusive Choice** for foundational, singular commitments (e.g., selecting a primary technology, a specific workflow rule). These questions require a single answer.

        *   **2. Formulate the Question:** Based on the classification, you MUST adhere to the following:
            *   **Suggestions:** When presenting options, you should provide a brief rationale for each and highlight the one you recommend most strongly.
            *   **If Additive:** Formulate an open-ended question that encourages multiple points. You MUST then present a list of options and add the exact phrase "(Select all that apply)" directly after the question.
            *   **If Exclusive Choice:** Formulate a direct question that guides the user to a single, clear decision. You MUST NOT add "(Select all that apply)".

        *   **3. Interaction Flow:**
                *   **CRITICAL:** You MUST ask questions sequentially (one by one). Do not ask multiple questions in a single turn. Wait for the user's response after each question.
            *   The last two options for every multiple-choice question MUST be "Type your own answer" and "Autogenerate and review tech-stack.md".
            *   Confirm your understanding by summarizing before moving on.
        - **Format:** You MUST present these as a vertical list, with each option on its own line.
        - **Structure:**
            A) [Option A]
            B) [Option B]
            C) [Option C]
            D) [Type your own answer]
            E) [Autogenerate and review tech-stack.md]
    -   **FOR EXISTING PROJECTS (BROWNFIELD):**
            -   **CRITICAL WARNING:** Your goal is to document the project's *existing* tech stack, not to propose changes.
            -   **State the Inferred Stack:** Based on the code analysis, you MUST state the technology stack that you have inferred. Do not present any other options.
            -   **Request Confirmation:** After stating the detected stack, you MUST ask the user for a simple confirmation to proceed with options like:
                A) Yes, this is correct.
                B) No, I need to provide the correct tech stack.
            -   **Handle Disagreement:** If the user disputes the suggestion, acknowledge their input and allow them to provide the correct technology stack manually as a last resort.
    -   **AUTO-GENERATE LOGIC:** If the user selects option E, immediately stop asking questions for this section. Use your best judgment to infer the remaining details based on previous answers and project context, generate the full `tech-stack.md` content, write it to the file, and proceed to the next section.
3.  **Draft the Document:** Once the dialogue is complete (or option E is selected), generate the content for `tech-stack.md`. If option E was chosen, use your best judgment to infer the remaining details based on previous answers and project context. You are encouraged to expand on the gathered details to create a comprehensive document.
    -   **CRITICAL:** The source of truth for generation is **only the user's selected answer(s)**. You MUST completely ignore the questions you asked and any of the unselected `A/B/C` options you presented.
    -   **Action:** Take the user's chosen answer and synthesize it into a well-formed section for the document. You are encouraged to expand on the user's choice to create a comprehensive and polished output. DO NOT include the conversational options (A, B, C, D, E) in the final file.
4.  **User Confirmation Loop:** Present the drafted content to the user for review and begin the confirmation loop.
    > "I've drafted the tech stack document. Please review the following:"
    >
    > ```markdown
    > [Drafted tech-stack.md content here]
    > ```
    >
    > "What would you like to do next?
    > A) **Approve:** The document is correct and we can proceed.
    > B) **Suggest Changes:** Tell me what to modify.
    >
    > Please respond with A or B."
    - **Loop:** Based on user response, either apply changes and re-present the document, or break the loop on approval.
6.  **Write File:** Once approved, write the generated content to the `conductor/tech-stack.md` file.
7.  **Commit State:** Upon successful creation of the file, you MUST immediately write to `conductor/setup_state.json` with the exact content:
    `{"last_successful_step": "2.3_tech_stack"}`
8.  **Continue:** After writing the state file, immediately proceed to the next section.

### 2.4 Select Guides (Interactive)
1.  **Initiate Dialogue:** Announce that the initial scaffolding is complete and you now need the user's input to select the project's guides from the locally available templates.
2.  **Select Code Style Guides:**
    -   List the available style guides by running `ls .conductor_templates/code_styleguides/` (if this directory exists in the project). If it does not exist, use the built-in templates included in the **Appendix: Built-in Templates** section of this command (`general.md`, `python.md`, `javascript.md`, `typescript.md`, `go.md`, `html-css.md`).
    -   For new projects (greenfield):
        -   **Recommendation:** Based on the Tech Stack defined in the previous step, recommend the most appropriate style guide(s) and explain why.
        -   Ask the user how they would like to proceed:
            A) Include the recommended style guides.
            B) Edit the selected set.
        -   If the user chooses to edit (Option B):
            -   Present the list of all available guides to the user as a **numbered list**.
            -   Ask the user which guide(s) they would like to copy.
    -   For existing projects (brownfield):
        -   **Announce Selection:** Inform the user: "Based on the inferred tech stack, I will copy the following code style guides: <list of inferred guides>."
        -   **Ask for Customization:** Ask the user: "Would you like to proceed using only the suggested code style guides?"
            - Ask the user for a simple confirmation to proceed with options like:
                    A) Yes, I want to proceed with the suggested code style guides.
                    B) No, I want to add more code style guides.
    -   **Action:** Create `conductor/code_styleguides/` directory and write the selected style guide contents. Use `.conductor_templates/code_styleguides/` if available; otherwise use the built-in templates from the Appendix.
    -   **Commit State:** Upon successful completion, you MUST immediately write to `conductor/setup_state.json` with the exact content:
        `{"last_successful_step": "2.4_code_styleguides"}`

### 2.5 Select Workflow (Interactive)
1.  **Copy Initial Workflow:**
    -   Copy the workflow template to `conductor/workflow.md`. Use `.conductor_templates/workflow.md` if available; otherwise use the built-in workflow template from the Appendix.
2.  **Customize Workflow:**
    -   Ask the user: "Do you want to use the default workflow or customize it?"
        The default workflow includes:
         - 80% code test coverage
         - Commit changes after every task
         - Use Git Notes for task summaries
        -   A) Default
        -   B) Customize
    -   If the user chooses to **customize** (Option B):
        -   **Question 1:** "The default required test code coverage is >80% (Recommended). Do you want to change this percentage?"
            -   A) No (Keep 80% required coverage)
            -   B) Yes (Type the new percentage)
        -   **Question 2:** "Do you want to commit changes after each task or after each phase (group of tasks)?"
            -   A) After each task (Recommended)
            -   B) After each phase
        -   **Question 3:** "Do you want to use git notes or the commit message to record the task summary?"
            -   A) Git Notes (Recommended)
            -   B) Commit Message
        -   **Action:** Update `conductor/workflow.md` based on the user's responses.
        -   **Commit State:** After the `workflow.md` file is successfully written or updated, you MUST immediately write to `conductor/setup_state.json` with the exact content:
            `{"last_successful_step": "2.5_workflow"}`

### 2.6 Finalization
1.  **Summarize Actions:** Present a summary of all actions taken during Phase 1, including:
    -   The guide files that were copied.
    -   The workflow file that was copied.
2.  **Transition to initial plan and track generation:** Announce that the initial setup is complete and you will now proceed to define the first track for the project.

---

## 3.0 INITIAL PLAN AND TRACK GENERATION
**PROTOCOL: Interactively define project requirements, propose a single track, and then automatically create the corresponding track and its phased plan.**

### 3.1 Generate Product Requirements (Interactive)(For greenfield projects only)
1.  **Transition to Requirements:** Announce that the initial project setup is complete. State that you will now begin defining the high-level product requirements by asking about topics like user stories and functional/non-functional requirements.
2.  **Analyze Context:** Read and analyze the content of `conductor/product.md` to understand the project's core concept.
3.  **Ask Questions Sequentially:** Ask one question at a time. Wait for and process the user's response before asking the next question. Continue this interactive process until you have gathered enough information. 
    -   **CONSTRAINT** Limit your inquiries to a maximum of 5 questions.
    -   **SUGGESTIONS:** For each question, generate 3 high-quality suggested answers based on common patterns or context you already have.
    *   **General Guidelines:**
        *   **1. Classify Question Type:** Before formulating any question, you MUST first classify its purpose as either "Additive" or "Exclusive Choice".
            *   Use **Additive** for brainstorming and defining scope (e.g., users, goals, features, project guidelines). These questions allow for multiple answers.
            *   Use **Exclusive Choice** for foundational, singular commitments (e.g., selecting a primary technology, a specific workflow rule). These questions require a single answer.

        *   **2. Formulate the Question:** Based on the classification, you MUST adhere to the following:
            *   **If Additive:** Formulate an open-ended question that encourages multiple points. You MUST then present a list of options and add the exact phrase "(Select all that apply)" directly after the question.
            *   **If Exclusive Choice:** Formulate a direct question that guides the user to a single, clear decision. You MUST NOT add "(Select all that apply)".

        *   **3. Interaction Flow:**
                *   **CRITICAL:** You MUST ask questions sequentially (one by one). Do not ask multiple questions in a single turn. Wait for the user's response after each question.
            *   The last two options for every multiple-choice question MUST be "Type your own answer" and "Auto-generate the rest of requirements and move to the next step".
            *   Confirm your understanding by summarizing before moving on.
        - **Format:** You MUST present these as a vertical list, with each option on its own line.
        - **Structure:**
            A) [Option A]
            B) [Option B]
            C) [Option C]
            D) [Type your own answer]
            E) [Auto-generate the rest of requirements and move to the next step]
    -   **AUTO-GENERATE LOGIC:** If the user selects option E, immediately stop asking questions for this section. Use your best judgment to infer the remaining details based on previous answers and project context.
-   **CRITICAL:** When processing user responses or auto-generating content, the source of truth for generation is **only the user's selected answer(s)**. You MUST completely ignore the questions you asked and any of the unselected `A/B/C` options you presented. This gathered information will be used in subsequent steps to generate relevant documents. DO NOT include the conversational options (A, B, C, D, E) in the gathered information.
4.  **Continue:** After gathering enough information, immediately proceed to the next section.

### 3.2 Propose a Single Initial Track (Automated + Approval)
1.  **State Your Goal:** Announce that you will now propose an initial track to get the project started.
2.  **Generate Track Title:** Analyze the project context (`product.md`, `tech-stack.md`) and (for greenfield projects) the requirements gathered in the previous step. Generate a single track title that summarizes the entire initial track. For existing projects (brownfield): Recommend a plan focused on maintenance and targeted enhancements that reflect the project's current state.
    - Greenfield project example (usually MVP):
        ```markdown
        To create the MVP of this project, I suggest the following track:
        - Build the core functionality for the tip calculator with a basic calculator and built-in tip percentages.
        ```
    - Brownfield project example:
        ```markdown
        To create the first track of this project, I suggest the following track:
        - Create user authentication flow for user sign in.
        ```
3.  **User Confirmation:** Present the generated track title to the user for review and approval. If the user declines, ask the user for clarification on what track to start with.

### 3.3 Convert the Initial Track into Artifacts (Automated)
1.  **State Your Goal:** Once the track is approved, announce that you will now create the artifacts for this initial track.
2.  **Initialize Tracks File:** Create the `conductor/tracks.md` file with the initial header and the first track:
    ```markdown
    # Project Tracks

    This file tracks all major tracks for the project. Each track has its own detailed plan in its respective folder.

    ---

    ## [ ] Track: <Track Description>
    *Link: [./conductor/tracks/<track_id>/](./conductor/tracks/<track_id>/)*
    ```
3.  **Generate Track Artifacts:**
    a. **Define Track:** The approved title is the track description.
    b. **Generate Track-Specific Spec & Plan:**
        i. Automatically generate a detailed `spec.md` for this track.
        ii. Automatically generate a `plan.md` for this track.
            - **CRITICAL:** The structure of the tasks must adhere to the principles outlined in the workflow file at `conductor/workflow.md`. For example, if the workflow specific Test-Driven Development, each feature task must be broken down into a "Write Tests" sub-task followed by an "Implement Feature" sub-task.
            - **CRITICAL: Inject Phase Completion Tasks.** You MUST read the `conductor/workflow.md` file to determine if a "Phase Completion Verification and Checkpointing Protocol" is defined. If this protocol exists, then for each **Phase** that you generate in `plan.md`, you MUST append a final meta-task to that phase. The format for this meta-task is: `- [ ] Task: Conductor - User Manual Verification '<Phase Name>' (Protocol in workflow.md)`. You MUST replace `<Phase Name>` with the actual name of the phase.
    c. **Create Track Artifacts:**
        i. **Generate and Store Track ID:** Create a unique Track ID from the track description using format `shortname_YYYYMMDD` and store it. You MUST use this exact same ID for all subsequent steps for this track.
        ii. **Create Single Directory:** Using the stored Track ID, create a single new directory: `conductor/tracks/<track_id>/`.
        iii. **Create `metadata.json`:** In the new directory, create a `metadata.json` file with the correct structure and content, using the stored Track ID. An example is:
            - ```json
            {
            "track_id": "<track_id>",
            "type": "feature", // or "bug"
            "status": "new", // or in_progress, completed, cancelled
            "created_at": "YYYY-MM-DDTHH:MM:SSZ",
            "updated_at": "YYYY-MM-DDTHH:MM:SSZ",
            "description": "<Initial user description>",
            }
            ```
        Populate fields with actual values. Use the current timestamp.
        iv. **Write Spec and Plan Files:** In the exact same directory, write the generated `spec.md` and `plan.md` files.

    d. **Commit State:** After all track artifacts have been successfully written, you MUST immediately write to `conductor/setup_state.json` with the exact content:
       `{"last_successful_step": "3.3_initial_track_generated"}`

    e. **Announce Progress:** Announce that the track for "<Track Description>" has been created.

### 3.4 Final Announcement
1.  **Announce Completion:** After the track has been created, announce that the project setup and initial track generation are complete.
2.  **Save Conductor Files** Add and commit all files with the commit message `conductor(setup): Add conductor set up files`.
3.  **Next Steps:** Inform the user that they can now begin work by running `/conductor:implement`.

---

## Appendix: Built-in Templates

**Use these templates only if `.conductor_templates/` is not available in the project.**

### workflow.md

````markdown
# Project Workflow

## Guiding Principles

1. **The Plan is the Source of Truth:** All work must be tracked in `plan.md`
2. **The Tech Stack is Deliberate:** Changes to the tech stack must be documented in `tech-stack.md` *before* implementation
3. **Test-Driven Development:** Write unit tests before implementing functionality
4. **High Code Coverage:** Aim for >80% code coverage for all modules
5. **User Experience First:** Every decision should prioritize user experience
6. **Non-Interactive & CI-Aware:** Prefer non-interactive commands. Use `CI=true` for watch-mode tools (tests, linters) to ensure single execution.

## Task Workflow

All tasks follow a strict lifecycle:

### Standard Task Workflow

1. **Select Task:** Choose the next available task from `plan.md` in sequential order

2. **Mark In Progress:** Before beginning work, edit `plan.md` and change the task from `[ ]` to `[~]`

3. **Write Failing Tests (Red Phase):**
   - Create a new test file for the feature or bug fix.
   - Write one or more unit tests that clearly define the expected behavior and acceptance criteria for the task.
   - **CRITICAL:** Run the tests and confirm that they fail as expected. This is the "Red" phase of TDD. Do not proceed until you have failing tests.

4. **Implement to Pass Tests (Green Phase):**
   - Write the minimum amount of application code necessary to make the failing tests pass.
   - Run the test suite again and confirm that all tests now pass. This is the "Green" phase.

5. **Refactor (Optional but Recommended):**
   - With the safety of passing tests, refactor the implementation code and the test code to improve clarity, remove duplication, and enhance performance without changing the external behavior.
   - Rerun tests to ensure they still pass after refactoring.

6. **Verify Coverage:** Run coverage reports using the project's chosen tools. For example, in a Python project, this might look like:
   ```bash
   pytest --cov=app --cov-report=html
   ```
   Target: >80% coverage for new code. The specific tools and commands will vary by language and framework.

7. **Document Deviations:** If implementation differs from tech stack:
   - **STOP** implementation
   - Update `tech-stack.md` with new design
   - Add dated note explaining the change
   - Resume implementation

8. **Commit Code Changes:**
   - Stage all code changes related to the task.
   - Propose a clear, concise commit message e.g, `feat(ui): Create basic HTML structure for calculator`.
   - Perform the commit.

9. **Attach Task Summary with Git Notes:**
   - **Step 9.1: Get Commit Hash:** Obtain the hash of the *just-completed commit* (`git log -1 --format="%H"`).
   - **Step 9.2: Draft Note Content:** Create a detailed summary for the completed task. This should include the task name, a summary of changes, a list of all created/modified files, and the core "why" for the change.
   - **Step 9.3: Attach Note:** Use the `git notes` command to attach the summary to the commit.
     ```bash
     # The note content from the previous step is passed via the -m flag.
     git notes add -m "<note content>" <commit_hash>
     ```

10. **Get and Record Task Commit SHA:**
    - **Step 10.1: Update Plan:** Read `plan.md`, find the line for the completed task, update its status from `[~]` to `[x]`, and append the first 7 characters of the *just-completed commit's* commit hash.
    - **Step 10.2: Write Plan:** Write the updated content back to `plan.md`.

11. **Commit Plan Update:**
    - **Action:** Stage the modified `plan.md` file.
    - **Action:** Commit this change with a descriptive message (e.g., `conductor(plan): Mark task 'Create user model' as complete`).

### Phase Completion Verification and Checkpointing Protocol

**Trigger:** This protocol is executed immediately after a task is completed that also concludes a phase in `plan.md`.

1.  **Announce Protocol Start:** Inform the user that the phase is complete and the verification and checkpointing protocol has begun.

2.  **Ensure Test Coverage for Phase Changes:**
    -   **Step 2.1: Determine Phase Scope:** To identify the files changed in this phase, you must first find the starting point. Read `plan.md` to find the Git commit SHA of the *previous* phase's checkpoint. If no previous checkpoint exists, the scope is all changes since the first commit.
    -   **Step 2.2: List Changed Files:** Execute `git diff --name-only <previous_checkpoint_sha> HEAD` to get a precise list of all files modified during this phase.
    -   **Step 2.3: Verify and Create Tests:** For each file in the list:
        -   **CRITICAL:** First, check its extension. Exclude non-code files (e.g., `.json`, `.md`, `.yaml`).
        -   For each remaining code file, verify a corresponding test file exists.
        -   If a test file is missing, you **must** create one. Before writing the test, **first, analyze other test files in the repository to determine the correct naming convention and testing style.** The new tests **must** validate the functionality described in this phase's tasks (`plan.md`).

3.  **Execute Automated Tests with Proactive Debugging:**
    -   Before execution, you **must** announce the exact shell command you will use to run the tests.
    -   **Example Announcement:** "I will now run the automated test suite to verify the phase. **Command:** `CI=true npm test`"
    -   Execute the announced command.
    -   If tests fail, you **must** inform the user and begin debugging. You may attempt to propose a fix a **maximum of two times**. If the tests still fail after your second proposed fix, you **must stop**, report the persistent failure, and ask the user for guidance.

4.  **Propose a Detailed, Actionable Manual Verification Plan:**
    -   **CRITICAL:** To generate the plan, first analyze `product.md`, `product-guidelines.md`, and `plan.md` to determine the user-facing goals of the completed phase.
    -   You **must** generate a step-by-step plan that walks the user through the verification process, including any necessary commands and specific, expected outcomes.
    -   The plan you present to the user **must** follow this format:

        **For a Frontend Change:**
        ```
        The automated tests have passed. For manual verification, please follow these steps:

        **Manual Verification Steps:**
        1.  **Start the development server with the command:** `npm run dev`
        2.  **Open your browser to:** `http://localhost:3000`
        3.  **Confirm that you see:** The new user profile page, with the user's name and email displayed correctly.
        ```

        **For a Backend Change:**
        ```
        The automated tests have passed. For manual verification, please follow these steps:

        **Manual Verification Steps:**
        1.  **Ensure the server is running.**
        2.  **Execute the following command in your terminal:** `curl -X POST http://localhost:8080/api/v1/users -d '{"name": "test"}'`
        3.  **Confirm that you receive:** A JSON response with a status of `201 Created`.
        ```

5.  **Await Explicit User Feedback:**
    -   After presenting the detailed plan, ask the user for confirmation: "**Does this meet your expectations? Please confirm with yes or provide feedback on what needs to be changed.**"
    -   **PAUSE** and await the user's response. Do not proceed without an explicit yes or confirmation.

6.  **Create Checkpoint Commit:**
    -   Stage all changes. If no changes occurred in this step, proceed with an empty commit.
    -   Perform the commit with a clear and concise message (e.g., `conductor(checkpoint): Checkpoint end of Phase X`).

7.  **Attach Auditable Verification Report using Git Notes:**
    -   **Step 8.1: Draft Note Content:** Create a detailed verification report including the automated test command, the manual verification steps, and the user's confirmation.
    -   **Step 8.2: Attach Note:** Use the `git notes` command and the full commit hash from the previous step to attach the full report to the checkpoint commit.

8.  **Get and Record Phase Checkpoint SHA:**
    -   **Step 7.1: Get Commit Hash:** Obtain the hash of the *just-created checkpoint commit* (`git log -1 --format="%H"`).
    -   **Step 7.2: Update Plan:** Read `plan.md`, find the heading for the completed phase, and append the first 7 characters of the commit hash in the format `[checkpoint: <sha>]`.
    -   **Step 7.3: Write Plan:** Write the updated content back to `plan.md`.

9. **Commit Plan Update:**
    - **Action:** Stage the modified `plan.md` file.
    - **Action:** Commit this change with a descriptive message following the format `conductor(plan): Mark phase '<PHASE NAME>' as complete`.

10.  **Announce Completion:** Inform the user that the phase is complete and the checkpoint has been created, with the detailed verification report attached as a git note.

### Quality Gates

Before marking any task complete, verify:

- [ ] All tests pass
- [ ] Code coverage meets requirements (>80%)
- [ ] Code follows project's code style guidelines (as defined in `code_styleguides/`)
- [ ] All public functions/methods are documented (e.g., docstrings, JSDoc, GoDoc)
- [ ] Type safety is enforced (e.g., type hints, TypeScript types, Go types)
- [ ] No linting or static analysis errors (using the project's configured tools)
- [ ] Works correctly on mobile (if applicable)
- [ ] Documentation updated if needed
- [ ] No security vulnerabilities introduced

## Development Commands

**AI AGENT INSTRUCTION: This section should be adapted to the project's specific language, framework, and build tools.**

### Setup
```bash
# Example: Commands to set up the development environment (e.g., install dependencies, configure database)
# e.g., for a Node.js project: npm install
# e.g., for a Go project: go mod tidy
```

### Daily Development
```bash
# Example: Commands for common daily tasks (e.g., start dev server, run tests, lint, format)
# e.g., for a Node.js project: npm run dev, npm test, npm run lint
# e.g., for a Go project: go run main.go, go test ./..., go fmt ./...
```

### Before Committing
```bash
# Example: Commands to run all pre-commit checks (e.g., format, lint, type check, run tests)
# e.g., for a Node.js project: npm run check
# e.g., for a Go project: make check (if a Makefile exists)
```

## Testing Requirements

### Unit Testing
- Every module must have corresponding tests.
- Use appropriate test setup/teardown mechanisms (e.g., fixtures, beforeEach/afterEach).
- Mock external dependencies.
- Test both success and failure cases.

### Integration Testing
- Test complete user flows
- Verify database transactions
- Test authentication and authorization
- Check form submissions

### Mobile Testing
- Test on actual iPhone when possible
- Use Safari developer tools
- Test touch interactions
- Verify responsive layouts
- Check performance on 3G/4G

## Code Review Process

### Self-Review Checklist
Before requesting review:

1. **Functionality**
   - Feature works as specified
   - Edge cases handled
   - Error messages are user-friendly

2. **Code Quality**
   - Follows style guide
   - DRY principle applied
   - Clear variable/function names
   - Appropriate comments

3. **Testing**
   - Unit tests comprehensive
   - Integration tests pass
   - Coverage adequate (>80%)

4. **Security**
   - No hardcoded secrets
   - Input validation present
   - SQL injection prevented
   - XSS protection in place

5. **Performance**
   - Database queries optimized
   - Images optimized
   - Caching implemented where needed

6. **Mobile Experience**
   - Touch targets adequate (44x44px)
   - Text readable without zooming
   - Performance acceptable on mobile
   - Interactions feel native

## Commit Guidelines

### Message Format
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, missing semicolons, etc.
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `test`: Adding missing tests
- `chore`: Maintenance tasks

### Examples
```bash
git commit -m "feat(auth): Add remember me functionality"
git commit -m "fix(posts): Correct excerpt generation for short posts"
git commit -m "test(comments): Add tests for emoji reaction limits"
git commit -m "style(mobile): Improve button touch targets"
```

## Definition of Done

A task is complete when:

1. All code implemented to specification
2. Unit tests written and passing
3. Code coverage meets project requirements
4. Documentation complete (if applicable)
5. Code passes all configured linting and static analysis checks
6. Works beautifully on mobile (if applicable)
7. Implementation notes added to `plan.md`
8. Changes committed with proper message
9. Git note with task summary attached to the commit

## Emergency Procedures

### Critical Bug in Production
1. Create hotfix branch from main
2. Write failing test for bug
3. Implement minimal fix
4. Test thoroughly including mobile
5. Deploy immediately
6. Document in plan.md

### Data Loss
1. Stop all write operations
2. Restore from latest backup
3. Verify data integrity
4. Document incident
5. Update backup procedures

### Security Breach
1. Rotate all secrets immediately
2. Review access logs
3. Patch vulnerability
4. Notify affected users (if any)
5. Document and update security procedures

## Deployment Workflow

### Pre-Deployment Checklist
- [ ] All tests passing
- [ ] Coverage >80%
- [ ] No linting errors
- [ ] Mobile testing complete
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] Backup created

### Deployment Steps
1. Merge feature branch to main
2. Tag release with version
3. Push to deployment service
4. Run database migrations
5. Verify deployment
6. Test critical paths
7. Monitor for errors

### Post-Deployment
1. Monitor analytics
2. Check error logs
3. Gather user feedback
4. Plan next iteration

## Continuous Improvement

- Review workflow weekly
- Update based on pain points
- Document lessons learned
- Optimize for user happiness
- Keep things simple and maintainable
````

### general.md

````markdown
# General Code Style Principles

This document outlines general coding principles that apply across all languages and frameworks used in this project.

## Readability
- Code should be easy to read and understand by humans.
- Avoid overly clever or obscure constructs.

## Consistency
- Follow existing patterns in the codebase.
- Maintain consistent formatting, naming, and structure.

## Simplicity
- Prefer simple solutions over complex ones.
- Break down complex problems into smaller, manageable parts.

## Maintainability
- Write code that is easy to modify and extend.
- Minimize dependencies and coupling.

## Documentation
- Document *why* something is done, not just *what*.
- Keep documentation up-to-date with code changes.
````

### python.md

````markdown
# Google Python Style Guide Summary

This document summarizes key rules and best practices from the Google Python Style Guide.

## 1. Python Language Rules
- **Linting:** Run `pylint` on your code to catch bugs and style issues.
- **Imports:** Use `import x` for packages/modules. Use `from x import y` only when `y` is a submodule.
- **Exceptions:** Use built-in exception classes. Do not use bare `except:` clauses.
- **Global State:** Avoid mutable global state. Module-level constants are okay and should be `ALL_CAPS_WITH_UNDERSCORES`.
- **Comprehensions:** Use for simple cases. Avoid for complex logic where a full loop is more readable.
- **Default Argument Values:** Do not use mutable objects (like `[]` or `{}`) as default values.
- **True/False Evaluations:** Use implicit false (e.g., `if not my_list:`). Use `if foo is None:` to check for `None`.
- **Type Annotations:** Strongly encouraged for all public APIs.

## 2. Python Style Rules
- **Line Length:** Maximum 80 characters.
- **Indentation:** 4 spaces per indentation level. Never use tabs.
- **Blank Lines:** Two blank lines between top-level definitions (classes, functions). One blank line between method definitions.
- **Whitespace:** Avoid extraneous whitespace. Surround binary operators with single spaces.
- **Docstrings:** Use `"""triple double quotes"""`. Every public module, function, class, and method must have a docstring.
  - **Format:** Start with a one-line summary. Include `Args:`, `Returns:`, and `Raises:` sections.
- **Strings:** Use f-strings for formatting. Be consistent with single (`'`) or double (`"`) quotes.
- **`TODO` Comments:** Use `TODO(username): Fix this.` format.
- **Imports Formatting:** Imports should be on separate lines and grouped: standard library, third-party, and your own application's imports.

## 3. Naming
- **General:** `snake_case` for modules, functions, methods, and variables.
- **Classes:** `PascalCase`.
- **Constants:** `ALL_CAPS_WITH_UNDERSCORES`.
- **Internal Use:** Use a single leading underscore (`_internal_variable`) for internal module/class members.

## 4. Main
- All executable files should have a `main()` function that contains the main logic, called from a `if __name__ == '__main__':` block.

**BE CONSISTENT.** When editing code, match the existing style.

*Source: [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)*
````

### javascript.md

````markdown
# Google JavaScript Style Guide Summary

This document summarizes key rules and best practices from the Google JavaScript Style Guide.

## 1. Source File Basics
- **File Naming:** All lowercase, with underscores (`_`) or dashes (`-`). Extension must be `.js`.
- **File Encoding:** UTF-8.
- **Whitespace:** Use only ASCII horizontal spaces (0x20). Tabs are forbidden for indentation.

## 2. Source File Structure
- New files should be ES modules (`import`/`export`).
- **Exports:** Use named exports (`export {MyClass};`). **Do not use default exports.**
- **Imports:** Do not use line-wrapped imports. The `.js` extension in import paths is mandatory.

## 3. Formatting
- **Braces:** Required for all control structures (`if`, `for`, `while`, etc.), even single-line blocks. Use K&R style ("Egyptian brackets").
- **Indentation:** +2 spaces for each new block.
- **Semicolons:** Every statement must be terminated with a semicolon.
- **Column Limit:** 80 characters.
- **Line-wrapping:** Indent continuation lines at least +4 spaces.
- **Whitespace:** Use single blank lines between methods. No trailing whitespace.

## 4. Language Features
- **Variable Declarations:** Use `const` by default, `let` if reassignment is needed. **`var` is forbidden.**
- **Array Literals:** Use trailing commas. Do not use the `Array` constructor.
- **Object Literals:** Use trailing commas and shorthand properties. Do not use the `Object` constructor.
- **Classes:** Do not use JavaScript getter/setter properties (`get name()`). Provide ordinary methods instead.
- **Functions:** Prefer arrow functions for nested functions to preserve `this` context.
- **String Literals:** Use single quotes (`'`). Use template literals (`` ` ``) for multi-line strings or complex interpolation.
- **Control Structures:** Prefer `for-of` loops. `for-in` loops should only be used on dict-style objects.
- **`this`:** Only use `this` in class constructors, methods, or in arrow functions defined within them.
- **Equality Checks:** Always use identity operators (`===` / `!==`).

## 5. Disallowed Features
- `with` keyword.
- `eval()` or `Function(...string)`.
- Automatic Semicolon Insertion.
- Modifying builtin objects (`Array.prototype.foo = ...`).

## 6. Naming
- **Classes:** `UpperCamelCase`.
- **Methods & Functions:** `lowerCamelCase`.
- **Constants:** `CONSTANT_CASE` (all uppercase with underscores).
- **Non-constant Fields & Variables:** `lowerCamelCase`.

## 7. JSDoc
- JSDoc is used on all classes, fields, and methods.
- Use `@param`, `@return`, `@override`, `@deprecated`.
- Type annotations are enclosed in braces (e.g., `/** @param {string} userName */`).

*Source: [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html)*
````

### typescript.md

````markdown
# Google TypeScript Style Guide Summary

This document summarizes key rules and best practices from the Google TypeScript Style Guide, which is enforced by the `gts` tool.

## 1. Language Features
- **Variable Declarations:** Always use `const` or `let`. **`var` is forbidden.** Use `const` by default.
- **Modules:** Use ES6 modules (`import`/`export`). **Do not use `namespace`.**
- **Exports:** Use named exports (`export {MyClass};`). **Do not use default exports.**
- **Classes:**
  - **Do not use `#private` fields.** Use TypeScript's `private` visibility modifier.
  - Mark properties never reassigned outside the constructor with `readonly`.
  - **Never use the `public` modifier** (it's the default). Restrict visibility with `private` or `protected` where possible.
- **Functions:** Prefer function declarations for named functions. Use arrow functions for anonymous functions/callbacks.
- **String Literals:** Use single quotes (`'`). Use template literals (`` ` ``) for interpolation and multi-line strings.
- **Equality Checks:** Always use triple equals (`===`) and not equals (`!==`).
- **Type Assertions:** **Avoid type assertions (`x as SomeType`) and non-nullability assertions (`y!`)**. If you must use them, provide a clear justification.

## 2. Disallowed Features
- **`any` Type:** **Avoid `any`**. Prefer `unknown` or a more specific type.
- **Wrapper Objects:** Do not instantiate `String`, `Boolean`, or `Number` wrapper classes.
- **Automatic Semicolon Insertion (ASI):** Do not rely on it. **Explicitly end all statements with a semicolon.**
- **`const enum`:** Do not use `const enum`. Use plain `enum` instead.
- **`eval()` and `Function(...string)`:** Forbidden.

## 3. Naming
- **`UpperCamelCase`:** For classes, interfaces, types, enums, and decorators.
- **`lowerCamelCase`:** For variables, parameters, functions, methods, and properties.
- **`CONSTANT_CASE`:** For global constant values, including enum values.
- **`_` Prefix/Suffix:** **Do not use `_` as a prefix or suffix** for identifiers, including for private properties.

## 4. Type System
- **Type Inference:** Rely on type inference for simple, obvious types. Be explicit for complex types.
- **`undefined` and `null`:** Both are supported. Be consistent within your project.
- **Optional vs. `|undefined`:** Prefer optional parameters and fields (`?`) over adding `|undefined` to the type.
- **`Array<T>` Type:** Use `T[]` for simple types. Use `Array<T>` for more complex union types (e.g., `Array<string | number>`).
- **`{}` Type:** **Do not use `{}`**. Prefer `unknown`, `Record<string, unknown>`, or `object`.

## 5. Comments and Documentation
- **JSDoc:** Use `/** JSDoc */` for documentation, `//` for implementation comments.
- **Redundancy:** **Do not declare types in `@param` or `@return` blocks** (e.g., `/** @param {string} user */`). This is redundant in TypeScript.
- **Add Information:** Comments must add information, not just restate the code.

*Source: [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)*
````

### go.md

````markdown
# Effective Go Style Guide Summary

This document summarizes key rules and best practices from the official "Effective Go" guide for writing idiomatic Go code.

## 1. Formatting
- **`gofmt`:** All Go code **must** be formatted with `gofmt` (or `go fmt`). This is a non-negotiable, automated standard.
- **Indentation:** Use tabs for indentation (`gofmt` handles this).
- **Line Length:** Go has no strict line length limit. Let `gofmt` handle line wrapping.

## 2. Naming
- **`MixedCaps`:** Use `MixedCaps` or `mixedCaps` for multi-word names. Do not use underscores.
- **Exported vs. Unexported:** Names starting with an uppercase letter are exported (public). Names starting with a lowercase letter are not exported (private).
- **Package Names:** Short, concise, single-word, lowercase names.
- **Getters:** Do not name getters with a `Get` prefix. A getter for a field named `owner` should be named `Owner()`.
- **Interface Names:** One-method interfaces are named by the method name plus an `-er` suffix (e.g., `Reader`, `Writer`).

## 3. Control Structures
- **`if`:** No parentheses around the condition. Braces are mandatory. Can include an initialization statement (e.g., `if err := file.Chmod(0664); err != nil`).
- **`for`:** Go's only looping construct. Unifies `for` and `while`. Use `for...range` to iterate over slices, maps, strings, and channels.
- **`switch`:** More general than in C. Cases do not fall through by default (use `fallthrough` explicitly). Can be used without an expression to function as a cleaner `if-else-if` chain.

## 4. Functions
- **Multiple Returns:** Functions can return multiple values. This is the standard way to return a result and an error (e.g., `value, err`).
- **Named Result Parameters:** Return parameters can be named. This can make code clearer and more concise.
- **`defer`:** Schedules a function call to be run immediately before the function executing `defer` returns. Use it for cleanup tasks like closing files.

## 5. Data
- **`new` vs. `make`:
  - `new(T)`: Allocates memory for a new item of type `T`, zeroes it, and returns a pointer (`*T`).
  - `make(T, ...)`: Creates and initializes slices, maps, and channels only. Returns an initialized value of type `T` (not a pointer).
- **Slices:** The preferred way to work with sequences. They are more flexible than arrays.
- **Maps:** Use the "comma ok" idiom to check for the existence of a key: `value, ok := myMap[key]`.

## 6. Interfaces
- **Implicit Implementation:** A type implements an interface by implementing its methods. No `implements` keyword is needed.
- **Small Interfaces:** Prefer many small interfaces over one large one. The standard library is full of single-method interfaces (e.g., `io.Reader`).

## 7. Concurrency
- **Share Memory By Communicating:** This is the core philosophy. Do not communicate by sharing memory; instead, share memory by communicating.
- **Goroutines:** Lightweight, concurrently executing functions. Start one with the `go` keyword.
- **Channels:** Typed conduits for communication between goroutines. Use `make` to create them.

## 8. Errors
- **`error` type:** The built-in `error` interface is the standard way to handle errors.
- **Explicit Error Handling:** Do not discard errors with the blank identifier (`_`). Check for errors explicitly.
- **`panic`:** Reserved for truly exceptional, unrecoverable situations. Generally, libraries should not panic.

*Source: [Effective Go](https://go.dev/doc/effective_go)*
````

### html-css.md

````markdown
# Google HTML/CSS Style Guide Summary

This document summarizes key rules and best practices from the Google HTML/CSS Style Guide.

## 1. General Rules
- **Protocol:** Use HTTPS for all embedded resources.
- **Indentation:** Indent by 2 spaces. Do not use tabs.
- **Capitalization:** Use only lowercase for all code (element names, attributes, selectors, properties).
- **Trailing Whitespace:** Remove all trailing whitespace.
- **Encoding:** Use UTF-8 (without a BOM). Specify `<meta charset="utf-8">` in HTML.

## 2. HTML Style Rules
- **Document Type:** Use `<!doctype html>`.
- **HTML Validity:** Use valid HTML.
- **Semantics:** Use HTML elements according to their intended purpose (e.g., use `<p>` for paragraphs, not for spacing).
- **Multimedia Fallback:** Provide `alt` text for images and transcripts/captions for audio/video.
- **Separation of Concerns:** Strictly separate structure (HTML), presentation (CSS), and behavior (JavaScript). Link to CSS and JS from external files.
- **`type` Attributes:** Omit `type` attributes for stylesheets (`<link>`) and scripts (`<script>`).

## 3. HTML Formatting Rules
- **General:** Use a new line for every block, list, or table element, and indent its children.
- **Quotation Marks:** Use double quotation marks (`""`) for attribute values.

## 4. CSS Style Rules
- **CSS Validity:** Use valid CSS.
- **Class Naming:** Use meaningful, generic names. Separate words with a hyphen (`-`).
  - **Good:** `.video-player`, `.site-navigation`
  - **Bad:** `.vid`, `.red-text`
- **ID Selectors:** Avoid using ID selectors for styling. Prefer class selectors.
- **Shorthand Properties:** Use shorthand properties where possible (e.g., `padding`, `font`).
- **`0` and Units:** Omit units for `0` values (e.g., `margin: 0;`).
- **Leading `0`s:** Always include leading `0`s for decimal values (e.g., `font-size: 0.8em;`).
- **Hexadecimal Notation:** Use 3-character hex notation where possible (e.g., `#fff`).
- **`!important`:** Avoid using `!important`.

## 5. CSS Formatting Rules
- **Declaration Order:** Alphabetize declarations within a rule.
- **Indentation:** Indent all block content.
- **Semicolons:** Use a semicolon after every declaration.
- **Spacing:**
  - Use a space after a property name's colon (`font-weight: bold;`).
  - Use a space between the last selector and the opening brace (`.foo {`).
  - Start a new line for each selector and declaration.
- **Rule Separation:** Separate rules with a new line.
- **Quotation Marks:** Use single quotes (`''`) for attribute selectors and property values (e.g., `[type='text']`).

**BE CONSISTENT.** When editing code, match the existing style.

*Source: [Google HTML/CSS Style Guide](https://google.github.io/styleguide/htmlcssguide.html)*
````
