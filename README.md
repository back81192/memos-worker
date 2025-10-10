[中文](./README_zh.md)

# Memos Worker: A Cloudflare-Powered Notes and Knowledge Base

<img width="1920" height="894" alt="image" src="https://github.com/user-attachments/assets/418b1034-60bd-44ab-a57b-9aceca6ae099" />
<img width="1920" height="894" alt="image" src="https://github.com/user-attachments/assets/525cc0d1-3d08-4d6b-8def-c4e44602b7e9" />
<img width="1920" height="894" alt="image" src="https://github.com/user-attachments/assets/2c341c9e-80a3-450c-8dbc-107f80d14f60" />
<img width="1920" height="894" alt="image" src="https://github.com/user-attachments/assets/3c965f5a-34dd-4efc-8c0c-d57859f1fbc8" />

**Memos Worker** is a powerful and high-performance serverless application for notes and knowledge management. Built entirely on the Cloudflare ecosystem (Workers, Pages, D1, R2, KV), it provides a private, cost-effective notes solution that you can own forever.

## ✨ Features

-   **✍️ Full-featured Markdown Support**: Supports real-time preview, split-screen editing, and smart pasting from rich text to Markdown.
-   **🗂️ Files & Attachments**: Supports drag-and-drop or pasting to upload images (to R2 or Imgur) and various file types.
-   **🔗 Public Sharing**: Generate unique, publicly accessible links for any file within your notes.
-   **🤖 Telegram Integration**: Record text, images, videos, and files on the go via a Telegram Bot.
-   **📚 Powerful Organization**: Automatic tagging, full-text search, timeline, calendar, and a contribution heatmap.
-   **📃 Knowledge Base (Docs)**: A separate, tree-structured documentation center, perfect for building organized knowledge systems.
-   **🎨 Highly Customizable**: Light/dark themes, custom primary colors, background images, glassmorphism effects, and layout adjustments.
-   **🚀 High Performance & Low Cost**: Blazing fast responses powered by the Cloudflare global network, running at virtually zero cost on the free tier.
-   **🔐 Own Your Data**: All your data is stored securely in your own Cloudflare account.

## 🚀 Deployment Guide

### Step 1: Fork Project & Create Cloudflare Resources

1.  **Fork this project** to your own GitHub account and clone it to your local machine.
2.  **Create Cloudflare Resources**: You need to manually create the necessary D1, R2, and KV resources in your Cloudflare Dashboard.

| Resource Type     | Recommended Name  | Binding Variable Name |
| ----------------- | ----------------- | --------------------- |
| **D1 Database**   | `notes-db`        | `DB`                  |
| **KV Namespace**  | `notes-kv`        | `NOTES_KV`            |
| **R2 Bucket**     | `notes-r2-bucket` | `NOTES_R2_BUCKET`     |

1.  **Create D1 Database (`notes-db`)**:
	-   Go to **Workers & Pages** -> **D1** -> **Create database**.
	-   **Important**: After creation, go to the database's console, copy and execute the entire content of the `schema.sql` file.
	-   **Important**: Note down the `database_id` and `database_name`. Open your local `wrangler.toml` file and fill them into the `[[d1_databases]]` section.

2.  **Create KV Namespace (`notes-kv`)**:
	-   Go to **Workers & Pages** -> **KV** -> **Create a namespace**.
	-   **Important**: Note down the `id`. Fill it into the `[[kv_namespaces]]` section of your `wrangler.toml` file.

3.  **Create R2 Bucket (`notes-r2-bucket`)**:
	-   Go to **R2** -> **Create bucket**.
	-   **Important**: Note down the `bucket_name`. Fill it into the `[[r2_buckets]]` section of your `wrangler.toml` file.

4.  **Commit and Push**: Save the changes to your `wrangler.toml` file and push them to your GitHub repository.

### Step 2: Deploy to Cloudflare Workers

1.  In your Cloudflare Dashboard, go to **Workers & Pages** -> **Create application** -> **Select the "Workers" tab**.
2.  Click **"Connect with Git"** and choose your forked repository.
3.  Click **Save and Deploy**.

### Step 3: Configure Environment Variables

After deployment, go to your new Worker's settings to add secrets.

1.  Navigate to your Worker's project -> **Settings** -> **Variables**.
2.  Under **Environment Variables**, add the following variables. Remember to click `Encrypt` for sensitive values.

	| Variable Name           | Description                                  |
	    | ----------------------- | -------------------------------------------- |
	| `USERNAME`              | Your login username                          |
	| `PASSWORD`              | Your login password                          |
	| `TELEGRAM_BOT_TOKEN`    | (Optional) Your Telegram bot's token         |
	| `TELEGRAM_WEBHOOK_SECRET` | (Optional) A long, random string for webhook security |
	| `AUTHORIZED_TELEGRAM_IDS` | (Optional) Your Telegram user ID to authorize |

3.  Trigger a new deployment from the "Deployments" tab to make the variables effective.

## 🔧 Development (Wrangler)

### 1. Local Development (Simulated Environment)

**Initialize local database**:
```bash
npx wrangler d1 execute memos-db --local --file=./schema.sql```

**Start the dev server**:
```bash
npx wrangler dev
```

### 2. Local Development (Connected to Cloud Resources)

This mode connects your local dev server to your actual Cloudflare resources.

1.  **Configure `wrangler.toml`**: Ensure the resource IDs from your Cloudflare Dashboard are filled in this file.
	```toml
	# wrangler.toml
	[[d1_databases]]
	binding = "DB"
	database_name = "notes-db"
	database_id = "YOUR_D1_DATABASE_ID" # Replace

	[[kv_namespaces]]
	binding = "NOTES_KV"
	id = "YOUR_KV_NAMESPACE_ID" # Replace

	[[r2_buckets]]
	binding = "NOTES_R2_BUCKET"
	bucket_name = "notes-r2-bucket"
	```
2.  **Create `.dev.vars` file**: Create this file in the project root for your local secrets.
	```ini
	# .dev.vars (This file is ignored by Git)
	USERNAME="dev_user"
	PASSWORD="dev_password"
	```
3.  **Start the remote-connected dev server**:
	```bash
	npx wrangler dev --remote
	```
