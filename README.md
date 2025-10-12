# github-data-fetcher

A compact Python utility to fetch a GitHub user's repository data and export it to JSON for further processing 

This repository includes `fetch_github.py`, which implements `GitHubDataFetcher` — a class that wraps several GitHub REST API calls to collect repository metadata, README contents, language statistics, topics, recent commits, and repository file structure.

## What it does

- Lists repositories for a user (paginated)
- Fetches per-repo details: description, stars, forks, watchers, issues, license, branch info
- Downloads README (decoded from base64)
- Retrieves language breakdown and topics
- Fetches recent commits and file tree (falls back to `master` if `main` is absent)
- Optionally includes/excludes forks and can skip forks where the user has not contributed
- Writes all results to `<username>_github_portfolio.json`

## Requirements

- Python 3.9+
- Dependencies: `requests`, `python-dotenv`

Install dependencies:

```bash
pip install requests python-dotenv
```

## Configuration

Create a `.env` file in the repository root (or set environment variables in your shell) with the following keys:

- `GITHUB_ACCESS_TOKEN`: your GitHub personal access token. Recommended scopes: `repo`, `read:user` (depending on whether you access private repos).
- `GITHUB_USERNAME`: the username to fetch.

Example `.env`:

```env
GITHUB_ACCESS_TOKEN=ghp_XXXXXXXXXXXX
GITHUB_USERNAME=your_username
```

## Usage

Run the script directly:

```bash
python fetch_github.py
```

The example usage in `fetch_github.py` demonstrates:

- Reading `GITHUB_ACCESS_TOKEN` and `GITHUB_USERNAME` from environment
- Creating `GitHubDataFetcher` with the token
- Calling `fetch_all_user_repos_data(...)` with configurable options
- Saving results via `save_to_json(...)` to `{username}_github_portfolio.json`

## Script options (adjust in the `if __name__ == "__main__"` block)

- `max_repos`: Maximum number of repositories to fetch (I've set it to 20)
- `include_forks`: Set to `False` to exclude forked repositories
- `skip_inactive_forks`: When True, skip forks where you haven't contributed (checks recent commits for the username)

## Output format

Each repository object in the output JSON contains fields such as:

- `name`, `full_name`, `description`, `url`, `homepage`
- `created_at`, `updated_at`, `pushed_at`
- `stars`, `forks`, `watchers`, `open_issues`
- `language`, `languages` (detailed bytes per language)
- `topics`, `readme`, `license`
- `is_fork`, `is_archived`, `default_branch`
- `file_structure`: list of file paths in the repository
- `recent_commits`: small array with `message`, `date`, `author`