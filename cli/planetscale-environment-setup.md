---
url: https://planetscale.com/docs/cli/planetscale-environment-setup
title: "Planetscale Environment Setup"
description: ""
access_date: 2026-10-30T18:26:51.105Z
current_date: 2026-10-30T18:26:51.105Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale environment set up

## Table of Contents

> [card] [macOS installation](#macos-instructions)

> [card] [Linux installation](#linux-instructions)

> [card] [Windows installation](#windows-instructions)

> [card] [Manual setup (any OS)](#manual-setup-any-os)

> [card] [Using the PlanetScale CLI](#using-the-planetscale-cli)

## Setup overview

### macOS instructions

To install the PlanetScale CLI on macOS, we recommend using Homebrew.

How to install or verify Homebrew is on your computer:

**Step 1**

Open Terminal.

**Step 2**

Check if you have Homebrew installed by running the following command:

```bash theme={null}
brew -v
```

**Step 3**

If you don't see "Homebrew" and a version number, then download and [install Homebrew](https://brew.sh/).

**Step 4**

Once you've installed Homebrew, repeat *Step 2* to verify.

**Installing via Homebrew**

* Run the following command:

```bash theme={null}
brew install pscale
```

Agent automation commands (`agent-guide`, JSON `auth check`, hosted `mcp install`, `pscale sql`) require **`pscale` 0.292.0 or later**. Verify with `pscale agent-guide --format json`.

* To install the MySQL command-line client:

```bash theme={null}
brew install mysql-client
```

* To install the PostgreSQL command-line client:

```bash theme={null}
brew install postgresql@17
```

To upgrade to the latest version:

```bash theme={null}
brew upgrade pscale
```

### Linux instructions

`pscale` is available as downloadable binaries from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page.

Download the .deb or .rpm from the [releases](https://github.com/planetscale/cli/releases/latest) page and install with `sudo dpkg -i` and `sudo rpm -i` respectively.

The MySQL and PostgreSQL command-line clients can be installed via your package manager.

**MySQL client:**

* Debian-based distributions:

```bash theme={null}
sudo apt-get install mysql-client
```

* RPM-based distributions:

```bash theme={null}
sudo yum install community-mysql
```

**PostgreSQL client:**

* Debian-based distributions:

```bash theme={null}
sudo apt-get install postgresql-client
```

* RPM-based distributions:

```bash theme={null}
sudo yum install postgresql
```

### Windows instructions

On Windows, `pscale` is available via [scoop](https://scoop.sh/), and as a downloadable binary from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page:

```bash theme={null}
scoop bucket add pscale https://github.com/planetscale/scoop-bucket.git
scoop install pscale mysql postgresql
```

To upgrade to the latest version:

```bash theme={null}
scoop update pscale
```

**Installation via binary**

Download the latest [Windows release](https://github.com/planetscale/cli/releases/latest) and unzip the `pscale.exe` file into the folder of your choice. Then, run it from PowerShell or whatever terminal you regularly use.

**MySQL client setup:**

The MySQL command-line client is available in the [Windows MySQL Installer](https://dev.mysql.com/doc/refman/8.0/en/windows-installation.html). To launch `pscale shell` you will need to have the `mysql.exe` executable's directory in your shell's PATH.

In PowerShell, add that directory to your current shell's PATH:

```powershell theme={null}
$env:path += ";C:\Program Files\MySQL\MySQL Server 8.0\bin"
```

**PostgreSQL client setup:**

The PostgreSQL command-line client is available from the [PostgreSQL downloads page](https://www.postgresql.org/download/windows/). After installation, you'll need to add the PostgreSQL bin directory to your PATH to use `psql`:

```powershell theme={null}
$env:path += ";C:\Program Files\PostgreSQL\17\bin"
```

## Manual setup (any OS)

If you prefer to manually install the `pscale` binary for your operating system, the following two methods may be used.

### Download the binary

Download the pre-compiled binaries from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page and download the binary for your operating system to the desired location. The binary may be run using the terminal of your choice from that location.

### Install using `bin`

[bin](https://github.com/marcosnils/bin) is a cross-platform tool to manage binary files. You can install the `pscale` CLI using `bin` with the following command:

```bash theme={null}
bin install https://github.com/planetscale/cli
```

### Install the MySQL or PostgreSQL Client

In either case, the MySQL or PostgreSQL client will need to be installed separately as well.

**MySQL client:** Refer to the [official MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/installing.html) and select the operating system you are working with.

**PostgreSQL client:** Refer to the [official PostgreSQL documentation](https://www.postgresql.org/download/) and select the operating system you are working with.

## Using the PlanetScale CLI

Authenticate with your PlanetScale account:

```bash theme={null}
pscale auth login
```

This opens a browser window to complete sign-in. See the [`auth` command reference](auth.md) for details.

See all available commands by running:

```bash theme={null}
pscale --help
```

Verify that you're using the latest version:

```bash theme={null}
pscale version
```

You're all set! Check out our [CLI reference page](https://planetscale.com/docs/cli.md) to explore all that's possible with the PlanetScale CLI.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.


This documentation is built and hosted on [Mintlify](https://mintlify.com), a developer documentation platform.
