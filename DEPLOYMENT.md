# Deployment Guide

## Overview

Telegram-Bot is designed to run as an always-available Deno application so it can receive Telegram updates, publish messages, manage access, and process scheduled work without depending on the owner’s device being online. The recommended production target is **Deno Deploy**, with self-hosting as a fallback when you need full infrastructure control. [docs.deno](https://docs.deno.com/runtime/deploy/)

This guide covers the full deployment flow:
- Choose a hosting option.
- Configure environment variables.
- Deploy the app.
- Connect Telegram through a webhook.
- Verify production behavior. [grammy](https://grammy.dev/hosting/deno-deploy)

## Hosting Options

### Deno Deploy
Deno Deploy is the simplest option for a Deno-based Telegram bot because it provides managed deployment, environment variable management, and monitoring in one place. You can create an app, connect a GitHub repository, set secrets, and publish from the dashboard or CLI. [docs.deno](https://docs.deno.com/deploy/)

### Self-Hosted Deno
If you want to run the bot on your own server, Deno supports two main approaches: compile a standalone binary with `deno compile`, or install Deno and run the app directly with `deno serve` behind a process manager such as systemd. [docs.deno](https://docs.deno.com/runtime/deploy/)

## Prerequisites

Before deployment, make sure you have:
- A Telegram bot token from BotFather.
- A working Deno project.
- A GitHub repository for the code.
- A public HTTPS URL for webhook delivery.
- The environment variables needed by the app. [docs.deno](https://docs.deno.com/deploy/getting_started/)

## Environment Variables

Telegram-Bot should keep secrets and production settings in environment variables. Deno Deploy lets you add them in the app settings, organization settings, or during project creation, and the values are available in code through `Deno.env.get()`. [docs.deno](https://docs.deno.com/deploy/classic/environment-variables/)

Typical variables include:

| Variable | Purpose |
| --- | --- |
| `TELEGRAM_BOT_TOKEN` | Telegram bot token used to authenticate with Telegram. |
| `WEBHOOK_SECRET_TOKEN` | Secret value used to validate webhook requests. |
| `OWNER_TELEGRAM_ID` | Telegram ID of the owner who can access admin functions. |
| `DENO_ENV` | Environment flag such as `production` or `development`. |

If you use a `.env` file locally, Deno Deploy can also import values from it during setup. [docs.deno](https://docs.deno.com/deploy/reference/env_vars_and_contexts/)

## Deno Deploy Setup

### 1. Create the app
Open Deno Deploy and create a new application or organization if needed. Deno’s getting-started flow begins with this setup step and then moves into build configuration and environment variables. [docs.deno](https://docs.deno.com/deploy/)

### 2. Connect GitHub
Link the Telegram-Bot repository from GitHub so updates can be deployed from your main branch. This is the easiest way to keep production aligned with source control. [docs.deno](https://docs.deno.com/deploy/getting_started/)

### 3. Set environment variables
Add the production values in the Deno Deploy settings. Mark secrets such as the Telegram token and webhook secret as protected values. [docs.deno](https://docs.deno.com/deploy/classic/environment-variables/)

### 4. Deploy the app
Deploy the project from the dashboard or the CLI. Deno Deploy creates a live deployment and gives you a public URL for the app. [docs.deno](https://docs.deno.com/runtime/reference/cli/deploy/)

## Webhook Configuration

Telegram bots usually work best in webhook mode for production because Telegram sends updates directly to your application. A common setup is to deploy the app first, then register the deployed HTTPS URL with Telegram using the `setWebhook` endpoint. [github](https://github.com/tv1ster/telegram-deno)

Example webhook call:

```bash
curl "https://api.telegram.org/bot<TELEGRAM_BOT_TOKEN>/setWebhook?url=https://<YOUR_APP_URL>/webhook"
```

If your bot uses a secret token for validation, keep that value in sync with your application code and deployment settings. [grammy](https://grammy.dev/hosting/deno-deploy)

## Local Development

A local setup is useful for testing before production deployment.

### Example local flow
1. Copy your environment file.
2. Set your local values.
3. Run the bot with Deno.
4. Test commands, publishing, and scheduling.

Example:

```bash
cp .env.example .env
deno task dev
```

Deno Deploy documentation also supports importing variables from `.env` files, which makes local-to-production setup easier. [docs.deno](https://docs.deno.com/deploy/reference/env_vars_and_contexts/)

## Self-Hosted Setup

If you choose to self-host, you can deploy Telegram-Bot in one of two ways:
- **Compile a standalone binary** with `deno compile`.
- **Run Deno directly** on a server with `deno serve`.

For a VPS or bare-metal server, the usual pattern is:
1. Build or compile the app.
2. Copy it to the server.
3. Configure environment variables.
4. Run it behind a process manager such as systemd.
5. Expose it through HTTPS. [docs.deno](https://docs.deno.com/runtime/deploy/)

## Production Checklist

Before you go live, confirm the following:
- The bot token is stored as a secret.
- The owner account is correctly configured.
- Webhook requests reach the deployed URL.
- Publishing works in each destination.
- Button updates work after publication.
- Scheduled jobs trigger on time.
- Access requests are limited to approved users. [docs.deno](https://docs.deno.com/deploy/classic/deployments/)

## Monitoring and Updates

Deno Deploy provides deployment management and monitoring, so each new release can be tracked as its own deployment. This makes it easier to verify changes, roll forward updates, and keep production behavior visible. [docs.deno](https://docs.deno.com/deploy/classic/deployments/)

For self-hosted deployments, use your server logs and process manager to monitor uptime, restarts, and failed webhook deliveries. [docs.deno](https://docs.deno.com/runtime/deploy/)

## Troubleshooting

### The bot does not receive updates
Check that:
- The webhook URL is correct.
- The app is reachable over HTTPS.
- The deployment is live.
- Telegram can access the endpoint. [github](https://github.com/tv1ster/telegram-deno)

### Environment variables are missing
Confirm that the required values are added in the Deno Deploy settings or in your local `.env` file, and ensure the code reads them with `Deno.env.get()`. [docs.deno](https://docs.deno.com/deploy/classic/environment-variables/)

### Scheduled tasks do not run
Check that the production deployment stays active and that the scheduler logic is part of the running app, not only the local environment. [docs.deno](https://docs.deno.com/deploy/)

## Suggested `DEPLOYMENT.md`
