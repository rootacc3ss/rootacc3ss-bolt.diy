# Integrating a Modern, Opinionated Tech Stack into Bolt.diy

This document outlines approaches for integrating a specific, modern technology stack into `bolt.diy`. The goal is to enable `bolt.diy` to generate beautiful, performant, and well-structured web applications using this stack, adhering to best design practices.

## 🎯 Target Technology Stack

The stack to be integrated includes (refactor new instructions to include ALL of these with the opportunity for ):

### Frontend
- **React** `^18.3.1`
- **TypeScript** `^5.5.3`
- **Vite** `^5.4.1`

### Backend & Database
- **Supabase** `^2.49.4` (Auth, Database, Storage)
- **Supabase Edge Functions** (Deno runtime)

### Styling
- **Tailwind CSS** `^3.4.11`
- **shadcn/ui** (Component library built on Tailwind CSS and Radix UI)
- **Tailwind Merge** `^2.5.2`
- **Tailwind Animate** `^1.0.7`

### UI Component Libraries (Core to shadcn/ui and standalone)
- **Radix UI Components** (Various, e.g., `@radix-ui/react-dialog`, `@radix-ui/react-dropdown-menu`)
- **Lucide React** `^0.462.0` (Icons)
- **Embla Carousel** `^8.3.0`
- **Sonner** `^1.5.0` (Toasts)
- **React Day Picker** `^8.10.1`
- **Vaul** `^0.9.3` (Drawers)

### State Management & Data Fetching
- **React Router DOM** `^6.26.2`
- **TanStack React Query** `^5.56.2`
- **React Hook Form** `^7.53.0`
- **Zod** `^3.23.8` (Schema validation)
- **@hookform/resolvers** `^3.9.0`
- **clsx** `^2.1.1`
- **class-variance-authority** `^0.7.1`

### Data Visualization
- **Recharts** `^2.12.7`
- **React Resizable Panels** `^2.1.3`
- **React Masonry CSS** `^1.0.16`

### Theming & Visual Effects
- **Next Themes** `^0.3.0` (Adapted for Vite, or a similar Vite-compatible solution)
- **date-fns** `^3.6.0`

### Development Tools
- **ESLint**, **TypeScript ESLint**, **PostCSS**, **Autoprefixer**, **@tailwindcss/typography**, **@vitejs/plugin-react-swc**
- **lovable-tagger** `^1.1.7`

### API Integration & Authentication
- **Supabase Auth UI React** (`@supabase/auth-ui-react`, `@supabase/auth-ui-shared`)
- **Supabase JS Client** (`@supabase/supabase-js`)
- Secure Edge Function patterns for external API calls (e.g., IntelVault API example)

## 🛠️ Approaches for Integration into Bolt.diy

Here are a few approaches to enable `bolt.diy` to generate applications using this comprehensive stack:

### Approach 1: The "Premium Full-Stack" Starter Template

This involves creating a new, highly opinionated starter template that includes the entire stack pre-configured.

**Implementation Details:**

1.  **Create GitHub Repository:**
    *   Develop a new GitHub repository (e.g., `your-org/bolt-premium-vite-react-supabase-shadcn-template`).
    *   This repository will contain a fully working Vite + React + TypeScript application.
    *   **Key configurations:**
        *   `package.json` with all specified dependencies.
        *   Vite config (`vite.config.ts`) with React SWC plugin, PostCSS, Tailwind CSS setup.
        *   `tailwind.config.js` set up for `shadcn/ui` (content paths, plugins like `tailwindcss-animate`).
        *   Basic `shadcn/ui` component setup (e.g., `components.json`, a few example components like Button, Card).
        *   `tsconfig.json` with paths configured for `shadcn/ui` (`@/*`).
        *   ESLint configuration.
        *   Basic Supabase client setup (`lib/supabaseClient.ts`).
        *   Wrapper for Supabase Auth UI.
        *   Example `TanStack React Query` setup.
        *   Basic `React Hook Form` with Zod example.
        *   `Next Themes` (or equivalent like `vite-plugin-themes`) integration for light/dark mode.
        *   Folder structure for `app/routes`, `app/components`, `app/lib`, `app/hooks`, `app/styles`, `supabase/functions/`.
        *   Example Recharts integration.
        *   A simple, beautifully designed example page showcasing some components and features.
2.  **Add to `bolt.diy` Constants:**
    *   In `app/utils/constants.ts`, add this new template to the `STARTER_TEMPLATES` array:
        ```typescript
        {
          name: 'Premium Vite React Supabase Shadcn',
          label: 'Premium Full-Stack (Vite, React, Supabase, Shadcn)',
          description: 'A comprehensive starter with Vite, React, TypeScript, Supabase, Tailwind CSS, shadcn/ui, TanStack Query, and more for building beautiful, modern web applications.',
          githubRepo: 'your-org/bolt-premium-vite-react-supabase-shadcn-template',
          tags: ['premium', 'fullstack', 'react', 'vite', 'supabase', 'shadcn', 'tailwind', 'typescript', 'design-focused'],
          icon: 'i-bolt:diamond' // Or a more fitting icon
        }
        ```
3.  **LLM Guidance (Optional but Recommended):**
    *   The template can include a `.bolt/prompt` file with instructions for the LLM on how to best *use* the pre-configured stack, such as:
        *   "When adding new UI elements, prioritize using `shadcn/ui` components. If a suitable `shadcn/ui` component doesn\'t exist, build a custom one following its design principles (Radix UI primitives, Tailwind CSS utility classes)."
        *   "For data fetching, always use TanStack React Query. Create query keys and mutation functions in `app/lib/queries.ts` or similar."
        *   "For forms, use React Hook Form with Zod for validation."
        *   "Follow the existing theming patterns for light/dark mode."
        *   "Place Supabase Edge Functions in `supabase/functions/your-function-name/index.ts`."

**Best Practices for the Template:**

*   **Minimal but Complete:** Should be runnable out-of-the-box with a clear example.
*   **Well-Documented:** Comments in code, and a `README.md` explaining the structure.
*   **Organized:** Clear folder structure that promotes scalability.
*   **Visually Appealing Base:** The initial template should already look good to inspire the user.
*   **Performance:** Optimized Vite and Tailwind configurations.

**Pros:**
*   **Fastest Startup:** Users get a fully configured, rich environment instantly.
*   **Consistency:** Ensures all parts of the stack are set up correctly together.
*   **Showcases Best Practices:** The template itself can be an example of how to structure such an app.
*   **Easier for LLM:** The LLM builds upon a solid foundation rather than creating everything from scratch.

**Cons:**
*   **Less Flexible (Initially):** Users get the whole stack, even if they only need parts of it for a smaller project (though the LLM can help remove unused parts).
*   **Maintenance:** The GitHub template itself needs to be kept up-to-date with library changes.

### Approach 2: Enhanced LLM Prompting & Modular Generation

This approach relies less on a monolithic template and more on `bolt.diy`'s LLM being expertly guided to assemble this stack piece by piece, or by combining smaller, more focused "micro-templates" or "feature modules."

**Implementation Details:**

1.  **Deep Dive into System Prompts:**
    *   Modify `app/lib/common/prompts/prompts.ts` (or `new-prompt.ts`) significantly.
    *   Add detailed sections for each part of the stack, similar to the existing `<database_instructions>` for Supabase.
    *   **Example - `<styling_instructions>`:**
        ```
        <styling_instructions>
          CRITICAL: For styling, use Tailwind CSS with shadcn/ui by default for React projects unless specified otherwise.
          1. Setup:
             - Ensure Tailwind CSS is installed and configured (`tailwind.config.js`, `postcss.config.js`, global CSS with @tailwind directives).
             - Instruct on setting up shadcn/ui: initialize CLI, configure `components.json`.
             - Add `tailwind-merge` and `clsx` for utility.
          2. Component Usage:
             - Prioritize shadcn/ui components.
             - If custom components are needed, build them using Radix UI primitives and Tailwind CSS, following shadcn/ui patterns.
             - Use `class-variance-authority` for managing component variants.
          3. Animation:
             - Use `tailwindcss-animate` for common animations.
             - For complex animations, consider libraries like Framer Motion if requested.
        </styling_instructions>
        ```
    *   **Example - `<data_fetching_instructions>`:**
        ```
        <data_fetching_instructions>
          CRITICAL: For data fetching and server state management in React projects, use TanStack React Query by default.
          1. Setup:
             - Install `@tanstack/react-query`.
             - Wrap the application with `<QueryClientProvider>`.
          2. Usage:
             - Define query keys in a structured way (e.g., `app/lib/queryKeys.ts`).
             - Use `useQuery` for data fetching, `useMutation` for data modification.
             - Implement proper error handling and loading states.
        </data_fetching_instructions>
        ```
    *   Similar detailed instructions would be needed for React Hook Form + Zod, Recharts, Next Themes, etc.
2.  **Action Handling:**
    *   The `ActionRunner` might need to be enhanced if specific CLI commands (like `shadcn-ui add`) are to be automated beyond simple file creation/terminal execution.
3.  **Modular Prompts/Functions:**
    *   `bolt.diy` could have internal functions that generate specific configurations (e.g., a function to generate a `tailwind.config.js` for `shadcn/ui` when requested). The LLM would call these "meta-actions."

**Best Practices for LLM Guidance:**

*   **Specificity:** Prompts must be extremely clear and unambiguous.
*   **Step-by-Step:** Guide the LLM through setup and usage for each library.
*   **Error Handling:** Tell the LLM how to instruct the user or what to do if a particular setup step fails.
*   **Prioritization:** Clearly state defaults (e.g., "Use shadcn/ui first").

**Pros:**
*   **Maximum Flexibility:** Users can pick and choose parts of the stack, or the LLM can assemble it based on high-level requests.
*   **Always Up-to-Date (Potentially):** If the LLM's knowledge is current (or it can web-search for latest versions/setup), it can generate more up-to-date configurations than a static template.
*   **Good for Learning:** The LLM can explain *why* it\'s doing certain things.

**Cons:**
*   **Much More Complex Prompt Engineering:** Requires significant effort to create and maintain these detailed prompts.
*   **Slower Initial Generation:** The LLM has to "think" and generate more from scratch.
*   **Higher Risk of Errors/Inconsistencies:** If prompts are not perfect, the LLM might make mistakes in setup or integration.
*   **Token Limits:** Very long and detailed prompts can hit LLM context window limits.

### Approach 3: Hybrid - Core Template + LLM Customization

This combines the benefits of a solid base with the flexibility of LLM-driven customization.

**Implementation Details:**

1.  **Create a "Core Modern React" Starter Template:**
    *   This template would include: React, Vite, TypeScript, Tailwind CSS (basic setup), ESLint, basic folder structure, Supabase client (unconfigured), React Router DOM, `clsx`, `tailwind-merge`.
    *   It would *not* include `shadcn/ui` fully, Recharts, React Hook Form, etc., by default but would be structured to easily accommodate them.
    *   This template would be added to `STARTER_TEMPLATES`.
2.  **Augment with `.bolt/prompt` and System Prompts:**
    *   The "Core Modern React" template's `.bolt/prompt` file would contain instructions like:
        *   "This is a base Vite + React + Tailwind project. Based on the user\'s request, you can now add:
            *   **UI Components:** `shadcn/ui` (guide through init and adding components).
            *   **State Management:** TanStack React Query.
            *   **Forms:** React Hook Form with Zod.
            *   **Charting:** Recharts.
            *   ..."
    *   The main system prompt (`app/lib/common/prompts/prompts.ts`) would still contain the high-level defaults and best practices for these libraries, which the LLM refers to when the template's `.bolt/prompt` triggers their addition.

**Pros:**
*   **Good Balance:** Faster than full LLM generation, more flexible than a monolithic template.
*   **Reduced Prompt Complexity:** The main system prompt doesn't need to cover *initial basic setup* of Vite/React/Tailwind as the template handles that.
*   **Guided Customization:** The LLM is guided to add features modularly.

**Cons:**
*   **Still Requires Careful Prompting:** The interaction between the template's `.bolt/prompt` and the main system prompt needs to be well-orchestrated.
*   **Potential for "Half-Configured" States:** If the LLM only partially adds a feature.

## ✨ General Best Design Practices for LLM Enforcement

Regardless of the integration approach, `bolt.diy`'s LLM should be guided by these design principles when generating code with this stack:

1.  **Visual Hierarchy & Layout:**
    *   "Ensure clear visual hierarchy using typography (size, weight, color), spacing, and component placement."
    *   "Utilize Tailwind CSS utility classes for consistent spacing (margins, paddings) based on a defined scale (e.g., `m-2`, `p-4`)."
    *   "Employ responsive design principles (`sm:`, `md:`, `lg:`) extensively. Test layouts on different screen sizes."
    *   "Use `shadcn/ui` layout components like `Card`, `AspectRatio`, or build custom responsive grids/flexbox layouts."
    *   "Consider using `React Resizable Panels` for complex dashboard-like interfaces where user-adjustable layouts are beneficial."

2.  **Component Design & Reusability:**
    *   "Break down UI into small, reusable React components."
    *   "When using `shadcn/ui`, leverage its composition patterns. For custom components, follow similar patterns using Radix UI primitives and Tailwind CSS."
    *   "Use `class-variance-authority` to manage component variants (e.g., button styles, sizes) in a type-safe way."
    *   "Ensure components have clear props and are well-typed with TypeScript."

3.  **Typography & Readability:**
    *   "Use `shadcn/ui` typography defaults or `@tailwindcss/typography` for prose. Ensure high contrast for text."
    *   "Select appropriate sans-serif fonts (e.g., Inter, system fonts) provided by `shadcn/ui` themes."
    *   "Maintain consistent line height and letter spacing for readability."

4.  **Color & Theming:**
    *   "Strictly adhere to the color palette defined by the `shadcn/ui` theme (or the theme configured via `Next Themes`/equivalent)."
    *   "Ensure theme changes (light/dark mode) apply consistently across all components."
    *   "Use semantic color tokens (e.g., `bg-primary`, `text-destructive`) provided by `shadcn/ui` rather than hardcoding Tailwind color values directly where theme-awareness is needed."

5.  **Interactivity & Feedback:**
    *   "Provide clear visual feedback for interactive elements: hover states, focus states (using Tailwind\'s `hover:`, `focus:` variants), active states."
    *   "Use `Sonner` for non-intrusive toast notifications for actions (success, error, info)."
    *   "Use `shadcn/ui` `AlertDialog` for critical confirmations."
    *   "Incorporate subtle animations using `tailwindcss-animate` or `Framer Motion` (if added) to enhance UX without being distracting. Focus on entrance, exit, and state change animations."
    *   "For loading states, use `shadcn/ui` `Skeleton` components or spinners (`lucide-react` icons)."

6.  **Forms & Inputs:**
    *   "Use `shadcn/ui` form components (`Input`, `Select`, `Checkbox`, `RadioGroup`, `Textarea`, `DatePicker` via `React Day Picker`) for a consistent look and feel."
    *   "Ensure all form inputs have associated `Label`s for accessibility."
    *   "Provide clear validation messages (using React Hook Form + Zod integration) displayed near the respective fields."

7.  **Iconography & Imagery:**
    *   "Use `Lucide React` for icons. Ensure icons are used consistently and meaningfully."
    *   "Optimize images for the web. Consider using modern image formats."

8.  **Data Visualization:**
    *   "When using `Recharts`, select chart types appropriate for the data being displayed."
    *   "Ensure charts are responsive and readable on all screen sizes."
    *   "Use tooltips, legends, and clear axes labeling. Adhere to theme colors for chart elements."

9.  **Accessibility (a11y):**
    *   "Many `shadcn/ui` components (built on Radix UI) are accessible by default. Do not break this."
    *   "Ensure sufficient color contrast."
    *   "Use semantic HTML elements."
    *   "Provide ARIA attributes where necessary if building custom complex components."
    *   "Ensure keyboard navigability for all interactive elements."

10. **Performance:**
    *   "Leverage Vite\'s fast builds and HMR."
    *   "Ensure Tailwind CSS is purging unused styles in production builds."
    *   "Code-split where appropriate (React.lazy, router-based splitting)."
    *   "Optimize data fetching with TanStack React Query to avoid unnecessary re-renders."

## 💡 Concluding Thought

The **Hybrid Approach (Approach 3)** likely offers the best balance for `bolt.diy`. It provides a solid, modern foundation (Vite, React, TS, basic Tailwind, Supabase client) quickly via a core template. Then, the LLM, guided by robust system prompts and specific instructions in the template's `.bolt/prompt` file, can intelligently layer in more advanced features like `shadcn/ui`, TanStack Query, Recharts, etc., based on the user's specific needs. This avoids overwhelming the user with a massive template if they don\'t need everything, while still enabling the creation of highly sophisticated and beautiful applications.

This approach also allows `bolt.diy` to evolve its capabilities modularly. As new libraries or design patterns emerge, the LLM prompts can be updated to incorporate them without necessarily requiring a brand new monolithic template for every permutation. 