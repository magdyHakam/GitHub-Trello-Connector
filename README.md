# Reusable Trello Integration Workflow 🔄

A reusable GitHub Actions workflow that automatically links pull requests to Trello cards by posting comments. This tool works with any language or project hosted on GitHub.

## Features ✨

* **Automatic Linking:** Extracts a Trello card ID from the Git branch name.
* **PR Activity Updates:** Posts a comment on the Trello card when a pull request is opened or updated.
* **Merge Notifications:** Posts a final confirmation comment when a pull request is merged into key branches (e.g., `main`, `stg`).
* **Zero Dependencies:** Uses `curl` to interact directly with the Trello API, requiring no third-party actions.
* **Centralized & Reusable:** Maintain the logic in one place and use it across unlimited repositories.

## How to Use 🚀

Follow these steps to integrate this workflow into any of your project repositories.

### Step 1: Get Trello Credentials 🔑

You need a Trello **API Key** and **User Token**.

1.  **Generate an API Key:**
    * Go to the [Trello Power-Up Admin Portal](https://trello.com/power-ups/admin).
    * Create a new Power-Up (you can name it "GitHub Workflow").
    * In the "API key" tab, generate and copy your API Key.
2.  **Generate a User Token:**
    * Construct the following URL, replacing `YOUR_API_KEY` with the key you just generated:
        ```
        [https://trello.com/1/authorize?expiration=never&name=GitHub+Action&scope=read,write&response_type=token&key=YOUR_API_KEY](https://trello.com/1/authorize?expiration=never&name=GitHub+Action&scope=read,write&response_type=token&key=YOUR_API_KEY)
        ```
    * Open the URL in your browser, click "Allow," and copy the token that appears.

### Step 2: Create a Caller Workflow File 📞

In the repository you want to integrate with (e.g., `my-cool-app`), create a new file at `.github/workflows/trello.yml`. Paste the following content:

```yaml
# .github/workflows/trello.yml

name: Trello Integration

on:
  pull_request:
    types: [opened, edited, closed]

jobs:
  call_trello_workflow:
    # Replace 'Your-GitHub-Org/reusable-workflows' with the path to YOUR reusable repo.
    uses: Your-GitHub-Org/reusable-workflows/.github/workflows/trello-linker.yml@main
    # The 'inherit' keyword automatically passes down the required secrets.
    secrets: inherit
```

### Step 3: Add GitHub Secrets 🤫

In your project repository (`my-cool-app`), add the Trello credentials as repository secrets.

1.  Go to **Settings** > **Secrets and variables** > **Actions**.
2.  Click **New repository secret** for each of the following:
    * `TRELLO_API_KEY`: Your Trello API Key from Step 1.
    * `TRELLO_AUTH_TOKEN`: Your Trello User Token from Step 1.

### Step 4: Configure Permissions 🔐

You must allow your repositories to communicate with each other.

1.  **In your `reusable-workflows` repository:**
    * Go to **Settings > Actions > General**.
    * Under **Access**, select "Accessible from repositories in the 'Your-GitHub-Org' organization."
2.  **In your project (`my-cool-app`) repository:**
    * Go to **Settings > Actions > General**.
    * Ensure "Workflow permissions" is set to "Read and write permissions."

## Developer Workflow 🧑‍💻

To trigger the automation, name your Git branches using the Trello card's 8-character short ID.

**Example:**

For a Trello card with the URL `https://trello.com/c/YLOAfMmd`, your branch name should look like this:

```bash
# git checkout -b feature/YLOAfMmd-new-user-dashboard
```

When you open a pull request from this branch, the workflow will automatically find the ID `YLOAfMmd` and post a comment to the corresponding Trello card.

## Customization ⚙️

You can customize the branches that trigger the "merge" comment by passing an `input` to the workflow.

In your `trello.yml` caller file:

```yaml
jobs:
  call_trello_workflow:
    uses: Your-GitHub-Org/reusable-workflows/.github/workflows/trello-linker.yml@main
    with:
      # This will post a merge comment for main, staging, and production branches.
      target-branches: '["main", "stg", "production"]'
    secrets: inherit
```
