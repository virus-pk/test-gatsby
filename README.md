# Gatsby + Cosmic

![gatsby-blog-cosmicjs](https://cdn.cosmicjs.com/fe5196f0-42c2-11ea-8d10-df553329919a-gatsby-blog-cosmic.png "The index page of the starter blog")

> This repo contains an example blog website that is built with [Gatsby](https://www.gatsbyjs.org/), and [Cosmic](https://www.cosmicjs.com).

> [See live demo hosted on Netlify](https://gatsby-blog-cosmicjs.netlify.com/)

> Uses the [Cosmic Gatsby Source Plugin](https://www.npmjs.com/package/gatsby-source-cosmicjs)

## Prerequisites

- Node (I recommend using v8.2.0 or higher)
- [Gatsby CLI](https://www.gatsbyjs.org/docs/)

## Install

``` bash
# Make sure that you have the Gatsby CLI program installed
npm install --global gatsby-cli

# run from your CLI
gatsby new gatsby-example-blog https://github.com/cosmicjs/gatsby-blog-cosmicjs
```
In `gatsby-config.js` you need to add configuration for your Cosmic Bucket

``` javascript
{
  resolve: 'gatsby-source-cosmicjs',
  options: {
    bucketSlug: '', /* Find this in Your Bucket > Settings > Basic Settings after logging in at https://app.cosmicjs.com/login */
    objectTypes: ['posts', 'settings'], /* Object types to fetch */
    apiAccess: {
      read_key: '', /* Find this in Your Bucket > Settings > API Access after logging in at https://app.cosmicjs.com/login */
    },
    localMedia: true /* Optional. If you want to enable local image for Gatsby Image */
  }
},
```

Then

``` bash
# Then you can run it by
cd gatsby-example-blog
npm run develop
```

## Deploy to Netlify
You can deploy to Netlify in a few steps using their CLI. Run the following commands from the root folder.
```
npm i -g netlify-cli
netlify deploy
```
## Security Remediation (KAN-1): minimatch CVE-2026-26996

This project had a Dependabot alert for a Regular Expression DoS in `minimatch` (CVE-2026-26996). We remediated by forcing a patched version across the entire dependency graph using npm `overrides`.

### Implementation

Add the following to `package.json`:

```json
{
  "overrides": {
    "minimatch": "3.1.3",
    "glob": { "minimatch": "3.1.3" },
    "ignore-walk": { "minimatch": "3.1.3" }
  }
}
```

Notes:
- Parent-targeted overrides for `glob` and `ignore-walk` ensure legacy transitive paths (e.g., via `rimraf` and `npm-packlist`/`node-pre-gyp`) also resolve to the patched `minimatch`.
- Use npm v8+ so `overrides` is supported.

### Verification

Run:

```bash
npm install
npm dedupe
npm ls minimatch
npm audit
```

Expected:
- All entries show `minimatch@3.1.3`.
- The minimatch advisory is cleared in `npm audit`.

### Build considerations

On macOS arm64, legacy Gatsby stacks may require native binaries for `sharp`. If local builds fail:
- Reinstall without `--ignore-scripts` so prebuilt binaries can be fetched, or
- Upgrade `sharp`/`gatsby-plugin-sharp` to versions compatible with your Node/macOS, or
- Install system `libvips` as required by `sharp`.

### Governance

Work tracked under Jira key `KAN-1`. Branch name used: `KAN-1-fix-minimatch-redos`. Commits and PRs should follow the Jira-first workflow.
