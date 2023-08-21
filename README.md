# Action-conventional-release

This action create a nice and clean github release leveraging git conventional commit best practices

## Requirement

Your runner should provide a valid nodejs/npm installation.
The fetched repository should have the full history (see [usage](#usage)).

## Usage

See [action.yml](action.yml)

Basic:

```yaml
steps:
- name: Checkout repository
  uses: actions/checkout@v2
  with:
      token: ${{ secrets.GITHUB_TOKEN }}
      # The action requires the ful history of the git repo
      fetch-depth: 0

# See https://github.com/creshpay/action-gpg
- name: Enable GPG
  uses: creshpay/action-gpg@v1
  with:
    gpg-passphrase: "${{ secrets.CI_GPG_PASSPHRASE }}"
    gpg-sign-key: "${{ secrets.CI_GPG_SIGN_KEY }}"
    git-email: "${{ secrets.CI_USER_EMAIL }}"
    git-username: "${{ secrets.CI_USER_NAME }}"

- name: Create release
  uses: creshpay/action-conventional-release@v1
  with:
    custom-tag: "${{ github.event.inputs.tags }}"
    github-access-token: "${{ secrets.GITHUB_TOKEN }}"
    github-access-token-reviewer: "${{ github.token }}"
    base-branch: ${{ github.ref_name }}
```

### Parameters

* **github-access-token** - required

  Access token of a user (we suggest a [machine user](https://docs.github.com/en/developers/overview/managing-deploy-keys#machine-users)) than have permission read:org and repo

* **github-access-token-reviewer** - required

  Access token for a reviewer. You can provide `${{ github.token }}` so Github Actions App user will be the reviewer.

  This aim to be able to merge PR with 1 reviewer requested without having to give admin role for your machine user

* **custom-tag** - optional

  The action allow you to override the generated tag. You have to specify a semver compliant version and the tag should not exists in your repository.

  Example of custom tag: `0.0.5`, `2.0.2`

* **delete-release-branch** - optional - default 0

  Enable or disable release branch deletion. 1 for delete, 0 to keep it

* **base-branch** - optional - default main

  Specify base branche for merge and release

### Limitations

If you require more than 1 reviewer you will have to give your machine user admin permissions on the repo.

## Thanks

This composite action leverage the great works of :

* [standard-version](https://github.com/conventional-changelog/standard-version)
* [extract-changelog-release](https://github.com/nonara/extract-changelog-release)
* [create-release](https://github.com/actions/create-release)

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)
