# Blaze Diagnostics App

Blaze Diagnostics is a workshop management and customer communication system for mechanical workshops.

The system is intended to improve communication between workshops and clients by tracking vehicle jobs, quote approvals, parts ordering, parts delivery, mechanic progress, invoicing, and collection readiness.

## Current Technical Stack

- Frontend: Next.js, React, TypeScript, Tailwind CSS
- Backend: Node.js, TypeScript, modular service/controller/repository structure
- Database: PostgreSQL
- Current ORM in the uploaded app: Prisma
- Earlier planning referenced: Drizzle ORM
- Version control: GitHub

Important: The current uploaded application uses Prisma. Do not assign Drizzle implementation work until the ORM direction has been confirmed. See `docs/technical/ORM_DECISION_NOTE.md`.

## Student Groups Using This Repository

This repository has been adjusted for the current CTU Bloemfontein internship groups:

- Software Engineering 1
- Software Engineering 2
- Software Development 1
- Software Development 2
- Cloud Administration
- Cyber Security

Architectural Draughting students should use the separate `synergy-inc-architectural-draughting` repository.

## Where Students Should Start

1. `STUDENT_START_HERE.md`
2. `docs/internship/REMOTE_INTERNSHIP_PLAN.md`
3. `docs/internship/FIRST_WEEK_TRAINING_SCHEDULE.md`
4. `docs/training/GITHUB_WORKFLOW.md`
5. `docs/training/GROUP_LEARNING_PATHS.md`
6. The learning path for your specific group in `docs/training/`
7. `docs/backlog/STUDENT_STARTER_TASKS_BY_GROUP.md`
8. `docs/backlog/BLAZE_DIAGNOSTICS_ISSUES_BY_GROUP.md`

## Repository Bootstrap

### Workspace Layout

- `backend/` - API and domain modules
- `frontend/` - Next.js application
- `docs/` - product, API, schema, training, backlog, and internship documents
- `.github/` - GitHub issue and pull request templates

### Quick Start

1. Copy `.env.example` to `.env`.
2. Copy `backend/.env.example` to `backend/.env` if required.
3. Copy `frontend/.env.example` to `frontend/.env.local`.
4. Start local services with `docker-compose up -d`.
5. Install dependencies in the required workspace.
6. Run the relevant database generate/migrate/seed commands once confirmed by the mentor.
7. Start the backend.
8. Start the frontend.

## Student GitHub Workflow

Students must not commit directly to `main`.

Expected workflow:

1. Read the issue.
2. Create a branch.
3. Make a small focused change.
4. Test or review your work.
5. Commit with a clear message.
6. Push the branch.
7. Open a pull request.
8. Respond to review feedback.

## Security Rule

Never commit passwords, API keys, database URLs, tokens, `.env` files, personal credentials, or private customer information.

## Student MVP Planning Update

This repository now includes student-ready MVP planning material grouped for:

- Software Engineering 1
- Software Engineering 2
- Software Development 1
- Software Development 2
- Cloud Administration
- Cyber Security

Start with:

- `STUDENT_START_HERE.md`
- `docs/training/FIRST_WEEK_BLAZE_DIAGNOSTICS_TECH_STACK.md`
- `docs/backlog/MVP_BACKLOG_BY_GROUP.md`
- `docs/backlog/MVP_USER_STORIES.md`

Cloud Administration students should also read:

- `docs/cloud/CLOUD_ADMINISTRATION_MVP_TASKS.md`

Cyber Security students should also read:

- `docs/security/CYBER_SECURITY_MVP_TASKS.md`

## Student Learning Resources

Blaze Diagnostics learning resources are available at:

`docs/training/student-learning-resources.md`

The full internship-wide resource list is maintained in the `internship-training-and-docs` repository under:

`13-resource-index/self-paced-resource-urls.md`

## Findings
### Back End 

Seed.ts  
The seed file initializes demo data in a clean, idempotent way using upsert operations. It sets up a tenant, branch, admin user, customer, vehicle, job, and quote so the system has baseline records to work with. This approach ensures consistency across runs and avoids duplication. A natural extension would be to seed parts and part requests, adding fields like name, quantity, price, and reorder level, tied to jobs. Doing so would give the PartsModule realistic data for testing queries, updates, and validations once it is implemented.

App.ts  
The application file defines how the backend instance is created and configured. The createApp function wires controllers into a lightweight application object, sets up middleware, registers routes, and provides a listen method to simulate starting the server. This scaffold is useful for testing without a full framework like Express. As the system grows, the hardcoded routes should be replaced with dynamically generated ones from each controller, ensuring new modules like PartsController or NotificationsController integrate seamlessly without manual updates.

Auth  
The authentication module acts as the security gatekeeper, ensuring only valid users can log in and interact with the system. The AuthController manages authentication actions, connecting validators, middleware, and the AuthService. DTOs define input and output contracts, making authentication predictable and secure. The repository handles persistence, bridging services and controllers with the in‑memory database. Routes centralize authentication endpoints, while the AuthService orchestrates workflows like login, logout, token refresh, and password resets. It enforces tenant checks, token lifetimes, and revocation policies, making it the backbone of identity management. Tests simulate login flows and validate token structures, while validators enforce strict schema rules for login, password resets, and token refresh, ensuring only well‑formed data reaches the service layer.

Collection  
The collection module is currently a scaffold, intended to manage sets of data. The CollectionController will eventually expose CRUD operations, while the DTO defines contracts for collection data. The repository will handle persistence logic, isolating database operations from business rules. Routes will register collection endpoints, and the service will enforce workflows between controller and repository. Tests are scaffolded with a trivial passing case, validators are stubs for schema enforcement, and the index file acts as a barrel export to simplify imports and modularize the module.

Customers  
The customers module manages customer data across the API and database layer. The CustomersController exposes endpoints for customer management, wrapping the service and validating inputs. DTOs enforce required fields like tenantId, fullName, and mobileNumber, while supporting optional metadata such as email, address, and marketing consent. Entities extend BaseEntity for consistency, adding customer‑specific fields. The repository provides methods for listing, searching, finding, creating, updating, and archiving customers, with duplicate checks and tenant isolation. Routes define endpoints for CRUD operations and soft deletion. The service implements business logic for customer workflows, enforcing uniqueness, tenant boundaries, and audit timestamps. Tests are scaffolded, validators ensure strict input validation for create and update operations, and the index file provides clean imports of controller and service.

Dashboard  
The dashboard module is responsible for backend logic that powers dashboard views and APIs. The DashboardController will expose endpoints for aggregated data such as customer counts, revenue, and system health. DTOs define structures for KPIs and summaries, ensuring predictable data contracts. The repository will query multiple sources to generate metrics rather than storing its own records. Routes will register dashboard endpoints, while the service will aggregate raw data into actionable insights, transforming it into KPIs and summaries. Tests are scaffolded to validate aggregation and tenant isolation, validators will enforce schema rules for dashboard inputs, and the index file provides a clean entry point for controller and service exports.

Inspections  
The inspections module manages inspection records, rules, and workflows. The InspectionsController will define CRUD endpoints for inspections, validating payloads and delegating logic to the service. DTOs will define inspection data contracts, including identifiers, dates, statuses, and inspector details. The repository will handle persistence, managing CRUD operations for inspection records. Routes will register inspection endpoints, while the service will enforce workflows like status updates, scheduling, and linking inspections to customers or assets. Tests are scaffolded to validate inspection workflows, validators will enforce schema rules for inspection payloads, and the index file provides modular exports.

Invoices  
The invoices module handles invoice management, including data, rules, and API endpoints. The InvoicesController will expose CRUD operations for invoices, validating requests and delegating logic to the service. DTOs will define invoice data contracts, including identifiers, customer details, financial fields, and status indicators. The repository will manage persistence, enforcing tenant isolation and preventing duplicate invoice numbers. Routes will register invoice endpoints, while the service will enforce workflows such as invoice creation, lifecycle management, reporting, and notifications. Tests are scaffolded to validate invoice workflows, ensuring the module evolves into a reliable backbone for financial operations.

Payments:  This module provides the foundation for handling payment processing and record management. It currently defines the basic building blocks but leaves detailed implementation for later. The PaymentsController will eventually manage incoming HTTP requests related to payments, while the PaymentsDto interface outlines the structure of payment data such as amount, currency, and user ID. The PaymentsRepository will handle persistence by interacting with the database to store and retrieve records. At present, paymentsRoutes is empty but will later map URLs to controller methods. The PaymentsService will contain business logic like validating transactions or applying discounts before saving them. A placeholder test suite is included, along with a stubbed validatePaymentsInput function for schema validation. Finally, re‑exports make the controller and service accessible throughout the project.

Quotes:  
This module manages business quotes and estimates, with a complete structure in place. The QuotesController provides methods to list quotes by job, create new ones, mark them as sent, and handle public approval or rejection via tokens. Business logic resides in the QuotesService, which calculates s
ubtotals, discounts, taxes, and totals, generates unique IDs and tokens, and updates statuses. Persistence is handled by the QuotesRepository, which stores and retrieves quotes and line items in memory. Data structures are defined through QuoteLineDto, CreateQuoteDto, and QuoteEntity. Validators such as validateCreateQuoteInput enforce proper formatting of incoming data. Routes connect API endpoints to controller methods, while a scaffolded test suite ensures the module can be tested. Re‑exports make the controller and service available elsewhere in the system.

Suppliers:  
The suppliers module is currently a scaffold, intended to manage supplier information such as names, contact details, and product categories. The SuppliersController will eventually handle HTTP requests, while the SuppliersDto defines the structure of supplier data. The SuppliersRepository will manage persistence, storing and updating supplier records. Routes are empty for now but will later map endpoints to controller methods. The SuppliersService will implement business logic, including validation and relationship management. A placeholder test suite and validation stub are included, with re‑exports ensuring easy integration.

Tenants:  
This module manages tenant data, including structure, persistence, and endpoints. The TenantsController exposes methods to list tenants, fetch by ID, create new tenants, and update existing ones. The TenantsService enforces business rules such as unique slugs, default settings (timezone, currency, tax rate), and merging updates with existing configurations. The TenantsRepository handles persistence in memory, supporting listing, finding, creating, and updating records. DTOs and entities define tenant data, while validators enforce correctness, ensuring required fields, valid emails, and restricted tenant types. Routes map endpoints to permissions, enforcing access control. A placeholder test suite is included, and re‑exports make the controller and service accessible.

Users:  
The users module is the backbone of authentication, authorization, and user data management. The UsersController provides endpoints to list, fetch, create, and update users. The UsersService enforces business rules such as tenant validation, duplicate email prevention, secure password hashing, and managing statuses (INVITED, ACTIVE, SUSPENDED). The UsersRepository persists user records in memory. DTOs and entities define user data, including names, roles, preferences, and audit timestamps. Validators enforce strict input correctness, ensuring roles and statuses are valid and passwords meet minimum requirements. The toPublicUser utility strips sensitive fields before returning data. Routes enforce permissions, while a scaffolded test suite ensures testability. Re‑exports make the module accessible across the project.

Vehicles:  
This module manages vehicle records and their links to customers, jobs, or inspections. The VehiclesController exposes endpoints to list, fetch, create, update, and archive vehicles. The VehiclesService enforces rules such as preventing duplicates (registration number or VIN), validating tenant ownership, and managing archival. The VehiclesRepository handles persistence, providing search, create, update, and archive functions. DTOs and entities define vehicle data, including registration, VIN, make, model, year, engine type, fuel type, transmission, odometer, and color. Validators enforce schema correctness. Routes expose RESTful APIs, while a scaffolded test suite ensures testability. Re‑exports make the controller and service available elsewhere.

Shared:  
This module provides common utilities for authentication, authorization, and data management. It includes password hashing with scrypt, constant‑time verification, role‑based permissions, and token handling with HMAC SHA‑256. The AuthContext defines authenticated user identity, tenant, role, and permissions. Functions like requireAuth, requirePermission, and requireAnyPermission enforce access control, while tenant scope utilities prevent cross‑tenant access. Domain constants such as JOB_STATUSES and DomainEvent define workflow states. An in‑memory database is seeded with demo data, including tenants, users, customers, vehicles, jobs, and quotes. Utility functions generate IDs, timestamps, and tokens, while validation helpers enforce data correctness. Response helpers standardize API outputs, and the app bootstrap starts the server with controllers logged.

Type:  
This module centralizes reusable type definitions for consistency across the backend. It declares Node.js global objects and crypto APIs, including process.env, Buffer, and functions like createHmac, randomBytes, scryptSync, and timingSafeEqual. These declarations provide type safety without implementing functionality, ensuring TypeScript can validate usage of Node.js features.

Server:  
The server file is the main entry point of the backend. It imports createApp, configures the application, and starts listening on a port defined by the environment variable PORT or defaults to 4000. Once running, it logs confirmation messages showing the port and available controllers, ensuring developers know the server is active and which modules are wired in.

.env:  
This configuration file defines runtime settings for the backend. It specifies the server port, frontend base URL, database and Redis connections, and JWT secrets for access, refresh, and reset tokens. It also defines token lifetimes: access tokens last 15 minutes, refresh tokens last 7 days, and reset tokens expire after 30 minutes. These variables ensure secure communication, persistence, caching, and authentication.

Package-lock.json:  
This auto‑generated file ensures consistent dependency management. It captures project metadata such as name, version, and lockfile format, and records installed packages. In this case, only the root package is defined, meaning no external dependencies have been installed yet. It guarantees reproducibility across environments.

Package.json:  
This minimal setup reflects the project’s identity and dependency state. It defines the project name and version but currently lists no external packages. It ensures reproducibility once dependencies are added.

Tsconfig.json:  
This configuration file defines TypeScript compiler rules. It targets ES2021, uses CommonJS modules, compiles from src to dist, enforces strict type checking, supports ES module interop, and skips library checks for faster compilation. It includes .ts and .d.ts files while excluding test files. This ensures consistent compilation into modern JavaScript with strong type guarantees.


### Front End 

CSS/Tailwind CSS

global.css is found the file sets up a visual foundation of the app it allows Tailwind Css base resets , component styles and utility classes and also defines the colour scheme with a white background and black text for the body links are set to get the same colour as the parent element

Typescript

Components
Forms:
FormActions: Recognises input that has the name {PropsWithChildren} then exports the users input to the attribute "children"

FormField: Start: The interfaces name FormFieldProps
Then give it a label that is specified as a string
name is also specified as a string
children is specified as React.ReactNode

Then: The function formField with all the previous Attributes in FormFieldProps is exported then returns a label for html and with the specified name and labeled as children.

Layout.tsx is found the root layout is a entry point for the Next.js app , It puts the pages in a standard HTML shell and defines the apps metadata and sets the browser title to Blaze Diagnostics and describes it as workshop diagnostics and client communication platform

page.tsx is found the homepage brings all core features of the platform in a single appshell layout component . it renders the dashboard , Customers , Vehicles,Jobs and Quotes panels in a vertical grid ,giving users a full overview of the workshop platform from the central screen

Timeline.tsx defines the items to require an ID and Title and optionally an Subtitle with a ? to show its optional.The functions accepts a single prop items an array of TimelineItem Objects ITEMS=[]means it defaults to an empty array if there is mothing passed in

StatusBadge.tsx - Summary

The StatusBadge component is a simple, stateless React component that takes a single value string prop and renders it as a small, pill-shaped badge. It uses an inline-styled <span> with fully rounded corners achieved via a large borderRadius, light grey background, a subtle border, and semi-bold text at a small font size — giving it the appearance of a compact label or tag commonly used to display statuses, categories, or flags in a UI.


customer.api.ts is an API service module for managing customers the imports eg.apiclients(shared fetch wrapper used to make all requests and handles stuff like auth headers ,error handling )endpoints(central object holding url strings)type imports(define the shape of data going in and coming out).List builds the URL and safely handles special charcaters in the search string,create and update send JSON bodies,update uses patch meaning it only updates the fields you send,Archive uses Delete but returns a CustomerRecord.

customers.schema.ts is a validation schema that describes the rules each form field must follow (fullname must be a string)(mobileNumber must be a string)(email must be a string but is optional)

customers.types.ts is a types file and gives fields a type to be identified as

customers-page.tsx is a page component that composes two things together Appshell and customerspanel ,appshell is a shared layout wrapper that accepts title props,customerpanel-the full customer managment UI you saw in the previous file dropped in as a child

CustomerPanel.tsx is a full CRUS customer managment panel that lets users create, search ,edit and archive cutomer records the 4 main functions are Loadcustomers(query?)fetches customers from API optionally filtered by a search string sets loading while waiting and catches any errors into error,Handlesubmit called when the from is submitted if editingID is set it calls update otherwise it calls create,HandleArchive(ID)calls the archive API for a customer then refreshes the list.if the archived customer was being edited,it also resets the form,StartedEdit populates the form with an existing customers data and sets editingID,switching the form from create mode to edit mode

Dashboard api-Summary

dashboardApi is an exported constant that is currently just an empty object serving as a placeholder, with a TODO comment left by the developer as a reminder that the actual API functions still need to be implemented.The structure exists so other parts of the codebase can already reference and import it, but no real logic or functionality has been added yet

dashboard.types.ts - Summary
DashboardRecord is a TypeScript interface that defines the structure of a dashboard data record. At this stage it only contains a single required field — id — which serves as a unique identifier for each record in the dashboard.
Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component or function working with dashboard records follows the same data shape. This promotes consistency and type safety throughout the application.
Overall, this is a minimal, foundational type definition that is likely a starting point, with more fields such as title, status, or value expected to be added as the dashboard feature grows and its data requirements become clearer.

dashboard-page.tsx - Summary

DashboardPage is a minimal, stateless React page component that serves as the entry point for the dashboard section of the application. It imports two components — AppShell for the overall layout and DashboardPanel for the main content — and composes them together into a single page.
The component wraps DashboardPanel inside AppShell, passing "Workshop Dashboard" as the title, which AppShell likely uses to render a header or page title. This pattern of separating layout from content is a common and clean architectural approach in React applications.
Overall, this file is intentionally simple — its only job is to combine the layout shell and the dashboard content into one cohesive page. The actual logic and styling live in the imported components, keeping this file focused, readable, and easy to maintain.

DashboardPanel.tsx - Summary

The DashboardPanel component is a stateless React component that renders a row of three summary cards in a responsive CSS Grid layout. It imports the StatusBadge component to display status labels, and relies on a hardcoded array of card data containing a title, a numeric value, and a status string for each card.
Each card is displayed inside a rounded, bordered container and is structured using flexbox to place the bold title on the left and the status badge on the right, with the numeric value rendered in large text below them. This layout creates a clean, scannable summary view that is common in dashboard interfaces.
Overall, this component is a straightforward presentational panel with no state or interactivity, simply mapping over the hardcoded data to produce a consistent card for each entry. In a real application, the hardcoded cards array would likely be replaced with dynamic data fetched from an API, making it more flexible and data-driven.

DashboardPage is a minimal, stateless React page component that serves as the entry point for the dashboard section of the application. It imports two components — AppShell for the overall layout and DashboardPanel for the main content — and composes them together into a single page.

The component wraps DashboardPanel inside AppShell, passing "Workshop Dashboard" as the title, which AppShell likely uses to render a header or page title. This pattern of separating layout from content is a common and clean architectural approach in React applications.

Overall, this file is intentionally simple — its only job is to combine the layout shell and the dashboard content into one cohesive page. The actual logic and styling live in the imported components, keeping this file focused, readable, and easy to maintain.
dashboard.types.ts - Summery

DashboardRecord is a TypeScript interface that defines the structure of a dashboard data record. At this stage it only contains a single required field — id — which serves as a unique identifier for each record in the dashboard.

Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component or function working with dashboard records follows the same data shape. This promotes consistency and type safety throughout the application.

Overall, this is a minimal, foundational type definition that is likely a starting point, with more fields such as title, status, or value expected to be added as the dashboard feature grows and its data requirements become clearer.

Inspections(api):
This is a placeholder file. It defines an empty inspectionsApi object with a TODO comment indicating that the actual API functions haven't been written yet.

Inspection(Components):
this is a placeholder component that renders a simple div with placeholder text. It follows the same pattern as AuthPanel and is ready to be built out when the inspections feature gets developed, slotting straight into a page component via AppShell the same way AuthPanel does.

Inspection(pages):
Same pattern again — a placeholder page component for the inspections route. Unlike the auth version though, it skips `AppShell` and `InspectionsPanel` entirely for now, rendering just a plain div. When the feature gets built out, it will follow the same composition pattern as `AuthPage` — wrapping `InspectionsPanel` inside `AppShell` with an appropriate title.

Inspections(Types):
A minimal placeholder type, just like the other inspections files. It defines an `InspectionsRecord` interface with only an `id` field for now — enough to establish the type exists and give the feature a starting shape, but the real fields like vehicle details, inspection results, dates, and technician info will be added when the feature gets built out.

invoices.api.ts - Summary

invoicesApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to invoices. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the invoices feature is built out.
The object is exported so that other parts of the codebase, such as components or services that deal with invoices, can already import and reference it without needing to restructure their imports later. This is a common pattern in larger codebases where the architecture is planned ahead of the actual implementation.
Overall, this is a foundational placeholder that establishes the invoicesApi as the designated home for invoice-related API logic. In a real application, it would be expected to contain functions for operations such as fetching a list of invoices, retrieving a single invoice by ID, creating new invoices, and updating or deleting existing ones.

invoices.api.ts - Summary

invoicesApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to invoices. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the invoices feature is built out.

The object is exported so that other parts of the codebase, such as components or services that deal with invoices, can already import and reference it without needing to restructure their imports later. This is a common pattern in larger codebases where the architecture is planned ahead of the actual implementation.

Overall, this is a foundational placeholder that establishes the invoicesApi as the designated home for invoice-related API logic. In a real application, it would be expected to contain functions for operations such as fetching a list of invoices, retrieving a single invoice by ID, creating new invoices, and updating or deleting existing ones.
Invoicespanel.tsx - Summary

InvoicesPanel is a minimal, stateless React component that currently renders nothing more than a <div> containing the text Invoices panel placeholder. It is exported so it can be imported and used in other parts of the application, such as a page-level component, while the actual implementation is still pending.

The placeholder pattern is common in frontend development, allowing developers to wire up the routing, layout, and structure of an application before the detailed UI and logic of each section is fully built. This means other parts of the codebase can already reference and render InvoicesPanel without causing errors.

Overall, this component is simply a stub that reserves the space and name for the invoices panel feature. In the future it would be expected to display a list or table of invoices, likely fetching data from the invoicesApi and presenting it with filtering, sorting, and status indicators similar to the DashboardPanel.
invoices-page.tsx - Summary

InvoicesPage is a minimal, stateless React page component that currently renders nothing more than a <div> containing the text "Invoices feature placeholder." It is the default export of its file, meaning it is intended to be used as a route-level component that represents the entire invoices section of the application.

Like the InvoicesPanel component, this follows the common placeholder pattern in frontend development, allowing the routing and navigation structure of the application to be fully set up before the detailed UI and logic of the invoices feature is implemented. Other parts of the codebase such as a router configuration can already point to this page without causing errors.

Overall, this is a stub page that reserves the place for the full invoices feature. In the future it would be expected to wrap the InvoicesPanel inside an AppShell layout, similar to how DashboardPage was structured, and eventually display a complete invoices management interface with real data, filtering, and actions.
invoice.types.ts - Summary

InvoicesRecord is a TypeScript interface that defines the structure of a single invoice record within the application. At this stage it only contains one required field — id — which serves as a unique identifier for each invoice record, and is a common starting point when defining data models in TypeScript.

Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component, function, or API call that works with invoice records follows the same data shape. This promotes consistency and type safety throughout the invoices feature of the application.

Overall, this is a minimal and foundational type definition that mirrors the DashboardRecord interface seen earlier, suggesting a consistent pattern is being followed across features in the codebase. In the future, more fields such as amount, status, date, or clientName would be expected to be added as the invoices feature is developed further.

Jobs(api):
This is the jobs feature's API layer, following the same pattern as `authApi` but simpler — three focused methods covering the core operations a job record needs. It imports `apiClient` and `endpoints` for the same reasons as before, keeping URLs centralized and request logic consistent.

The three methods map cleanly to distinct use cases. `list` fetches all jobs for a specific tenant by appending `tenantId` as a query parameter, scoping results to the right workspace. `create` accepts a job payload but deliberately excludes `id`, `referenceNumber`, `status`, and `statusHistory` using TypeScript's `Omit` utility — those fields are server-generated and shouldn't be sent by the client. `updateStatus` is a `PATCH` rather than a full `PUT`, meaning it only updates the status field rather than replacing the entire record, and it also records who made the change via `changedByUserId`.

The notable design decision here is that status changes are treated as their own dedicated endpoint rather than a generic update. This suggests the backend tracks a status history log, which aligns with the `statusHistory` field excluded from `create` — status transitions are meaningful events on a job, not just field edits.

Jobs(Components):
`JobsPanel` is a display component that renders a single hardcoded demo job to showcase how the jobs feature will look and behave. Rather than fetching real data, it uses a `demoJob` constant defined at the top of the file — a realistic-looking job record with a reference number, status, complaint, diagnosis, and a status history entry — giving the UI something meaningful to render while the feature is still being built out.

The component renders two things. First, a card showing the job's reference number alongside a `StatusBadge` component, the customer complaint, and the diagnosis summary. Second, a `Timeline` component fed from the `statusHistory` array, where each history entry is mapped into a title showing the status transition (from → to) and a subtitle showing who made the change. The `??` fallback on `statusHistory` handles the case where the array is undefined, and uses `'START'` when `fromStatus` is absent, meaning the first transition in a job's life had no prior state.

The broader purpose is the same as the other placeholder panels — it establishes the visual structure and component composition for the jobs feature before real API integration is wired up. When live data gets connected, the `demoJob` constant gets replaced with state from a hook, and the rest of the component stays largely the same.

Jobs(Pages):
a thin page component that composes AppShell and JobsPanel together with no logic of its own. It sets the shell title to "Jobs" and lets JobsPanel handle everything else. Its only job is to be the routable entry point for the jobs URL in Next.js.

Jobs(schemas)
A minimal validation schema for the job creation form, defining the three fields a user must provide to open a new job — a customer, a vehicle, and a description of the complaint. All three are marked `required`, which aligns with the `create` method in `jobsApi` where those fields would be part of the payload. The schema format suggests a lightweight validation library is being used rather than something like Zod or Yup, though the actual validator that consumes this object isn't visible in this file.

Jobs(types):
Two related interfaces that together describe the shape of a job and its history. `JobRecord` is the main entity — it holds the identifying fields (`id`, `tenantId`, `referenceNumber`), the relational links to a customer and vehicle, the current `status`, the customer's reported complaint, and an optional diagnosis summary added by the technician. The `statusHistory` array is also optional, meaning the API can return a job without it when a full history isn't needed.

`JobStatusHistoryRecord` describes each entry in that history array. `fromStatus` is optional because the very first transition has no prior state, which is exactly why `JobsPanel` falls back to `'START'` when mapping history items. `toStatus` and `changedByUserId` are always required, capturing what the status changed to and who changed it, while `createdAt` provides the timestamp for the timeline.

Together these two interfaces form a clean audit trail — at any point you can look at a `JobRecord` and reconstruct exactly how it moved through its workflow, who drove each transition, and when each change happened.

The `AppShell` component is a reusable React layout wrapper that provides a consistent outer frame for every page in your app. It accepts two props: an optional `title` string (which defaults to `'Vehicle Service Platform'` if none is provided) and `children`, which represents whatever content you nest inside it.

Structurally, it renders three things — a centered container div capped at 1200px wide, a header showing the title and a subtitle, and a section tag where the child content appears. This means any page that uses `AppShell` automatically gets the same header and layout without you having to rewrite it.

marketplace.api.ts - Summary

marketplaceApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to the marketplace feature. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the marketplace feature is built out.

The object is exported so that other parts of the codebase, such as components or services that deal with marketplace data, can already import and reference it without needing to restructure their imports later. This follows the same consistent pattern seen across the codebase with dashboardApi and invoicesApi, suggesting a deliberate and uniform architectural approach to organizing API logic by feature.

Overall, this is a foundational placeholder that establishes marketplaceApi as the designated home for all marketplace-related API logic. In a real application, it would be expected to contain functions for operations such as fetching marketplace listings, searching or filtering items, retrieving individual product details, and handling purchases or inventory updates.
Marketplacepanel.tsx - summary

marketplaceApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to the marketplace feature. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the marketplace feature is built out.

The object is exported so that other parts of the codebase, such as components or services that deal with marketplace data, can already import and reference it without needing to restructure their imports later. This follows the same consistent pattern seen across the codebase with dashboardApi and invoicesApi, suggesting a deliberate and uniform architectural approach to organizing API logic by feature.

Overall, this is a foundational placeholder that establishes marketplaceApi as the designated home for all marketplace-related API logic. In a real application, it would be expected to contain functions for operations such as fetching marketplace listings, searching or filtering items, retrieving individual product details, and handling purchases or inventory updates.
marketplace-page.tsx - Summery

MarketplacePage is a minimal, stateless React page component that currently renders nothing more than a <div> containing the text "Marketplace feature placeholder." It is the default export of its file, meaning it is intended to be used as a route-level component that represents the entire marketplace section of the application.

Like the InvoicesPage and DashboardPage components seen earlier, this follows the same consistent placeholder pattern used throughout the codebase, allowing the routing and navigation structure of the application to be fully set up before the detailed UI and logic of the marketplace feature is implemented. Other parts of the codebase such as a router configuration can already point to this page without causing errors.

Overall, this is a stub page that reserves the place for the full marketplace feature. In the future it would be expected to wrap a MarketplacePanel inside an AppShell layout, similar to how the other page components are structured, and eventually display a complete marketplace interface with product listings, search and filtering, and purchasing functionality powered by the marketplaceApi.
marketplace.types.ts - Summary

MarketplaceRecord is a TypeScript interface that defines the structure of a single marketplace record within the application. At this stage it only contains one required field — id — which serves as a unique identifier for each marketplace entry, following the same minimal starting point seen with DashboardRecord and InvoicesRecord earlier in the codebase.

Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component, function, or API call that works with marketplace records follows the same data shape. This promotes consistency and type safety throughout the marketplace feature, and reflects the uniform pattern being applied across all features in the application.

Overall, this is a minimal and foundational type definition that is clearly part of a consistent pattern followed across the codebase for defining feature-level data models. In the future, more fields such as name, price, category, stock, or sellerId would be expected to be added as the marketplace feature is developed and its data requirements become clearer.

In practical terms, it's a "set it once, use everywhere" shell. You wrap your page content inside `<AppShell>` and it handles the framing, so all your pages stay visually consistent without repeating the same boilerplate code each time.

Notifications(Api):
Empty for now with a TODO comment, ready to have notification-related methods added when the feature gets built out.

Notifications(Components):
 A placeholder component that renders a plain div until the notifications feature gets built out.

Notifications(Pages):
A placeholder page component for the notifications route, skipping AppShell and NotificationsPanel for now until the feature gets built out.

Notifications(Types):
A bare-minimum placeholder type with just an id field to establish the interface exists, ready to be expanded with real fields like message content, read status, recipient, and timestamp when the feature gets built out.

parts.api.ts - Summary

partsApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to the parts feature. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the parts feature is built out, consistent with the same pattern seen across dashboardApi, invoicesApi, and marketplaceApi.

The object is exported so that other parts of the codebase, such as components or services that deal with parts data, can already import and reference it without needing to restructure their imports later. This reflects the deliberate and uniform architectural approach being applied consistently across all features in the application, making the codebase predictable and easy to navigate.

Overall, this is a foundational placeholder that establishes partsApi as the designated home for all parts-related API logic. In a real application, it would be expected to contain functions for operations such as fetching a list of parts, searching or filtering by part type or compatibility, retrieving individual part details, and managing stock levels or supplier information.
PartsPanel.tsx - Summary 

PartsPanel is a minimal, stateless React component that currently renders nothing more than a <div> containing the text "Parts panel placeholder." It is exported so it can be imported and used in other parts of the application, such as a page-level component, while the actual implementation is still pending.

The placeholder pattern continues to be applied consistently across the codebase, following the same approach seen with InvoicesPanel and other feature components. This allows the overall structure and navigation of the application to be fully wired up before the detailed UI and logic of each individual feature is implemented, keeping development organized and predictable.

Overall, this component is simply a stub that reserves the space and name for the parts panel feature. In the future it would be expected to display a list or table of parts data, likely fetching information from the partsApi and presenting it with details such as part names, stock levels, compatibility, and supplier information, consistent with the broader workshop management theme of the application.
parts-page.tsx - Summary

PartsPage is a minimal, stateless React page component that currently renders nothing more than a <div> containing the text "Parts feature placeholder." It is the default export of its file, meaning it is intended to be used as a route-level component that represents the entire parts section of the application.

Like the InvoicesPage and MarketplacePage components seen earlier, this follows the same consistent placeholder pattern used throughout the codebase, allowing the routing and navigation structure of the application to be fully set up before the detailed UI and logic of the parts feature is implemented. Other parts of the codebase such as a router configuration can already point to this page without causing errors.

Overall, this is a stub page that reserves the place for the full parts feature. In the future it would be expected to wrap the PartsPanel inside an AppShell layout, similar to how the other page components are structured, and eventually display a complete parts management interface with real data, stock levels, filtering, and supplier information powered by the partsApi.
parts.types.ts

PartsRecord is a TypeScript interface that defines the structure of a single parts record within the application. At this stage it only contains one required field — id — which serves as a unique identifier for each parts entry, following the same minimal starting point seen consistently with DashboardRecord, InvoicesRecord, and MarketplaceRecord earlier in the codebase.

Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component, function, or API call that works with parts records follows the same data shape. This promotes consistency and type safety throughout the parts feature, and reinforces the uniform pattern being applied across all features in the application.

Overall, this is a minimal and foundational type definition that is clearly part of the consistent pattern followed across the entire codebase for defining feature-level data models. In the future, more fields such as name, partNumber, stock, supplier, or compatibility would be expected to be added as the parts feature is developed and its data requirements become clearer.

parts.api.ts - Summary

partsApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to the parts feature. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later as the parts feature is built out, consistent with the same pattern seen across dashboardApi, invoicesApi, and marketplaceApi.

The object is exported so that other parts of the codebase, such as components or services that deal with parts data, can already import and reference it without needing to restructure their imports later. This reflects the deliberate and uniform architectural approach being applied consistently across all features in the application, making the codebase predictable and easy to navigate.

Overall, this is a foundational placeholder that establishes partsApi as the designated home for all parts-related API logic. In a real application, it would be expected to contain functions for operations such as fetching a list of parts, searching or filtering by part type or compatibility, retrieving individual part details, and managing stock levels or supplier information.
PartsPanel.tsx - Summary 

PartsPanel is a minimal, stateless React component that currently renders nothing more than a <div> containing the text "Parts panel placeholder." It is exported so it can be imported and used in other parts of the application, such as a page-level component, while the actual implementation is still pending.

The placeholder pattern continues to be applied consistently across the codebase, following the same approach seen with InvoicesPanel and other feature components. This allows the overall structure and navigation of the application to be fully wired up before the detailed UI and logic of each individual feature is implemented, keeping development organized and predictable.

Overall, this component is simply a stub that reserves the space and name for the parts panel feature. In the future it would be expected to display a list or table of parts data, likely fetching information from the partsApi and presenting it with details such as part names, stock levels, compatibility, and supplier information, consistent with the broader workshop management theme of the application.
parts-page.tsx - Summary

PartsPage is a minimal, stateless React page component that currently renders nothing more than a <div> containing the text "Parts feature placeholder." It is the default export of its file, meaning it is intended to be used as a route-level component that represents the entire parts section of the application.

Like the InvoicesPage and MarketplacePage components seen earlier, this follows the same consistent placeholder pattern used throughout the codebase, allowing the routing and navigation structure of the application to be fully set up before the detailed UI and logic of the parts feature is implemented. Other parts of the codebase such as a router configuration can already point to this page without causing errors.

Overall, this is a stub page that reserves the place for the full parts feature. In the future it would be expected to wrap the PartsPanel inside an AppShell layout, similar to how the other page components are structured, and eventually display a complete parts management interface with real data, stock levels, filtering, and supplier information powered by the partsApi.
parts.types.ts

PartsRecord is a TypeScript interface that defines the structure of a single parts record within the application. At this stage it only contains one required field — id — which serves as a unique identifier for each parts entry, following the same minimal starting point seen consistently with DashboardRecord, InvoicesRecord, and MarketplaceRecord earlier in the codebase.

Being exported means it is intended to be shared and reused across multiple files in the codebase, ensuring that any component, function, or API call that works with parts records follows the same data shape. This promotes consistency and type safety throughout the parts feature, and reinforces the uniform pattern being applied across all features in the application.

Overall, this is a minimal and foundational type definition that is clearly part of the consistent pattern followed across the entire codebase for defining feature-level data models. In the future, more fields such as name, partNumber, stock, supplier, or compatibility would be expected to be added as the parts feature is developed and its data requirements become clearer.

Payments(api):
Same pattern as `inspectionsApi` and `notificationsApi` — an empty placeholder object ready to have payment-related methods like `create`, `list`, and `refund` added when the feature gets built out.

Payments(components):
Same as the other placeholder panels — an empty component ready to be built out when the payments feature gets developed.

Payments(Pages):
Same placeholder page pattern as inspections and notifications — no `AppShell` or panel composition yet, just a div until the payments feature gets built out.

Payments(types):
Same minimal placeholder type as `InspectionsRecord` and `NotificationsRecord` — just an `id` field to establish the interface, ready to be expanded with real fields like amount, currency, status, and job reference when the feature gets built out.

quotes.api.ts - Summary

quotesApi is a fully implemented API object that provides three functions for managing quotes within the application. Unlike the placeholder API objects seen earlier in the codebase, this one contains real logic, using a shared apiClient utility and centralized endpoints to keep the code clean, consistent, and easy to maintain.

The three functions cover the core lifecycle of a quote — listing quotes by job, creating a new quote with detailed line items, and approving a quote via a token-based public URL. The create function is the most complex, accepting a structured payload that includes optional financial fields and an array of typed line items representing either labor or parts, reflecting the workshop management context of the application.

Overall, quotesApi is a well-structured and feature-complete API module that follows clean coding practices by centralizing endpoint references, reusing a shared API client, and enforcing strict TypeScript typing through the QuoteRecord type. It serves as a good example of what the other placeholder API objects such as partsApi, invoicesApi, and marketplaceApi are expected to look like once they are fully implemented.
QuoteBuilder.tsx - Summary

QuoteBuilder is a stateless React component that renders a detailed and well-structured quote card using hardcoded demo data. It imports the StatusBadge component from a shared components directory to display the current status of the quote as a pill-shaped badge, keeping the visual language consistent with the rest of the application.

The hardcoded demoQuote object defines all the data the component needs to render, including the quote version, status, subtotal, tax amount, discount amount, total, and an array of two line items representing a labor charge and a parts charge. This demo data serves as a placeholder until real data from the quotesApi is wired in.

The component is divided into three distinct visual sections — a header, a line items grid, and a financial summary. The header uses flexbox to place the quote version number on the left and the StatusBadge on the right, giving it a clean and balanced appearance consistent with other panel components in the codebase.

The line items section dynamically maps over the lines array and renders each entry in a four-column grid displaying the description, quantity, unit price, and line total. All monetary values are formatted to two decimal places using .toFixed(2), ensuring a consistent and professional appearance. A horizontal divider separates the line items from the financial summary below.

Overall, QuoteBuilder is a well-structured and visually clear presentational component that effectively communicates the contents and financials of a quote. In a real application it would be expected to receive dynamic data from the quotesApi, and could be expanded to include interactive features such as adding or removing line items, applying discounts, and allowing users to submit or approve quotes directly from the UI.
QuotesPanel.tsx - Summary

QuotesPanel is a minimal, stateless React component that serves as a thin wrapper around the QuoteBuilder component, simply rendering it without adding any additional logic or styling. This pattern is common in React applications where a panel component acts as an intermediate layer between a page and its content, allowing for future flexibility to add layout, headers, or additional components without modifying the page or the QuoteBuilder directly.

Overall, while this component is very simple right now, its existence as a separate layer is architecturally intentional. In the future, QuotesPanel could be expanded to include features such as a toolbar, filtering options, a list of multiple quotes, or pagination controls, all sitting alongside the QuoteBuilder — making it a useful abstraction that keeps the codebase organized and scalable.You said: import { StatusBadge } from '.
quotes-page.tsx - Summary

QuotesPage is a clean and minimal React page component that serves as the entry point for the quotes section of the application. It imports two components — AppShell for the overall page layout and QuotesPanel for the main content — and composes them together into a single cohesive page, following the exact same pattern established by DashboardPage earlier in the codebase.

The component wraps QuotesPanel inside AppShell, passing "Quotes" as the page title, which AppShell likely uses to render a header or browser tab title. This separation of layout and content is a deliberate and clean architectural choice that keeps each component focused on a single responsibility, making the codebase easier to maintain and scale.

Overall, QuotesPage is intentionally simple, with its only job being to combine the shared layout shell and the quotes content into one complete page. This mirrors the structure of other page components in the application such as DashboardPage and InvoicesPage, confirming that a consistent and well-organized pattern is being followed throughout the entire codebase.
quotes.types.ts - Summary 

QuoteLineRecord is a TypeScript interface that defines the structure of a single line item within a quote. It contains six fields, each representing a specific piece of information about the line item, including a unique id, a type restricted to either 'LABOR' or 'PART', a text description, a quantity, a unitPrice, and a calculated total. The use of a union type for the type field ensures that only valid values can be assigned, adding an extra layer of type safety to the data model.

QuoteRecord is a more comprehensive TypeScript interface that defines the full structure of a quote within the application. It includes essential identifiers such as id, tenantId, and jobId to link the quote to a specific tenant and job, as well as a version number and a status string to track the lifecycle and current state of the quote throughout the application.

The financial fields within QuoteRecord — subtotal, taxAmount, discountAmount, and total — together provide a complete picture of the quote's monetary value, covering all stages of the calculation from the raw subtotal through to the final total after tax and discounts. The optional publicToken field enables token-based public access to the quote, which is consistent with the approve function seen earlier in the quotesApi.

The two interfaces work together to form a complete and well-structured data model for the quotes feature, with QuoteRecord referencing QuoteLineRecord through its optional lines array to create a clear parent-child relationship between a quote and its individual line items. Together they provide a solid and type-safe foundation that is used consistently across the quotes feature, from the quotesApi functions to the QuoteBuilder component, ensuring data integrity and predictability throughout the entire codebase.
settings.api.ts - Summary

settingsApi is an exported constant that currently serves as an empty placeholder object, intended to eventually hold all API functions related to the settings feature of the application. The TODO comment signals that the developer has deliberately left this as a skeleton to be filled in later, following the exact same consistent pattern seen across dashboardApi, invoicesApi, marketplaceApi, and partsApi throughout the codebase.

Overall, this is a foundational placeholder that establishes settingsApi as the designated home for all settings-related API logic. In a real application, it would be expected to contain functions for operations such as fetching user or tenant settings, updating preferences, managing notifications, or handling configuration changes, all using the shared apiClient and centralized endpoints consistent with the rest of the application.
SettingsPanel.tsx - Summary

SettingsPanel is a minimal, stateless React component that currently renders nothing more than a <div> containing the text "Settings panel placeholder." It is exported so it can be imported and used in other parts of the application, such as a page-level component, while the actual implementation is still pending. This follows the exact same consistent placeholder pattern seen across InvoicesPanel, PartsPanel, and other feature panel components throughout the codebase.

Overall, this component is simply a stub that reserves the space and name for the settings panel feature. In the future it would be expected to display a full settings interface, likely containing forms and controls for managing user preferences, tenant configuration, notification settings, and other application-wide options, with data being fetched and updated through the settingsApi once it is fully implemented.
settings-page.tsx - Summary

SettingsPage is a minimal, stateless React page component that currently renders nothing more than a <div> containing the text "Settings feature placeholder." It is the default export of its file, meaning it is intended to be used as a route-level component that represents the entire settings section of the application, following the exact same consistent placeholder pattern seen across InvoicesPage, MarketplacePage, and PartsPage throughout the codebase.

Overall, this is a stub page that reserves the place for the full settings feature. In the future it would be expected to wrap the SettingsPanel inside an AppShell layout, passing "Settings" as the page title, consistent with how other fully implemented page components such as DashboardPage and QuotesPage are structured, and eventually display a complete settings management interface powered by the settingsApi.
settings.types.ts - Summary 

SettingsRecord is a TypeScript interface that defines the structure of a single settings record within the application. At this stage it only contains one required field — id — which serves as a unique identifier for each settings entry, following the exact same minimal starting point seen consistently with DashboardRecord, InvoicesRecord, MarketplaceRecord, and PartsRecord throughout the codebase.

Overall, this is a minimal and foundational type definition that is clearly part of the consistent pattern followed across the entire codebase for defining feature-level data models. In the future, more fields such as tenantId, theme, notificationsEnabled, language, or timezone would be expected to be added as the settings feature is developed and its data requirements become clearer.

suppliers.api.ts is a object declaration that defines an API module for suppliers

SuppliersPanel.tsx is a simple placeholder UI that defines and exports a react components called suppliers panel making it importable in other files

suppliers-page.tsx exactly the same as the panel just a page

suppliers.types.ts defines a Typescript interface for a suppliers data record-currently with only one field: ID:string -every supplier must have a string identifier

Tenants(api):
The tenants API layer, slightly different from the others in that it hardcodes the URL strings directly (`'/api/tenants'`) rather than pulling from the central `endpoints` object. It has two methods — `list` fetches all tenants, and `getById` fetches a single tenant by ID. The absence of `create`, `update`, or `delete` methods suggests tenant management is either handled elsewhere, restricted to backend admin tooling, or simply not built out yet.

Tenants(Types):
A well-defined type compared to the placeholder records seen in other features. `Tenant` has a clear shape with two notable details — the `type` field is a union literal (`'SERVICE_STATION' | 'SUPPLIER'`) rather than a plain string, meaning only those two values are valid and the compiler will catch anything else. The contact fields and timestamps are optional, suggesting a tenant can exist in a minimal state with just the core identity fields. This also lines up with the `AuthUser` type seen earlier, which references `tenantId` and includes a nested `AuthTenant` object with a similar shape.

Tenants(users):
Users(api):
The users API layer, closely mirroring `tenantsApi` in structure with `list` and `getById` methods. The one notable detail is that `list` scopes results by `tenantId` using a query parameter, and wraps it in `encodeURIComponent` to safely handle any special characters in the ID — a small but correct defensive detail absent from the equivalent `jobsApi.list` call. Like `tenantsApi`, it hardcodes URL strings directly rather than using the central `endpoints` object, which is a minor inconsistency worth aligning when the codebase gets tidied up.

Users(types):
This interface is nearly identical to `AuthUser` from the auth types file — same fields, same optional markers. The only difference is `AuthUser` includes a nested `tenant?: AuthTenant` object while `User` doesn't. This suggests `AuthUser` is the enriched version returned during login and session hydration, where the full tenant details are embedded for convenience, while `User` is the leaner version returned by the users API when listing or fetching users in an admin context where the tenant details aren't needed.

Vehicles(api):
VehiclesApi has the full set of CRUD operations plus a search capability. list accepts an optional query parameter that appends a search term to the request, wrapped in encodeURIComponent for safety, making it the only list method in the codebase that supports filtering. getById, create, and update follow the same patterns seen elsewhere, with update using PATCH to send only changed fields via a dedicated VehicleUpdatePayload type rather than the full record.
The most interesting method is archive — it uses a DELETE HTTP method but returns a VehicleRecord rather than a simple success response. This suggests the backend performs a soft delete, marking the vehicle as archived rather than removing it from the database, and returns the updated record to confirm the change. This is a sensible design for a service platform where historical job and inspection records need to remain linked to a vehicle even after it's no longer active.

Vehicles(components):
A complete CRUD interface for managing vehicle records. On mount it fetches both vehicles and customers in parallel using `Promise.all`, since the vehicle form needs the customer list for its dropdown and the vehicle cards need customer names for display. A `search` state drives the list filtering, with explicit Search and Clear buttons rather than live-as-you-type, which avoids hammering the API on every keystroke.

The form handles both creating and editing through a single `editingId` state — when `null` the form creates, when set to a vehicle's ID it switches to edit mode and pre-populates all fields via `startEdit`. The `year` and `odometer` fields get explicit `Number()` conversion on submit because HTML inputs always return strings, and the type expects numbers. `resetForm` is careful to preserve the currently selected customer rather than blindly resetting to the default, keeping the form in a sensible state after each operation.

The right column renders each vehicle as a card showing registration, make, model, owner name, year, odometer, VIN, fuel type, and transmission — with `customerNameFor` doing a local lookup against the already-fetched customers array rather than making extra API calls. Every async operation — load, submit, archive — follows the same try/catch/finally pattern with a shared error display at the bottom, and archiving a vehicle that's currently being edited automatically resets the form to avoid leaving stale data in the inputs.

Vehicles(Page):
Same thin page pattern as `AuthPage` and `JobsPage` — composes `AppShell` and `VehiclesPanel` together with `"Vehicles"` as the title, no logic of its own, just the routable entry point for the vehicles URL.

Vehicles(Types):
A well-structured types file that does more with less by deriving its payload types from the base interface rather than repeating fields. `VehicleRecord` is the full shape returned by the API — only `id`, `tenantId`, `customerId`, `registrationNumber`, `make`, and `model` are required, everything else is optional reflecting that a vehicle can be registered with minimal information and enriched later.

The two payload types are derived cleanly using TypeScript utility types. `VehicleCreatePayload` uses `Omit` to strip the server-generated fields (`id`, `createdAt`, `updatedAt`, `isArchived`) since the client shouldn't send those. `VehicleUpdatePayload` then wraps `VehicleCreatePayload` in `Partial`, making every field optional since a PATCH only needs to include what's changing. This three-type pattern — record, create payload, update payload — is the right approach and worth applying to `JobRecord` and other entities as the codebase matures.

SERVICES 
endpoints.ts this is the endpoints registry a central map of every API route in the application it stores all URL strings in onew place instead bof scattering raw strings like api customers.(Most endpoints are simple flat strings) 
the application tells you 
customer-Vehicle-Job-Quote-Parts-Invoice-Payment

app-store.ts
this is a global store definition it describes the shape of the application-wide state and its starting value(defines TenantID as a string, USERID as a string and auth as a authsessionstate all three fields are optional so it can be empty








































































































































