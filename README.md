# Bintray CLI

This small bash cli aims to help [Bintray](https://bintray.com) management.
The usual process via web browser:

- click on organisation (subject)
- click on repo
- click on package
- **create new version**
- navigate to file upload
- **upload** the file
- **publish** the file, otherwise it will be discarded after a while

## Installation

To use the helper function all you need is *source* a file.

```
curl -Lo /tmp/bintray-functions j.mp/bintray-functions && . /tmp/bintray-functions
```
or if you have `wget`

```
wget -O /tmp/bintray-functions j.mp/bintray-functions && . /tmp/bintray-functions
```

## Authentication

Obviously you will need a Bintray account, we recommend to use GitHub's OAuth2.

Bintray REST API needs basic authentication. Username is your Bintray login id,
while the `API key` can be found at your [profile tab](https://bintray.com/user/edit/tab/apikey)

You have to set the following 2 environment variable:
```
BINTRAY_USER=<username>
BINTRAY_KEY=<api-key>
```

Note: email address wont work for authentication, please use the username, which
shows in the top banner of the webpage when you are logged in.

## Browse your repos

If you want to browse your repos/packages/versions from cli you can use `bint-browse`
```
bint-browse <username>
```

or to browse an organisation you are a member of:

```
bint-browse <organisation>
```

## Upload/Publish a new version

The most typical action is to:

- create a new version
- upload a single file
- publish it

It can be done with a one-line&reg;

```
bint-upload-with-version <USER> <REPO> <PACKAGE> <VERSION> <FILE>
```

example:

```
bint-upload-with-version sequenceiq sequenceiq-bin hadoop-native-64bit 2.4.1 hadoop-native-64-2.4.1.tar
```

## Commands

- bint-browse: browse into **repo**s than into **packages**, then show versions
- bint-repos: list repos for a subject
- bint-packages: list packages for a subject/repo
- bint-package: get a single package's JSON
- bint-versions: list versions of a package
- bint-create-version: as it states
- bint-delete-version: as it states
