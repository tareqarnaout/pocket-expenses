# Pocket Expenses

Pocket Expenses is an Android household budget app for tracking expenses, income, transfers, savings, upcoming bills, financial reports, SMS transaction review, home-screen widgets, and biometric app unlock.

## Download

Download and install the latest signed APK from the [Releases page](../../releases/latest).

- Android package: `com.householdledger.expenses`
- Current release: `1.5.1`
- Android may ask you to allow installs from your browser or file manager.

## Set up Supabase

Pocket Expenses connects directly to a Supabase project that you control. A free Supabase project is enough to get started.

### 1. Create a Supabase project

1. Go to [supabase.com/dashboard](https://supabase.com/dashboard) and sign in.
2. Select **New project**.
3. Choose an organization, project name, region, and a strong database password.
4. Wait until the project finishes provisioning.

Keep the database password private. Pocket Expenses does not need it.

### 2. Install the database schema

1. In your Supabase project, open **SQL Editor**.
2. Select **New query**.
3. Open [`supabase/schema.sql`](supabase/schema.sql) from this repository.
4. Copy the entire file into the SQL Editor and select **Run**.
5. Confirm that the query completes successfully.

The schema creates:

- `households`, `members`, and invite-code functions
- `expenses`, `income`, and `transfers`
- default and custom `categories`
- `gold_prices`
- indexes, constraints, helper functions, and onboarding RPCs
- Row Level Security policies that keep private records restricted to their owner

The script is idempotent, so it can be run again when updating an existing database.

### 3. Enable email authentication

1. In Supabase, open **Authentication → Providers**.
2. Make sure **Email** is enabled.
3. Decide whether users must confirm their email address.

If **Confirm email** is enabled, new users must open the confirmation email before they can finish creating or joining a household. For a small private deployment, you may disable confirmation. For a public deployment, keeping email confirmation enabled is recommended.

### 4. Copy the connection details

In Supabase, open **Project Settings → API**. In newer dashboards this information may appear under **Connect** or **API Keys**.

Copy these two values:

- **Project URL**, similar to `https://abcdefgh.supabase.co`
- **Publishable key** or legacy **anon public key**

> Use only the publishable/anon key in Pocket Expenses. Never enter or distribute the `service_role`, secret, database password, or JWT signing secret.

The publishable/anon key is designed for client applications. Data protection is enforced by the Row Level Security policies installed by `schema.sql`.

## Link the Android app

1. Install and open Pocket Expenses.
2. Continue through the welcome screen.
3. On **Database Setup**, paste the Supabase **Project URL**.
4. Paste the **Publishable / Anon key**.
5. Select **Connect Database**. The app reloads using that project.
6. Create an account, then create a household or join one with an invite code.

To change the database later:

1. Open Pocket Expenses.
2. Open **More → Settings**.
3. Select **Database Server**.
4. Enter the new Project URL and publishable/anon key.

Changing servers signs the device out because accounts and financial data belong to the selected Supabase project.

## Verify the connection

After signing in:

1. Add a small test expense.
2. In Supabase, open **Table Editor → expenses** and confirm the row appears.
3. Add income or a transfer and confirm the corresponding table updates.
4. Delete the test entry from the app.

If registration succeeds but household setup fails, rerun the complete `schema.sql` file. The household onboarding functions are required in addition to the tables.

## Troubleshooting

### Invalid URL

Use the complete HTTPS Project URL from Supabase. Remove spaces and do not use the dashboard URL.

### Invalid API key or unauthorized

Use the project’s publishable key or legacy anon key. Keys from a different Supabase project will not work with the selected URL.

### Account created but sign-in does not work

If email confirmation is enabled, confirm the address from the email sent by Supabase. Also check **Authentication → Users** to verify that the account exists.

### Data does not appear

Make sure the entire schema ran successfully. The app requires the Row Level Security policies and onboarding functions included near the end of the SQL file.

### Existing installation is connected to the wrong server

Open **More → Settings → Database Server**, enter the correct details, and reconnect. You will then sign into an account from that Supabase project.

### Live gold prices

The main expense, income, transfer, savings, household, and reporting features work after installing the schema. Automatic live gold-price refresh uses an optional Supabase Edge Function and is not included in this binary-only distribution repository. Without it, the rest of the app remains usable.

## Security notes

- Keep Row Level Security enabled on every financial table.
- Do not add broad policies for the `anon` role.
- Never place a `service_role` key in the app, README, screenshots, QR codes, or support messages.
- Back up the Supabase project and the Android signing key separately.
- Every APK update must be signed with the same certificate to install over an older version.

## Updates and authenticity

Official updates are published on the [Releases page](../../releases). Each version is signed with the same Pocket Expenses signing certificate.

## Source availability

This repository distributes compiled application releases, setup documentation, and the database schema. The Android application source code and signing material are not published here.

Copyright © 2026 Pocket Expenses. All rights reserved.
