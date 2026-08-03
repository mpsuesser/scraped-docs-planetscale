---
url: https://planetscale.com/docs/vitess/tutorials/connect-go-app
title: "Connect Go App"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Connect a Go application to PlanetScale

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

export const VimeoEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://player.vimeo.com/video/${id}?dnt=true`} title={title} className="aspect-video w-full" allow="autoplay; fullscreen; picture-in-picture" />
    </Frame>;
};

<PlatformAvailability current="vitess" />

<VimeoEmbed id="759188218" title="Connect to PlanetScale with Go" />

## Introduction

In this guide, you’ll learn how to connect to a PlanetScale MySQL database with Go by exploring a sample API built using the Gin routing framework.

**Prerequisites:**

* [Go](https://go.dev/doc/install)
* [A PlanetScale account](https://auth.planetscale.com/sign-up)
* [VS Code](https://code.visualstudio.com/download) (optional)
* The [VS Code Rest Client plugin](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) (optional)

<Tip>
  Already have a Go application and just want to connect to PlanetScale? Check out the [Go quick connect repo](https://github.com/planetscale/connection-examples/tree/main/go).
</Tip>

## Create the database

Start in PlanetScale by creating a new database. From the dashboard, click "**New Database**", then "**Create new database**". Name the database `products_db`, select the desired [Plan type](../../planetscale-plans.md), and click "**Create database**".

By default, web console access to production branches is disabled to prevent accidental deletion. From your database's dashboard page, click on the "**Settings**" tab, check the box labelled "**Allow web console access to production branches**", and click "**Save database settings**".

Then, click on the **"Console"** tab, then "**Connect**".

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/connect-go-app/console-2.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=47ce9ab0dec9d1aa926fdbfd3e2e3f3d" alt="The Console tab" width="1529" height="1108" data-path="images/assets/docs/tutorials/connect-go-app/console-2.png" />
</Frame>

Run the following two commands to create a sample table and insert some data:

```sql theme={null}
CREATE TABLE `products` (
	`id` int PRIMARY KEY AUTO_INCREMENT,
	`name` varchar(100) NOT NULL,
	`price` int NOT NULL
);

INSERT INTO `products` (name, price) VALUES
  ('Cyberfreak 2076', 40),
  ('Destination 2: Shining Decline', 20),
  ('Edge Properties 3', 15);
```

Finally, head to the **"Dashboard"** tab and click **"Connect"**.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/connect-go-app/connect-2.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=3dafe11e53a351c65575f30f0a246c84" alt="The location of the Connect button" width="1598" height="806" data-path="images/assets/docs/tutorials/connect-go-app/connect-2.png" />
</Frame>

On the following page, click **"Create password"** to generate a new password for your database. Then click **Go** in the **Select your language or framework** section, and copy the contents of the `.env` file. You'll need it for the next section.

## Run the demo project

Start by opening a terminal on your workstation and clone the sample repository provided.

```bash theme={null}
git clone https://github.com/planetscale/golang-example-gin.git
```

Open the project in VS Code and add a new file in the root of the project named `.env`, Populate the file with the contents taken from the Connect modal in the previous section.

```sql theme={null}
DSN=****************:************@tcp(us-east.connect.psdb.cloud)/products_db?tls=true&interpolateParams=true
```

Now open an integrated terminal in VS Code and run the project using the following commands:

```bash theme={null}
go mod tidy
go run .
```

The terminal should update with the following output.

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/connect-go-app/go-run-output.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d39f619c6cadddb3e9330b6087de3539" alt="The output of the GET test" width="3140" height="1322" data-path="images/assets/docs/tutorials/connect-go-app/go-run-output.png" />
</Frame>

## Exploring the code

Now that the project is running, let’s explore the code to see how everything works. All of the code is stored in `main.go`, with each of the core SQL operations mapped by HTTP method in the `main` function:

| HTTP Method Name | Query Type |
| :--------------- | :--------- |
| get              | SELECT     |
| post             | INSERT     |
| put              | UPDATE     |
| delete           | DELETE     |

```go theme={null}
func main() {
	// Load in the `.env` file
	err := godotenv.Load()
	if err != nil {
		log.Fatal("failed to load env", err)
	}

	// Open a connection to the database
	db, err = sql.Open("mysql", os.Getenv("DSN"))
	if err != nil {
		log.Fatal("failed to open db connection", err)
	}

	// Build router & define routes
	router := gin.Default()
	router.GET("/products", GetProducts)
	router.GET("/products/:productId", GetSingleProduct)
	router.POST("/products", CreateProduct)
	router.PUT("/products/:productId", UpdateProduct)
	router.DELETE("/products/:productId", DeleteProduct)

	// Run the router
	router.Run()
}
```

Open the `tests.http` file, which contains HTTP requests that can be sent to test the API. Running the `get {{hostname}}/products` test is the equivalent of running `SELECT * FROM products` in SQL and returning the results as JSON.

<Warning>
  If you do not wish to use VS Code with the Rest Client plugin, you may use `tests.http` as a reference for your preferred IDE and API testing software.
</Warning>

<Frame>
  <img src="https://mintcdn.com/planetscale-2/Lta43VIYjNTnQ47e/images/assets/docs/tutorials/connect-go-app/go-run-output.png?fit=max&auto=format&n=Lta43VIYjNTnQ47e&q=85&s=d39f619c6cadddb3e9330b6087de3539" alt="The terminal output of the go run command" width="3140" height="1322" data-path="images/assets/docs/tutorials/connect-go-app/go-run-output.png" />
</Frame>

This is the `GetProducts` function defined in `main.go`. Notice how the `query` variable is the `SELECT` statement, which is passed into `db.Query` before being scanned into a slice of `Product` structs.

```go expandable theme={null}
func GetProducts(c *gin.Context) {
	query := "SELECT * FROM products"
	res, err := db.Query(query)
	defer res.Close()
	if err != nil {
		log.Fatal("(GetProducts) db.Query", err)
	}

	products := []Product{}
	for res.Next() {
		var product Product
		err := res.Scan(&product.Id, &product.Name, &product.Price)
		if err != nil {
			log.Fatal("(GetProducts) res.Scan", err)
		}
		products = append(products, product)
	}

	c.JSON(http.StatusOK, products)
}
```

To pass parameters into queries, you may use a `?` as a placeholder for the parameter. For example, `GetSingleProduct` uses a query with a `WHERE` clause that is passed into the `db.QueryRow` function along with the query string.

```go expandable theme={null}
func GetSingleProduct(c *gin.Context) {
	productId := c.Param("productId")
	productId = strings.ReplaceAll(productId, "/", "")
	productIdInt, err := strconv.Atoi(productId)
	if err != nil {
		log.Fatal("(GetSingleProduct) strconv.Atoi", err)
	}

	var product Product
	// `?` is a placeholder for the parameter
	query := `SELECT * FROM products WHERE id = ?`
	// `productIdInt` is passed in with the query
	err = db.QueryRow(query, productIdInt).Scan(&product.Id, &product.Name, &product.Price)
	if err != nil {
		log.Fatal("(GetSingleProduct) db.Exec", err)
	}

	c.JSON(http.StatusOK, product)
}
```

Parameters in queries are populated in the order they are passed into the respective `db` function, as demonstrated in `CreateProduct`.

```go expandable theme={null}
func CreateProduct(c *gin.Context) {
	var newProduct Product
	err := c.BindJSON(&newProduct)
	if err != nil {
		log.Fatal("(CreateProduct) c.BindJSON", err)
	}

	// This query has multiple `?` parameter placeholders
	query := `INSERT INTO products (name, price) VALUES (?, ?)`
	// The `Exec` function takes in a query, as well as the values for
	//     the parameters in the order they are defined
	res, err := db.Exec(query, newProduct.Name, newProduct.Price)
	if err != nil {
		log.Fatal("(CreateProduct) db.Exec", err)
	}
	newProduct.Id, err = res.LastInsertId()
	if err != nil {
		log.Fatal("(CreateProduct) res.LastInsertId", err)
	}

	c.JSON(http.StatusOK, newProduct)
}
```

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
