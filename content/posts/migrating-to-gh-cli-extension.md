+++
title = 'Migrating My CLI APP BulkPR to a Github CLI Extension'
date = 2024-12-24T12:00:00+09:00
publishdate = 2024-12-23T12:00:00+09:00
draft = false
+++

Recently, I built a CLI tool called **BulkPR** for my job. It's a simple app, but it works for what I needed. However, one challenge I faced was distribution. My colleagues use different systems-some on Windows, some on MacOS-and I need a way to ensure the tool worked smoothly across all environments.

# The Problem: Cross-Platform Distribution with Docker and Go

At first, I tried to solve the cross-platform issue by using Go's built-in `GOOS` and `GOARCH` environment variables to build different binaries for each platform. This approach worked in theory, but I didn't have access to the devices I needed to test on, so just made a workflow for that.

```yml
name: Build

on:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        os: [linux, windows, darwin]
        arch: [amd64]

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Go
        uses: actions/setup-go@v5
        with:
          go-version: "1.23"

      - name: Build the executable
        run: |
          GOOS=${{ matrix.os }} GOARCH=${{ matrix.arch }} go build -o bulkpr-${{ matrix.os }}-${{ matrix.arch }} main.go

      - name: Upload Build Artifacts
        uses: actions/upload-artifact@v4
        with:
          name: bulkpr-${{ matrix.os }}-${{ matrix.arch }}
          path: bulkpr-${{ matrix.os }}-${{ matrix.arch }}
```

Around the same time, I was working on a open-source project and needed to set up development environment using Docker. This got me thinking: maybe I could use Docker to distribute the app. But there was still one challenge: my CLI uses `gh auth` under thee hood. This made it more complicated since I need to manage Github authentication across environments.

# The Revelation: Github CLI Extensions

Then it hit me: if my tool already use `gh auth`, why not make it a part of the Github CLI ecosystem as an extension? This would simplify the authentication process and, more importantly, it would make distribution much easier.

# Migrating to Github CLI Extension

The migration process turned out to be surprisingly straightforward.

1. **Renaming the Project**  
   Since GitHub CLI extensions need to start with `gh-`, I renamed my project from **bulkpr** to **gh-bulkpr**.
2. **Setting Up the Extension**  
   Next, I created the executable for my app and added it as a GitHub CLI extension using the `gh extension add .` command.
3. **Testing and Deploying**  
   After making suer everything worked, I push it to GitHub. Then, I set up a workflow to automatically distribute the app.

```yml
name: Release
on:
  push:
    tags:
      - "v*"
  workflow_dispatch:
permissions:
  contents: write
  id-token: write
  attestations: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: cli/gh-extension-precompile@v2
        with:
          generate_attestations: true
          go_version_file: go.mod
```

4. **Distribution Made Easy**  
   Now, any of my colleagues can easily install the app

```sh
gh extension add l-lumin/gh-bulkpr
```

With that, the installation process is incredibly simpler.

# Conclusion

At first, migrating my app to a GitHub extension felt like I was losing the original idea. However, it ended up saving me a lot overhead by solving distribution and authentication issues across systems.

# Check Out the Project

You can find [gh-bulkpr](https://github.com/l-lumin/gh-bulkpr) on GitHub.
