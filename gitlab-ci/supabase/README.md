# CI/CD Pipeline for Supabase Functions

## Overview
This GitLab CI/CD pipeline is designed to lint, format, and deploy Supabase functions. It consists of three stages:

1. **Linting (`lint`)** - Checks for code style issues in Deno functions.
2. **Formatting & Additional Linting (`check`)** - Ensures correct formatting and compliance with `deno.json`.
3. **Deployment (`deploy`)** - Deploys the functions to a Supabase project.

## Stages & Jobs

### 1. Linting (`lint-deno`)
- Uses the latest Deno image.
- Runs `deno lint` on the `supabase/functions` directory.
- Fails the pipeline if linting issues are found.

### 2. Formatting & Additional Linting (`check-deno`)
- Uses the latest Deno image.
- Runs:
  - `deno fmt` to format files.
  - `deno fmt --config deno.json --check` to validate formatting.
  - `deno lint --config deno.json` for additional linting rules.
- Runs only on merge requests.

### 3. Deployment (`deploy-function`)
- Runs only when merging into the `main` branch (manual trigger required).
- Uses Docker-in-Docker for Supabase CLI.
- Links the project using `npx supabase link`.
- Finds and deploys all available functions in `supabase/functions`.
- Functions that begin with `_` or are named `functions` are ignored.
- Deploys functions with JWT verification disabled.

## Deployment Rules
- Deployment is **manual** when merging into `main`.
- Functions must be inside `supabase/functions/`.
- If no functions are found, deployment exits successfully without making changes.

## Requirements
- Supabase CLI installed (`npx supabase` used in the script).
- `SUPABASE_PROJECT_ID` environment variable must be set in GitLab CI/CD variables.

## Usage
1. Push code to a feature branch.
2. Open a Merge Request (MR) to trigger lint and format checks.
3. Merge the MR into `main`.
4. Manually trigger the deployment from GitLab UI.
5. Functions are automatically deployed to Supabase.

## Troubleshooting
- Check GitLab CI/CD logs if a job fails.
- Ensure that the `SUPABASE_PROJECT_ID` variable is correctly set.
- If a function fails to deploy, verify its code and retry manually using `npx supabase functions deploy`.
