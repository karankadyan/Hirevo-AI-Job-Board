# AI-Powered Job Board

This project is a **fully functional job board** that handles users, organisations, payment plans, and subscriptions. It takes the concept to the next level by integrating **AI features throughout the entire application**. The development also focuses on **best practices with Next.js, TypeScript, and databases**, ensuring clean and maintainable code. This is described as a "massive project" that would look "incredible on any resume or portfolio".

## Key Features

The job board offers a comprehensive set of functionalities, enhanced by artificial intelligence:

*   **User and Organisation Management**
    *   Handles users, organisations, permissions, and roles, simplifying implementation through Clerk.
    *   **Organizations** have an ID, name, image URL (nullable), creation and update timestamps.
    *   **Users** have an ID (from Clerk), name, image URL, email (unique and not null), creation and update timestamps.
    *   Supports **subscription billing** and different payment plans (Basic, Growth, Enterprise) using Clerk's billing features.
    *   Enables **organization management** with roles like `admin`, `applicant manager`, and `job listing manager`, each with custom permissions.
    *   Manages **user-specific notification settings** for organizations, including email preferences and minimum rating thresholds for applicants.

*   **Job Listing Management**
    *   Employers can **create, update, and delete job listings**.
    *   Job listings include fields such as title, description (text input), wage (integer), wage interval (hourly/yearly enum), location (state abbreviation, city), and boolean for `isFeatured`.
    *   Job listings have a `status` (draft, published, dlisted) and a `postedAt` timestamp.
    *   **Dynamic status changes**: Jobs can transition from draft/dlisted to published, and from published to dlisted.
    *   **Featured job listings**: Certain plans allow for "featured" job listings that appear at the top, offering an extra incentive.
    *   **Caching** is implemented for job listings to improve performance.

*   **Application Process**
    *   Users can **apply for jobs** by submitting their resume and cover letter.
    *   An **AI-powered ranking system** compares the applicant's resume/cover letter to the job listing, providing a star rating (1-5) for fit.
    *   The application process supports **resume uploads**.
    *   Application details include `jobListingID`, `userID` (forming a composite primary key), `coverLetter` (text), `rating`, and `applicationStage` (enum: applied, interested, interviewed, hired, denied).

*   **AI-Powered Features**
    *   **AI Search**: Users can type a natural language prompt (e.g., "entry-level web developer job") to find jobs matching their criteria. This process uses AI models to identify relevant job listings.
    *   **AI Resume Summaries**: When a resume is uploaded, the AI automatically generates a summary that is stored and used for the ranking system. This uses models like Anthropic Claude.
    *   **Applicant Ranking**: The AI rates applicants (1-5 stars) based on their resume, cover letter, and the job listing requirements. This is handled by an "applicant ranking agent" using models like Google Gemini.
    *   **Daily Email Notifications**: Users can set up daily email alerts based on custom AI filter prompts for new jobs that fit their exact criteria. Employers also receive daily summaries of applicants meeting a minimum star rating.

*   **Technical Implementations**
    *   **Dark and Light Mode**: The application fully supports both dark and light modes, swapping based on browser preferences.
    *   **Background Jobs**: Uses **Ingest** for cron jobs, background jobs, and most importantly, integrating AI features, which would be "really difficult and annoying" in plain Next.js. Ingest handles retry logic and fault tolerance for long-running tasks.
    *   **Webhooks**: Clerk webhooks (user and organisation events) are seamlessly integrated with Ingest for real-time data synchronisation and processing.
    *   **Database**: Built with **PostgreSQL** and managed using **Drizzle ORM** for type-safe database interactions and migrations. Docker is used for local PostgreSQL setup.
    *   **File Uploads**: Leverages **UploadThing** for resume uploads and management, including deleting old files when new ones are uploaded.
    *   **Email Sending**: Uses **Resend** for sending automated email notifications.
    *   **Responsive Design**: Employs **Tailwind CSS** with **container queries** for highly flexible and responsive layouts that adapt to changing sidebar sizes, rather than just screen size.
    *   **UI Components**: Utilises **Shadcn/UI** components for consistent and high-quality UI elements, including tables, dialogs, and forms.
    *   **Performance**: Implements **caching** (via Next.js dynamic IO and custom cache tags) for frequently accessed data to ensure quick responses.
    *   **Form Handling**: Integrates **React Hook Form** with **Zod resolver** for robust form validation and type safety.

## Technologies Used

*   **Framework**: **Next.js 15 (Canary)**
*   **Styling**: **Tailwind CSS** & **Shadcn/UI**
*   **Database**: **PostgreSQL** with **Drizzle ORM**
*   **Authentication & User Management**: **Clerk**
*   **Background Jobs & AI Orchestration**: **Ingest**
*   **File Uploads**: **UploadThing**
*   **AI Models**: **Anthropic Claude** & **Google Gemini**
*   **Email Service**: **Resend**
*   **Data Validation**: **Zod**
*   **Form Library**: **React Hook Form**
*   **Markdown Editor**: **MDX Editor** & **Next MDX Remote**
*   **Webhooks Verification**: **Svix**
*   **Transitions**: **React Transition**
*   **Date Utilities**: **date-fns**

## Setup Instructions

To get the AI-Powered Job Board running on your local machine, follow these steps:

### Prerequisites

*   **Node.js** (LTS recommended)
*   **npm** (Node Package Manager)
*   **Docker** (for running PostgreSQL locally)

### 1. Clone the Repository (Implied)

(Assuming you have cloned the project repository to your local machine.)

### 2. Install Dependencies

Navigate to the project directory in your terminal and install the required Node.js packages:

```bash
npm install --force
```

**Note**: The `--force` flag is necessary due to the project using the **Canary version of Next.js 15** which may have compatibility issues with some packages.

### 3. Environment Variables Setup

Create a `.env` file in the root of your project. This file will store sensitive API keys and configuration settings.

Example `.env` structure:

```
# Database
DB_PASSWORD=password
DB_USER=postgres
DB_HOST=localhost
DB_PORT=5432
DB_NAME=jobboard

# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_your_clerk_publishable_key
CLERK_SECRET_KEY=sk_live_your_clerk_secret_key
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/clerk/sign-in
NEXT_PUBLIC_CLERK_AFTER_SIGN_IN_URL=/employer
NEXT_PUBLIC_CLERK_AFTER_SIGN_UP_URL=/employer
CLERK_WEBHOOK_SECRET=whsec_your_clerk_webhook_secret

# Ingest
INGEST_API_KEY=your_ingest_api_key

# UploadThing
UPLOADTHING_SECRET=sk_your_uploadthing_secret_key
NEXT_PUBLIC_UPLOADTHING_APP_ID=your_uploadthing_app_id

# AI API Keys
ANTHROPIC_API_KEY=sk-your_anthropic_api_key
GEMINI_API_KEY=your_gemini_api_key

# Resend
RESEND_API_KEY=re_your_resend_api_key

# Other
SERVER_URL=http://localhost:3000
```

**Explanation of variables and how to obtain them:**

*   **Database (PostgreSQL)**: These are standard PostgreSQL credentials. `DB_PASSWORD` can be set to 'password' for local development. `DB_NAME` is set to `jobboard` by default.
*   **Clerk**:
    *   **`NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` & `CLERK_SECRET_KEY`**: Obtain these from your Clerk dashboard after creating a new project. Navigate to "API Keys".
    *   **Clerk Callback URLs**: These are configured within the Clerk dashboard and specify where users are redirected after sign-in/sign-up. The provided URLs (`/employer`) are used.
    *   **`CLERK_WEBHOOK_SECRET`**: Found under "Webhooks" in your Clerk dashboard.
*   **Ingest**:
    *   **`INGEST_API_KEY`**: Sign up for an Ingest account and obtain your API key from your dashboard.
*   **UploadThing**:
    *   **`UPLOADTHING_SECRET`**: Get this from your UploadThing dashboard after creating an app.
    *   **`NEXT_PUBLIC_UPLOADTHING_APP_ID`**: Also from your UploadThing dashboard.
*   **AI API Keys**:
    *   **`ANTHROPIC_API_KEY`**: Obtain from the Anthropic API console. Free usage may be available for new accounts.
    *   **`GEMINI_API_KEY`**: Get from the Google AI Studio or Google Cloud Console. Gemini offers a free tier.
*   **Resend**:
    *   **`RESEND_API_KEY`**: Create an account on Resend and generate an API key. Ensure it has "sending access".
*   **`SERVER_URL`**: Set this to your application's base URL, typically `http://localhost:3000` during development.

### 4. Database Setup

The project uses Docker for local PostgreSQL setup:

*   **Start PostgreSQL Container**:
    Open a terminal in the project root and run:
    ```bash
    docker compose up
    ```
    This will start a PostgreSQL 17 container accessible at `localhost:5432`.
*   **Generate Drizzle Migrations**:
    Open a **new** terminal in the project root and run:
    ```bash
    npm run db generate
    ```
    This command generates SQL migration files based on your Drizzle schema, located in `source/drizzle/migrations`.
*   **Run Database Migrations**:
    In the same terminal, execute the migrations:
    ```bash
    npm run db migrate
    ```
    This applies the generated SQL to your PostgreSQL database, creating all necessary tables.
*   **(Optional) Inspect Database**:
    To view your database tables and data, run Drizzle Studio:
    ```bash
    npm run db studio
    ```
    This opens a web interface in your browser to introspect your database.

### 5. Configure Clerk

*   **Enable Features**: In your Clerk dashboard, ensure "Organization Management" and "Billing" are enabled under "Settings".
*   **Define Roles and Permissions**: Configure roles (Admin, Applicant Manager, Job Listing Manager) and permissions (e.g., `job_listing_applications:change_rating`, `job_listings:create`) to manage user access and capabilities within organizations.
*   **Set up Subscription Plans**: Create "Organization Plans" (Basic, Growth, Enterprise) with associated prices and features (e.g., "create job listings", "post one job listing", "unlimited featured jobs").
*   **Integrate Webhooks with Ingest**: In your Clerk dashboard, go to "Webhooks" and add a new endpoint. Paste your Ingest webhook URL (e.g., `https://api.ingest.io/webhook/clerk`) and subscribe to events such as `organization.created`, `organization.updated`, `organization.deleted`, `organizationMembership.created`, `organizationMembership.deleted`, `user.created`, `user.updated`, `user.deleted`.

### 6. Configure Ingest

*   **Sign Up**: If you haven't already, sign up for Ingest.
*   **Create Environment**: Create a `dev` environment in Ingest to keep development and production webhooks separate.
*   **Add Clerk Webhook**: In your Ingest `dev` environment, add a webhook for Clerk. The payload transformation should extract the `event.type` for event naming and include `data`, `headers`, and `raw` properties for verification.
*   **Run Ingest Dev Server**:
    Open a **new** terminal in the project root and run:
    ```bash
    npm run ingest
    ```
    This starts the Ingest development server, which connects to your running Next.js application and allows you to monitor background jobs.

### 7. Configure UploadThing

*   **Create App**: In the UploadThing dashboard, create a new application.
*   **API Key**: Copy your `UPLOADTHING_SECRET` and `NEXT_PUBLIC_UPLOADTHING_APP_ID` to your `.env` file.

### 8. Configure Resend (Optional - For Email Preview)

*   **Create Account**: Sign up for a Resend account.
*   **API Key**: Obtain your API key from the Resend dashboard and add it to your `.env` file.
*   **Run Email Development Server**:
    To preview emails locally:
    ```bash
    npm run email
    ```
    This starts a development server on port 3001 (by default), hosting your email templates for easy preview.

### 9. Run the Application

Once all prerequisites and configurations are in place, start the Next.js development server:

```bash
npm run dev
```

Your AI-Powered Job Board should now be running locally, typically accessible at `http://localhost:3000`. You can test the login page at `http://localhost:3000/clerk/sign-in`.

Enjoy building and exploring this comprehensive job board application!# Hirevo-AI-Job-Board
