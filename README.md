# Vercel Action

Forked from awesome [amondnet/vercel-action](https://github.com/amondnet/vercel-action).

This repository is maintained just for myself. Use at your own risk.

## Inputs

| Name              | Required | Default | Description                                                                                       |
|-------------------|:--------:|---------|---------------------------------------------------------------------------------------------------|
| vercel-token        |    [x]   |         | Vercel token.                                                                                   |
| github-comment    |    [ ]   | true    | if you don't want to comment on pull request.                                                     |
| github-token      |    [ ]   |         | if you want to comment on pull request.                                                           |
| vercel-args          |    [ ]   |         | This is optional args for `vercel` cli. Example: `--prod`                                            |
| working-directory |    [ ]   |         | the working directory                                                                             |
| vercel-project-id    |    [x]   |         | ❗Vercel CLI 17+,The `name` property in vercel.json is deprecated (https://zeit.ink/5F)                  |
| vercel-org-id        |    [x]   |         | ❗Vercel CLI 17+,The `name` property in vercel.json is deprecated (https://zeit.ink/5F)                  |


## Outputs

### `preview-url`

The url of deployment preview.

### `preview-name`

The name of deployment name.

## Example Usage

### Disable Vercel for GitHub

> The Vercel for GitHub integration automatically deploys your GitHub projects with Vercel, providing Preview Deployment URLs, and automatic Custom Domain updates.
[link](https://vercel.com/docs/v2/git-integrations)

We would like to to use `github actions` for build and deploy instead of `Vercel`. 

Set `github.enabled: false` in vercel.json

```json
{
  "version": 2,
  "public": false,
  "github": {
    "enabled": false
  },
  "builds": [
    { "src": "./public/**", "use": "@now/static" }
  ],
  "routes": [
    { "src": "/(.*)", "dest": "public/$1" }
  ]
}

```
When set to false, `Vercel for GitHub` will not deploy the given project regardless of the GitHub app being installed.


`vercel.json` Example:
```json
{
  "version": 2,
  "scope": "amond",
  "public": false,
  "github": {
    "enabled": false
  },
  "builds": [
    { "src": "./public/**", "use": "@now/static" }
  ],
  "routes": [
    { "src": "/(.*)", "dest": "public/$1" }
  ]
}
```

### Project Linking

You should link a project via [Vercel CLI](https://vercel.com/download) in locally.

When running `vercel` in a directory for the first time, [Vercel CLI](https://vercel.com/download) needs to know which scope and Project you want to deploy your directory to. You can choose to either link an existing project or to create a new one.

> NOTE: Project linking requires at least version 17 of [Vercel CLI](https://vercel.com/download). If you have an earlier version, please [update](https://vercel.com/guides/updating-vercel-cli) to the latest version.

```bash
vercel
```

```bash
? Set up and deploy “~/web/my-lovely-project”? [Y/n] y
? Which scope do you want to deploy to? My Awesome Team
? Link to existing project? [y/N] y
? What’s the name of your existing project? my-lovely-project
🔗 Linked to awesome-team/my-lovely-project (created .vercel and added it to .gitignore)
```

Once set up, a new `.vercel` directory will be added to your directory. The `.vercel` directory contains both the organization(`vercel-org-id`) and project(`vercel-project-id`) id of your project.

```json
{"orgId":"example_org_id","projectId":"example_project_id"}
```

You can save both values in the secrets setting in your repository. Read the [Official documentation](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) if you want further info on how secrets work on Github.

### Github Actions

* This is a complete `.github/workflow/deploy.yml` example.

Set the `vercel-project-id` and `vercel-org-id` you found above.

```yaml
name: deploy website
on: [pull_request]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: amondnet/vercel-action@v19
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }} # Required
          github-token: ${{ secrets.GITHUB_TOKEN }} #Optional 
          vercel-args: '--prod' #Optional
          vercel-org-id: ${{ secrets.ORG_ID}}  #Required
          vercel-project-id: ${{ secrets.PROJECT_ID}} #Required 
          working-directory: ./sub-directory
```

### Basic Auth Example

How to add Basic Authentication to a Vercel deployment

See [.github/workflow/example-express-basic-auth.yml](.github/workflow/example-express-basic-auth.yml)

[source code](https://github.com/amondnet/vercel-action/tree/master/example/express-basic-auth)
