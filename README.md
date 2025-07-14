# Bitlab GitHub App Authentication Action

This GitHub Action generates a GitHub App token and configures Git authentication for Bitlab repositories.

## Usage

```yaml
- name: Setup Bitlab Authentication
  uses: bitlabco/modules-action@v1.1
  id: bitlab-auth
  with:
    private-key: ${{ secrets.BITLAB_GITHUB_APP_PRIVATE_KEY }}

# Use the token in subsequent steps
- name: Use token
  run: echo "Token: ${{ steps.bitlab-auth.outputs.token }}"
```

## Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `private-key` | GitHub App private key | Yes |

## Outputs

| Output | Description |
|--------|-------------|
| `token` | Generated GitHub App token |

## What it does

1. Configures Git to use the token for authentication with Bitlab repositories
2. Exports the token as an output for use in other workflow steps

## Setup Requirements

1. Talk to a Bitlab admin to generate for you a private key
2. Install bitlab-terraform app to your GitHub Organization (<https://github.com/apps/bitlab-terraform-modules>)
3. Add the private key as a repository secret: `BITLAB_GITHUB_APP_PRIVATE_KEY`

## Example Workflow

```yaml
name: Deploy with Bitlab Auth
on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Bitlab Authentication
        uses: bitlabco/modules-action@v2
        id: auth
        with:
          private-key: ${{ secrets.BITLAB_GITHUB_APP_PRIVATE_KEY }}
      
      - name: Clone private repo
        run: git clone https://github.com/Bitlabco/private-repo.git
        
      - name: Use token in API call
        run: |
          curl -H "Authorization: Bearer ${{ steps.auth.outputs.token }}" \
               https://api.github.com/user
```
