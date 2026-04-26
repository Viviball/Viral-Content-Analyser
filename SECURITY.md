# Security

This project handles content-library integrations that may use private Airtable credentials.

## Reporting Issues

If you find a security issue, report it privately to the repository owner instead of opening a public issue.

## Secret Handling

- Never commit real API tokens or session credentials.
- Use environment variables for Airtable configuration.
- Rotate any token that appears in logs, screenshots, commits, pull requests, or issue comments.
- Avoid storing cookies, browser local storage, or platform session tokens in this repository.

## Platform Access

The skill should use normal authenticated browser access when a user is already logged in. It must not bypass privacy gates, scrape private data without permission, or request passwords/cookies from users.
