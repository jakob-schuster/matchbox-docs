# *matchbox* documentation site

This is the source code for the official documentation site for [matchbox](https://github.com/jakob-schuster/matchbox), a flexible read processor. 

**[Click here to access the documentation](https://jakob-schuster.github.io/matchbox-docs/)**

# Documentation for the documentation site

This site is powered by the Zola static site generator. Any changes can be made by modifying the markdown in the `content` directory.

## Versioning

The documentation is versioned separately from *matchbox*, so we can track changes easily. [SemVer](https://semver.org/) is loosely applied as follows:

1. **MAJOR** version bump when you make a site overhaul (e.g. move from Zola to Quarto, or restructure the whole site)
2. **MINOR** version bump when you make an edit that changes the meaning of existing text (e.g. change an existing function's documentation example, or update a command line parameter name to correspond to an equivalent breaking *matchbox* change)
3. **PATCH** version bump when you make an edit that's strictly additive and does not change the meaning of existing text (e.g. you add a new example, or document a new function)

When you make an update, change the version number in `config.toml` and make a new GitHub release.