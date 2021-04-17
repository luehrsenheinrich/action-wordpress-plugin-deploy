# WordPress.org Plugin Deploy

This Action commits the contents of your Git tag to the WordPress.org plugin repository using the same tag name. It can exclude files as defined in either `.distignore` or `.gitattributes`, and moves anything from a `.wordpress-org` subdirectory to the top-level `assets` directory in Subversion (plugin banners, icons, and screenshots).

## Configuration

### Required secrets
* `SVN_USERNAME`
* `SVN_PASSWORD`

[Secrets are set in your repository settings](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). They cannot be viewed once stored.

### Optional environment variables
* `SLUG` - defaults to the repository name, customizable in case your WordPress repository has a different slug or is capitalized differently.
* `VERSION` - defaults to the tag name; do not recommend setting this except for testing purposes.
* `ASSETS_DIR` - defaults to `.wordpress-org`, customizable for other locations of WordPress.org plugin repository-specific assets that belong in the top-level `assets` directory (the one on the same level as `trunk`).
* `PLUGIN_DIR` - defaults to the root folder of the GitHub repository.

## Excluding files from deployment
If there are files or directories to be excluded from deployment, such as tests or editor config files, they can be specified in a `.distignore` file.

### Sample baseline files

#### `.distignore`

**Notes:** `.distignore` is for files to be ignored **only**; it does not currently allow negation like `.gitignore`. This comes from its current expected syntax in WP-CLI's [`wp dist-archive` command](https://github.com/wp-cli/dist-archive-command/). It is possible that this Action will allow for includes via something like a `.distinclude` file in the future, or that WP-CLI itself makes a change that this Action will reflect for consistency. It also will need to contain more than `.gitattributes` because that method **also** respects `.gitignore`.

```
/.wordpress-org
/.git
/.github
/node_modules

.distignore
.gitignore
```


## Example Workflow Files

To get started, you will want to copy the contents of one of these examples into `.github/workflows/deploy.yml` and push that to your repository. You are welcome to name the file something else.

### Deploy on pushing a new tag

```yml
name: Deploy to WordPress.org
on:
  push:
    tags:
    - "*"
jobs:
  tag:
    name: New tag
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Build # Remove or modify this step as needed
      run: |
        npm install
        npm run build
    - name: WordPress Plugin Deploy
      uses: luehrsenheinrich/action-wordpress-plugin-deploy@stable
      env:
        SVN_PASSWORD: ${{ secrets.SVN_PASSWORD }}
        SVN_USERNAME: ${{ secrets.SVN_USERNAME }}
        SLUG: my-super-cool-plugin # optional, remove if GitHub repo name matches SVN slug, including capitalization
```

## Contributing
Want to help? Check out our [contributing guidelines](CONTRIBUTING.md) to get started.

## License

Our GitHub Actions are available for use and remix under the MIT license.
