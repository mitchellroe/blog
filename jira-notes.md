# Jira

## Viewing issue details with the API

If you want to view all the details of a Jira issue, you can do so using the API.

Navigate to
`https://<your-instance-name>.atlassian.net/rest/api/2/issue/<issue-reference>`.
This will return JSON with all the information about your issue.

## Manually tagging someone based on their ID

`[~accountid:<the-users-account-id>]`
