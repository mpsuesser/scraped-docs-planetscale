---
url: https://planetscale.com/docs/cli/api
title: "Api"
description: ""
access_date: 2026-08-03T19:45:59.089Z
current_date: 2026-08-03T19:45:59.089Z
---

## Getting Started

Make sure to first [set up your PlanetScale developer environment](planetscale-environment-setup.md). Once you’ve installed the `pscale` CLI, you can interact with PlanetScale and manage your databases straight from the command line.

## The api command

Performs authenticated calls against the PlanetScale API and prints the response to stdout. This is a helper for constructing any call to [the PlanetScale API](https://planetscale.com/docs/api/reference). It can invoke any endpoint, whether support in equivalent commands of the CLI has been added or not. For these reasons, the `api` command is useful in cases such as scripting or where you might use `curl` against the API.

**Usage:**

```shellscript
pscale api <ENDPOINT> <FLAG>
```

### Endpoint

The `<ENDPOINT>` should be a path for the v1 PlanetScale API, not including the `v1` prefix. You can use placeholders such as `{org}`, `{db}` and `{branch}` in the endpoint, and these will be replaced with the currently selected organization, and the project’s database and branch.

If invoked from outside a project, `{db}` and `{branch}` yield an empty string. These values can also be specified by the `--org`, `--database`, and `--branch` flags.

Refer to the [API documentation](https://planetscale.com/docs/api/reference) for available endpoints.

### Query parameters

Query parameters, such as for pagination, can be added with the `--query` / `-Q` flag, in `key=value` format.

### HTTP methods

By default, all HTTP requests use the GET method, unless fields for the body are passed via `--field` / `-F`, in which case a POST method is used. If another method is required, use the `--method` / `-X` flag to specify which one to use.

### Headers

Use the `--header` / `-H` flag to specify headers manually. Default headers are already included, such as `User-Agent`, `Content-Type`, `Accept` and `Authorization`.

### Request body

If a body is required for the endpoint you’re targeting, the `--field` / `-F` flag allows you to specify values at specific paths of a JSON document. Repeat the flag to set multiple values. All bodies sent are JSON objects; it isn’t possible to send non-JSON content, or JSON entities of non-object type as the root of the body.

If you wish to pass the content of a file as the body, use the `--input` / `-I` flag. If the body should be read from stdin, use `--input="-"`.

Fields specified with `--field` / `-F` will be updated in the content of `--input` / `-I` if the content is JSON. If not, an error will be returned.

### Available flags

| **Flag** | **Description** |
| --- | --- |
| `-h`, `--help` | View help for the `api` command. |
| `-F`, `--field` key=value | HTTP body to send with the request, in `key=value` format where value is a JSON entity, unless value starts with a `@` in which case, the string after `@` represents a file that will be read. Nested types are represented as `root.depth1.depth2=value`. |
| `-H`, `--header` stringArray | HTTP headers to add to the request. |
| `-I`, `--input` string | HTTP body to send with the request, as a file that will be read and then sent. Use `-` to read from stdin. |
| `-X`, `--method` string | HTTP method to use for the request. Defaults to GET for requests without a body, or POST when a body is specified with `--field` / `-F` or `--input` / `-I`. |
| `-Q`, `--query` key=value | Query to append to the URL path, in `key=value` format. |
| `--org <ORGANIZATION_NAME>` | The organization for the current user. |
| `--database <DATABASE_NAME>` | The database this project is using. |
| `--branch <BRANCH_NAME>` | The branch this project is using. |

### Global flags

| **Command** | **Description** |
| --- | --- |
| `--api-token <TOKEN>` | The API token to use for authenticating against the PlanetScale API. |
| `--api-url <URL>` | The base URL for the PlanetScale API. Default is `https://api.planetscale.com/`. |
| `--config <CONFIG_FILE>` | Config file. Default is `$HOME/.config/planetscale/pscale.yml`. |
| `--debug` | Enable debug mode. |
| `-f`, `--format <FORMAT>` | Show output in a specific format. Possible values: `human` (default), `json`, `csv`. |
| `--no-color` | Disable color output. |
| `--service-token <TOKEN>` | The service token for authenticating. |
| `--service-token-id <TOKEN_ID>` | The service token ID for authenticating. |

## Examples

### To get the current user:

**Command:**

```shellscript
pscale api user
```

**Output:**

The body of the HTTP response.

### To list an organization’s databases:

**Command:**

```shellscript
pscale api organizations/{org}/databases
```

**Output:**

The body of the HTTP response.

### To create a database:

**Command:**

```shellscript
pscale api organizations/{org}/databases -F 'name="my-database"'
```

**Output:**

The body of the HTTP response.

### To get a database:

**Command:**

```shellscript
pscale api organizations/{org}/databases/my-database
```

**Output:**

The body of the HTTP response.

### To delete a database:

**Command:**

```shellscript
pscale api -X DELETE organizations/{org}/databases/my-database
```

**Output:**

The body of the HTTP response.

### To add a note on a database from the content of a file:

**Command:**

```shellscript
pscale api -X PATCH organizations/{org}/databases/{db} -F 'notes=@mynotes.txt'
```

**Output:**

The body of the HTTP response.

### To create a database branch from a file:

**Command:**

```shellscript
pscale api organizations/{org}/databases/{db}/branches --input=spec.json
```

**Output:**

The body of the HTTP response.

### To create a database branch from stdin, override the name:

**Command:**

```shellscript
pscale api organizations/{org}/databases/{db}/branches --input=- -F 'name="my-name"'
```

**Output:**

The body of the HTTP response.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
