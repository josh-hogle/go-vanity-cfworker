<div align="center">
  <img width="128" src="./logo.png" alt="Cloudflare Worker logo" />
  <h1>go-vanity-cfworker</h1>
  <p>Go vanity URL import server using Cloudflare Workers</p>
  <hr />
  <br />
  <a href="#"><img src="https://img.shields.io/badge/stability-alpha-pink?style=for-the-badge" /></a>
  <a href="https://en.wikipedia.org/wiki/MIT_License" target="_blank"><img src="https://img.shields.io/badge/license-MIT-maroon?style=for-the-badge" /></a>
  <a href="#"><img src="https://img.shields.io/badge/support-community-purple?style=for-the-badge" /></a>
</div>
<br />
<hr />
<br />

<!-- omit in toc -->

## Table of Contents

- [Table of Contents](#table-of-contents)
- [👁️ Overview](#️-overview)
- [✅ Requirements](#-requirements)
- [📦 Setup and Deployment](#-setup-and-deployment)
- [⚙️ Vanity Package Configuration](#️-vanity-package-configuration)
- [📃 License](#-license)
- [❓ Questions, Issues and Feature Requests](#-questions-issues-and-feature-requests)

## 👁️ Overview

`go-vanity-cfworker` is a Cloudflare Workers service that provides vanity URLs for Golang's `import` directive. It utilizes both Cloudflare Workers and Cloudflare Workers KV to map vanity package names to actual source repositories. This makes it very easy to move your actual source packages around or rename source repositories without the need for updating your actual Go code.

## ✅ Requirements

You will need the following software installed on your system:

- Node.js 16.x or later
- Yarn package manager
- Cloudflare's `wrangler` CLI utility
- The `jq` utility (optional - used for initial setup)
- `bash` shell (optional - used for initial setup)

## 📦 Setup and Deployment

In order to set up your vanity "server", you'll need to perform the following steps:

0. In order for wrangler to work you need to autorize it with you cloudflare account. This can be done using the command `yarn exec wrangler login`. 
1. After cloning the repository, run `yarn setup`. This will install the required Node packages and prepare the Cloudflare Workers KV namespaces for you.

   - You can choose any name you'd like for the service.
   - Cloudflare Worker KV stores can **only** contain alphanumeric and underscore (**\_**) characters.
   - If you already have an existing service, you can just use it by supplying its name here.
   - The KV store will be named `SERVICE_NAME-STORE_NAME` where `SERVICE_NAME` is the name you choose for the service and `STORE_NAME` is the name you choose for the KV store.
   - A second KV store will also be created with `_PREVIEW` appended to it. This KV store can be used for testing and development purposes.
   - Custom domains can be mapped by entering one each when prompted. If no custom domains are required or if you are done entering custom names, simply leave the entry blank.
   - The setup script can be re-run if changes are required.

2. When you are ready to deploy the worker, simply run `yarn deploy` to publish the settings to Cloudflare.
3. You can populate the `_PREVIEW` KV store in Cloudflare (see [Configuration](#configuration) below) with test values and then run `yarn dev` to test locally.
4. Populate the production KV store in Cloudflare with your vanity package names and source configurations (see [Configuration](#configuration) below).

If you wish to make changes to your configuration, you can either re-run the `yarn setup` command or manually edit the `wrangler.toml` file. See the [Cloudflare Wrangler](https://developers.cloudflare.com/workers/wrangler/configuration/) docs for more details.

## ⚙️ Vanity Package Configuration

Vanity package names are stored in the Cloudflare Workers KV associated with your Cloudflare Workers service. All keys and values have an implied `https://` protocol associated with them so you do **not** have to specify this in any configuration settings.

Each key in the KV store represents the vanity package name. For example, if you want to point `go.joshhogle.dev/errorx` to `github.com/josh-hogle/go-errorx`, the key should be `go.joshhogle.dev/errorx`.

Key values can be in one of two formats: JSON or plaintext.

If you choose to use JSON for the value, it should be in the following format:

```
{
  "source": "path_to_source_repo",
  "vcs": "git",
  "defaultBranch": "main"
}
```

`source` is required and represents the source path to the repository (eg: `github.com/josh-hogle/go-errorx`).

`vcs` is optional and is by default set to `git`. If your source repository uses a different version control system, you may change it here.

`defaultBranch` is optional is is by default set to `main`. If the default branch of your repository is different (eg: `master`), you may change it here.

If you use plaintext, the value should simply be the path to the source repository. In the example above, the value would be `github.com/josh-hogle/go-errorx`. Values for `vcs` and `defaultBranch` in this case are set to the default values.

**NOTE:**

Keep in mind that Cloudflare Workers KV values are _eventually_ consistent, so it may take a few moments for values to be reflected when they are created or updated.

## 📃 License

This module is distributed under the MIT License.

## ❓ Questions, Issues and Feature Requests

If you have questions about this project, find a bug or wish to submit a feature request, please [submit an issue](https://github.com/josh-hogle/go-vanity-cfworker/issues).
