---
title: "GitExtractor"
description: >
  GitExtractor Plugin
---

## Summary

This plugin extracts commits and references from a remote or local git repository. It then saves the data into the database or csv files.

## Steps to make this plugin work

1. Use the Git repo extractor to retrieve data about commits and branches from your repository.
2. Use the GitHub plugin to retrieve data about Github issues and PRs from your repository.
   NOTE: you can run only one issue collection stage as described in the Github Plugin README.
3. Use the [RefDiff](./refdiff.md) plugin to calculate version diff, which will be stored in `refs_commits` table.

Note: If you do not want to collect commit files, you can bypass this step by setting the SKIP_COMMIT_FILES=true in the .env file. This will prevent the plugin from collecting commit file data.

## Sample Request

```
curl --location --request POST 'localhost:8080/pipelines' \
--header 'Content-Type: application/json' \
--data-raw '
{
    "name": "git repo extractor",
    "plan": [
        [
            {
                "Plugin": "gitextractor",
                "Options": {
                    "url": "https://github.com/merico-dev/lake.git",
                    "repoId": "github:GithubRepo:384111310"
                }
            }
        ]
    ]
}
'
```

- `url`: the location of the git repository. It should start with `http`/`https` for a remote git repository and with `/` for a local one.
- `repoId`: column `id` of `repos`.
  Note : For GitHub, to find the repo id run `$("meta[name=octolytics-dimension-repository_id]").getAttribute('content')` in browser console.
- `proxy`: optional, http proxy, e.g. `http://your-proxy-server.com:1080`.
- `user`: optional, for cloning private repository using HTTP/HTTPS
- `password`: optional, for cloning private repository using HTTP/HTTPS
- `privateKey`: optional, for SSH cloning, base64 encoded `PEM` file
- `passphrase`: optional, passphrase for the private key

## Standalone Mode

You call also run this plugin in a standalone mode without any DevLake service running using the following command:

```
cd backend
go run ./plugins/gitextractor -url https://github.com/apache/devlake.git -repoId github:GithubRepo:384111310
```

It uses the database configured in `.env` (`DB_URL`).

For more options (e.g., `skipCommitStat`, `skipCommitFiles`), please read `plugins/gitextractor/gitextractor.go`.

## Development

This plugin reads repositories by running `git` commands against a local clone,
so it requires **git 2.31 or newer** where DevLake runs. libgit2, cgo and a C
toolchain are no longer needed.

### Linux

```
[ubuntu]
apt install git -y
[centos]
yum install git -y
```

### MacOS

```
brew install git
```

<br/><br/><br/>
