---
url: https://planetscale.com/docs/cli/planetscale-environment-setup
title: "Planetscale Environment Setup"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

## Table of Contents

## macOS installation

## Linux installation

## Windows installation

## Manual setup (any OS)

## Using the PlanetScale CLI

## Setup overview

### macOS instructions

To install the PlanetScale CLI on macOS, we recommend using Homebrew.

How to install or verify Homebrew is on your computer:

**Installing via Homebrew**

- Run the following command:

```shellscript
brew install pscale
```

Agent automation commands (`agent-guide`, JSON `auth check`, hosted `mcp install`, `pscale sql`) require **`pscale` 0.292.0 or later**. Verify with `pscale agent-guide --format json`.

- To install the MySQL command-line client:

```shellscript
brew install mysql-client
```

- To install the PostgreSQL command-line client:

```shellscript
brew install postgresql@17
```

To upgrade to the latest version:

```shellscript
brew upgrade pscale
```

### Linux instructions

`pscale` is available as downloadable binaries from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page.

Download the.deb or.rpm from the [releases](https://github.com/planetscale/cli/releases/latest) page and install with `sudo dpkg -i` and `sudo rpm -i` respectively.

The MySQL and PostgreSQL command-line clients can be installed via your package manager.

**MySQL client:**

- Debian-based distributions:

```shellscript
sudo apt-get install mysql-client
```

- RPM-based distributions:

```shellscript
sudo yum install community-mysql
```

**PostgreSQL client:**

- Debian-based distributions:

```shellscript
sudo apt-get install postgresql-client
```

- RPM-based distributions:

```shellscript
sudo yum install postgresql
```

### Windows instructions

On Windows, `pscale` is available via [scoop](https://scoop.sh/), and as a downloadable binary from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page:

```shellscript
scoop bucket add pscale https://github.com/planetscale/scoop-bucket.git
scoop install pscale mysql postgresql
```

To upgrade to the latest version:

```shellscript
scoop update pscale
```

**Installation via binary**

Download the latest [Windows release](https://github.com/planetscale/cli/releases/latest) and unzip the `pscale.exe` file into the folder of your choice. Then, run it from PowerShell or whatever terminal you regularly use.

**MySQL client setup:**

The MySQL command-line client is available in the [Windows MySQL Installer](https://dev.mysql.com/doc/refman/8.0/en/windows-installation.html). To launch `pscale shell` you will need to have the `mysql.exe` executable’s directory in your shell’s PATH.

In PowerShell, add that directory to your current shell’s PATH:

```powershell
$env:path += ";C:\Program Files\MySQL\MySQL Server 8.0\bin"
```

**PostgreSQL client setup:**

The PostgreSQL command-line client is available from the [PostgreSQL downloads page](https://www.postgresql.org/download/windows/). After installation, you’ll need to add the PostgreSQL bin directory to your PATH to use `psql`:

```powershell
$env:path += ";C:\Program Files\PostgreSQL\17\bin"
```

## Manual setup (any OS)

If you prefer to manually install the `pscale` binary for your operating system, the following two methods may be used.

### Download the binary

Download the pre-compiled binaries from the [PlanetScale releases](https://github.com/planetscale/cli/releases/latest) page and download the binary for your operating system to the desired location. The binary may be run using the terminal of your choice from that location.

### Install using bin

[bin](https://github.com/marcosnils/bin) is a cross-platform tool to manage binary files. You can install the `pscale` CLI using `bin` with the following command:

```shellscript
bin install https://github.com/planetscale/cli
```

### Install the MySQL or PostgreSQL Client

In either case, the MySQL or PostgreSQL client will need to be installed separately as well.

**MySQL client:** Refer to the [official MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/installing.html) and select the operating system you are working with.

**PostgreSQL client:** Refer to the [official PostgreSQL documentation](https://www.postgresql.org/download/) and select the operating system you are working with.

## Using the PlanetScale CLI

See all available commands by running:

```shellscript
pscale --help
```

Verify that you’re using the latest version:

```shellscript
pscale version
```

You’re all set! Check out our [CLI reference page](../cli.md) to explore all that’s possible with the PlanetScale CLI.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
