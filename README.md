# Bintray CLI

This smal bash cli aims to help [Bintray](https://bintray.com) management.
The usual process via web browser:

- click on organisation (subject)
- click on repo
- click on package
- **create new version**
- navigate to file upload
- **upload** the file
- **publish** the file, otherwise it will be discarded after a while

## Bintray cli

To uuse the heper function all you need is *source* a file.

```
curl -o /tmp/bint-functions j.mp/bint-functions && . /tmp/bint-functions
```

## Commands

- bint-browse: browse into **repo**s than into **packages**, then show versions
- bint-repos: list repos for a subject
- bint-packages: list packages for a subject/repo
- bint-package: get a single package's JSON
- bint-versions: list versions of a package
- bint-create-version: as it states
- bint-delete-version: as it states
