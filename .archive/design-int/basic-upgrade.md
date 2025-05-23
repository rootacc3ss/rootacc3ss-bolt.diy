Read this chat, it outlines how we could make a basic upgrade:

The search results point to a few key files:
app/utils/constants.ts: This file defines STARTER_TEMPLATES. Each template object has a name, description, githubRepo, tags, and an icon. This is the primary source of available stacks/templates. Examples:
Expo App (xKevIsDev/bolt-expo-template)
Basic Astro (xKevIsDev/bolt-astro-basic-template)
NextJS Shadcn (xKevIsDev/bolt-nextjs-shadcn-template)
Vite Shadcn (xKevIsDev/vite-shadcn)
Remix Typescript (xKevIsDev/bolt-remix-ts-template)
And many more for Qwik, Slidev, SvelteKit, Vanilla Vite, Vue, Angular.

app/utils/selectStarterTemplate.ts:
This file contains the logic for the LLM to select a starter template based on user input. It constructs a prompt (starterTemplateSelectionPrompt) that lists available templates (from STARTER_TEMPLATES in constants.ts, though it filters out "shadcn" templates for this specific prompt, which is interesting).
It then makes an API call to /api/llmcall with the user's request and this system prompt.
The LLM's response (expected in an XML format like <selection><templateName>...</templateName>...) is parsed to get the chosen template name.
The getTemplates function fetches the actual files for the selected template from its specified GitHub repository (using /api/github-template as a proxy).
It also looks for special files within the fetched template in a .bolt directory, specifically prompt (to give further instructions to the LLM after template import) and ignore (to specify read-only files).

app/components/chat/StarterTemplates.tsx: This React component displays the available starter templates in the UI, allowing users to click and initiate a project from a template. The links directly point to /git?url=https://github.com/${template.githubRepo}.git.
app/utils/fileUtils.ts and app/utils/projectCommands.ts: These files contain logic to detect the project type (Node.js, Static) and determine setup/start commands (e.g., npm install && npm run dev) by inspecting files like package.json or index.html after files are in the workspace. This is more about understanding an existing set of files rather than defining a new stack from scratch.

---

How bolt.diy determines the tech stack for generated webapps:
Predefined Starter Templates: The core mechanism is a list of starter templates, each pointing to a GitHub repository. These repositories contain the boilerplate code (dependencies in package.json, initial file structure, basic configuration) for a specific stack (e.g., Next.js + Tailwind, Vite + React + TypeScript).
LLM-Assisted Selection: When a user describes their project, an LLM helps choose the most suitable starter template from the predefined list.
Template Fetching: bolt.diy fetches the chosen template's files from GitHub.
Further LLM Guidance (Optional): If the fetched template includes a .bolt/prompt file, its contents are used as further instructions for the LLM to continue building upon the template.

---

IMPORTANT:

Hypothesizing how to make additions to that stack:
There are a few ways to add or modify the tech stacks that bolt.diy can generate:
Modify Existing Starter Templates (Directly in GitHub):
The most straightforward way is to modify the an existing template in xKevIsDev's GitHub repositories that are referenced in STARTER_TEMPLATES. For example, to add a new default library to the "Vite React" template, you would fork xKevIsDev/bolt-vite-react-ts-template, add the library and any necessary setup, and then update the githubRepo URL in app/utils/constants.ts to point to your fork.
You could also propose changes to the upstream xKevIsDev repositories directly.

Add New Starter Templates:
Create a new GitHub repository containing the boilerplate for your desired new stack (e.g., a SvelteKit project with specific database adapters pre-configured).
Add a new entry to the STARTER_TEMPLATES array in app/utils/constants.ts, providing the name, description, githubRepo (pointing to your new template repo), tags, and icon.
The LLM selection prompt in app/utils/selectStarterTemplate.ts should automatically pick up this new template if its description and tags match user queries.

Enhance the LLM Prompt System for In-Flight Modifications (More Complex):
This is how the Supabase integration itself works. Instead of having a separate template for every variation, the base template is more generic, and the LLM is instructed (via the main system prompt and potentially template-specific .bolt/prompt files) to add specific dependencies or configurations.
To add a new technology (e.g., a new database or UI library) using this method:
Update Prompts: Modify the core LLM prompts (likely found in app/lib/common/prompts/ or constructed dynamically) to include instructions for using this new technology. This would involve:
Telling the LLM when to use it (e.g., "If the user asks for a real-time database, use X").
How to install it (e.g., npm install new-library).
How to configure it (e.g., "Create a new-library.config.js file with the following content...").
Best practices for its usage.
Update Action Handling (If Necessary): If the new technology requires special actions beyond file creation or terminal commands (like the Supabase query confirmation), you might need to update app/lib/runtime/action-runner.ts.
Update Environment Variable Handling: If it needs environment variables, ensure the prompts instruct the LLM to add them to the .env file, similar to how VITE_SUPABASE_URL is handled.

Combination Approach: Use a base starter template and then heavily guide the LLM via a .bolt/prompt file within that template to customize it further. This keeps the number of distinct GitHub template repositories manageable.

Where to find the prompts:
The search showed app/utils/selectStarterTemplate.ts has a starterTemplateSelectionPrompt.
The supa-int/1.md document extensively quoted prompt snippets related to Supabase. These are likely part of a larger system prompt or a set of conditional prompts.
The directory app/lib/common/prompts/ is mentioned in intro.md as the location for "LLM prompt templates and libraries." This is the most probable place to find the main system prompts that define how bolt.diy instructs the LLM to build applications, including which default dependencies and technologies to use beyond the initial template.
I'll now search within app/lib/common/prompts/ to get a better idea of how these prompts are structured and how they define the default stack.

Okay, we have three potentially relevant files in app/lib/common/prompts/:
new-prompt.ts
optimized.ts
prompts.ts
Given their names, prompts.ts or new-prompt.ts are the most likely candidates for the main, active prompt. optimized.ts might be a variation.
To understand how the default stack (beyond the starter template) is defined and how to add to it, I'll read prompts.ts first, as it seems like a general or foundational prompt file. If that doesn't yield the answer, I'll check new-prompt.ts.
This prompts.ts file (specifically the getSystemPrompt function) is a goldmine. It constructs a detailed system prompt for the LLM, defining its persona, constraints, available tools, and crucially, instructions for various tasks including database operations and, by extension, the default technologies.
Here's what I've gathered regarding how bolt.diy defines the stack and how to add to it, focusing on the parts beyond the initial starter template:

Core Philosophy: LLM-Driven Development with Strong Guidance
bolt.diy doesn't just use an LLM; it micromanages it with extremely detailed prompts. This ensures consistency and adherence to best practices for the code it generates.
The system prompt explicitly states limitations of the WebContainer environment (e.g., no pip for Python, no C/C++ compiler, prefer Vite, Git not available within the container for the LLM's direct use).

Default Technologies (as per prompts.ts):
Web Server: "IMPORTANT: Prefer using Vite instead of implementing a custom web server."
Scripting: "IMPORTANT: Prefer writing Node.js scripts instead of shell scripts."
Databases:
"CRITICAL: Use Supabase for databases by default, unless specified otherwise."
For other databases (if Supabase isn't chosen/available), it advises: "prefer options that don't rely on native binaries. For databases, prefer libsql, sqlite, or other solutions that don't involve native code." This is a WebContainer constraint.
Supabase Client: "@supabase/supabase-js"
Authentication (with Supabase): "ALWAYS use email and password sign up... FORBIDDEN: NEVER use magic links, social providers, or SSO... ALWAYS use Supabase's built-in authentication!"

How Technologies are Enforced/Introduced by the LLM:
Directives in the System Prompt: The examples above show direct commands to the LLM.
File Creation & Modification: The LLM is instructed to create/modify files (e.g., .env for Supabase credentials, /supabase/migrations/ for SQL).
Package Installation: The LLM will generate npm install some-package commands, which are then executed in the WebContainer terminal.
Code Generation: The LLM writes the actual code to import and use these libraries (e.g., creating a Supabase client singleton).

How to Add New Technologies or Modify the Stack (Beyond Starter Templates):
The most robust way to influence the stack after a starter template has been selected (or if starting from blank) is to modify the system prompt in app/lib/common/prompts/prompts.ts (or potentially new-prompt.ts if that's the active one).
Let's say you want to make "Tailwind CSS" a default styling solution if no other styling is specified, or introduce a new charting library "ChartJS" as the default for visualizations.

Steps to Hypothesize an Addition (e.g., Making ChartJS the default charting library):
Identify the Relevant Prompt Section:
There isn't an explicit "styling_instructions" or "visualization_instructions" section in the visible part of prompts.ts. You'd likely add a new section or integrate instructions into a more general "frontend development" or "UI generation" part of the prompt. If no such section exists, you'd create one.

Draft New Prompt Instructions:
Default Choice: "CRITICAL: For data visualizations and charts, use Chart.js by default, unless specified otherwise."
Installation: "If Chart.js is needed and not already in package.json, instruct the user or add a boltAction to run npm install chart.js react-chartjs-2 (if using React)." (Note: react-chartjs-2 is a common wrapper for React).
Import & Usage: "When generating chart components, import Bar, Line, etc., from react-chartjs-2 and Chart from chart.js/auto. Ensure Chart.register(...ArcElement, ...Tooltip, ...Legend); or similar registrations are done."
Best Practices: "Advise on creating reusable chart components. Remind to pass data and options props effectively. Ensure charts are responsive."
Alternatives (if Chart.js is unsuitable): "If Chart.js is not suitable for a specific complex visualization, consider D3.js, but acknowledge its steeper learning curve and only if the user's project complexity justifies it."

Consider WebContainer Constraints:
Chart.js is a JavaScript library, so it's perfectly fine for WebContainer. If you were adding something with native dependencies, you'd hit issues.

Update package.json in Starter Templates (Optional but Recommended for Common Additions):
If Chart.js becomes a very common addition, you might also add it directly to the dependencies in the package.json of relevant starter templates (e.g., "Vite React," "NextJS Shadcn") to save the npm install step during generation. This is a trade-off: it makes templates bigger but speeds up initial setup for that specific stack.
Test Thoroughly: After modifying prompts, extensive testing is needed to see how the LLM interprets the new instructions in various scenarios. Prompt engineering is iterative.

Key Files for Modifying Generated Stack (Post-Template):
app/lib/common/prompts/prompts.ts (or new-prompt.ts): This is the brain. Modifications here have the broadest impact on LLM behavior for generating code within a project.
app/utils/constants.ts: To add/modify the initial set of starter templates.
The GitHub repositories referenced in STARTER_TEMPLATES: For changing the base code of those templates.

This dual approach (starter templates + detailed LLM prompting) gives bolt.diy a lot of flexibility. It can provide common starting points quickly and then use the LLM to tailor them or build more complex features on top, all while trying to adhere to the defined best practices and constraints.
Would you like me to proceed with adding the .archive folder to Git, or do you have more questions about the codebase structure or how to modify the generated tech stack? We can also look into new-prompt.ts if you think that might be the more current prompt file being used.
