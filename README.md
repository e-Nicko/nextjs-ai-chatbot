<a href="https://chat.vercel.ai/">
  <img alt="Next.js 14 and App Router-ready AI chatbot." src="app/(chat)/opengraph-image.png">
  <h1 align="center">Chat SDK</h1>
</a>

<p align="center">
    Chat SDK is a free, open-source template built with Next.js and the AI SDK that helps you quickly build powerful chatbot applications.
</p>

<p align="center">
  <a href="https://chat-sdk.dev"><strong>Read Docs</strong></a> ·
  <a href="#features"><strong>Features</strong></a> ·
  <a href="#model-providers"><strong>Model Providers</strong></a> ·
  <a href="#deploy-your-own"><strong>Deploy Your Own</strong></a> ·
  <a href="#running-locally"><strong>Running locally</strong></a>
</p>
<br/>

## Features

- [Next.js](https://nextjs.org) App Router
  - Advanced routing for seamless navigation and performance
  - React Server Components (RSCs) and Server Actions for server-side rendering and increased performance
- [AI SDK](https://sdk.vercel.ai/docs)
  - Unified API for generating text, structured objects, and tool calls with LLMs
  - Hooks for building dynamic chat and generative user interfaces
  - Supports xAI (default), OpenAI, Fireworks, and other model providers
- [shadcn/ui](https://ui.shadcn.com)
  - Styling with [Tailwind CSS](https://tailwindcss.com)
  - Component primitives from [Radix UI](https://radix-ui.com) for accessibility and flexibility
- Data Persistence
  - [Neon Serverless Postgres](https://vercel.com/marketplace/neon) for saving chat history and user data
  - [Vercel Blob](https://vercel.com/storage/blob) for efficient file storage
- [Auth.js](https://authjs.dev)
  - Simple and secure authentication

## Model Providers

This template ships with [xAI](https://x.ai) `grok-2-1212` as the default chat model. However, with the [AI SDK](https://sdk.vercel.ai/docs), you can switch LLM providers to [OpenAI](https://openai.com), [Anthropic](https://anthropic.com), [Cohere](https://cohere.com/), and [many more](https://sdk.vercel.ai/providers/ai-sdk-providers) with just a few lines of code.

## Running locally

You will need to use the environment variables [defined in `.env.example`](.env.example) to run Next.js AI Chatbot. It's recommended you use [Vercel Environment Variables](https://vercel.com/docs/projects/environment-variables) for this, but a `.env` file is all that is necessary.

> Note: You should not commit your `.env` file or it will expose secrets that will allow others to control access to your various AI and authentication provider accounts.

1. Install Vercel CLI: `npm i -g vercel`
2. Link local instance with Vercel and GitHub accounts (creates `.vercel` directory): `vercel link`
3. Download your environment variables: `vercel env pull`

```bash
pnpm install
pnpm dev
```

Your app template should now be running on [localhost:3000](http://localhost:3000).

## Troubleshooting Common Issues

During local setup, you might encounter a few common issues. Here's how to resolve them:

### 1. `MissingSecret` Error / Server Configuration Problem

- **Symptom (in browser):** `{"message":"There was a problem with the server configuration. Check the server logs for more information."}`
- **Symptom (in server logs):** `[auth][error] MissingSecret: Please define a \`secret\`. Read more at https://errors.authjs.dev#missingsecret`
- **Cause:** The `AUTH_SECRET` environment variable, crucial for Auth.js, is not set.
- **Solution:**
  1.  Generate a strong secret. You can use OpenSSL:
      ```bash
      openssl rand -hex 32
      ```
  2.  Create or open your `.env.local` file in the project root.
  3.  Add the generated secret to the file:
      ```env
      AUTH_SECRET=your_generated_secret_here
      ```
  4.  Restart your development server (`pnpm dev`).

### 2. Database Connection Error / `CallbackRouteError`

- **Symptom (in server logs):**
  - `[auth][error] CallbackRouteError: Read more at https://errors.authjs.dev#callbackrouteerror`
  - `[auth][cause]: Error: An error occurred while executing a database query.`
  - `[auth][details]: { ... "Failed to create guest user" ... }`
  - Running `pnpm db:migrate` fails with `Error: POSTGRES_URL is not defined`.
- **Cause:** The `POSTGRES_URL` environment variable, needed to connect to your Neon Serverless Postgres database, is not set or is incorrect. This template is designed to work with a Postgres database provisioned by Vercel (Neon).
- **Solution:**
  1.  Ensure you have followed the Vercel setup steps in the "Running locally" section, specifically:
      - Install Vercel CLI: `npm i -g vercel`
      - Link your project: `vercel link` (follow prompts to connect to your Vercel project where the chatbot is deployed/managed).
      - Pull environment variables: `vercel env pull .env.local`
        This command should populate your `.env.local` file with the correct `POSTGRES_URL` and other necessary variables from your Vercel project settings.
  2.  Once `.env.local` is populated with `POSTGRES_URL`, run database migrations:
      ```bash
      pnpm db:migrate
      ```
  3.  Restart your development server (`pnpm dev`).

### 3. Database Connection Timeout (`ETIMEDOUT`)

- **Symptom (in server logs, typically during `pnpm db:migrate` or app startup):** `Error: connect ETIMEDOUT <IP_ADDRESS>:<PORT>` (e.g., `Error: connect ETIMEDOUT 44.207.148.149:5432`)
- **Cause:** Your local machine cannot establish a connection with the remote Neon Postgres database. This is often due to:
  - **IP Whitelisting:** Your Neon database on Vercel might have IP whitelisting enabled, and your current public IP address is not on the allow list.
  - **Firewall/VPN:** A local or network firewall, or a VPN service, might be blocking the outgoing connection.
- **Solution:**
  1.  **Check IP Allow List on Vercel/Neon:**
      - Go to your Vercel Dashboard, navigate to your project, and find the settings for your Postgres (Neon) database.
      - Look for "IP Allow List" or similar networking settings.
      - Ensure your current public IP address is added to the list. (You can find your public IP by searching "what is my IP" on Google).
  2.  **Firewall/VPN:**
      - Temporarily disable any local firewall software or VPN client to see if this resolves the issue. Remember to re-enable them afterward.
      - If on a corporate network, consult your network administrator as there might be outbound port restrictions.
  3.  **Database Provisioning:** Ensure the Neon database associated with your Vercel project is fully provisioned and active.
  4.  After addressing potential blocks, try running `pnpm db:migrate` again, and then restart your development server (`pnpm dev`).
