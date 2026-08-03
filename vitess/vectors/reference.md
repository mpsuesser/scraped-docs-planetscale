---
url: https://planetscale.com/docs/vitess/vectors/reference
title: "Reference"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Vector type and index reference

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

<PlatformAvailability current="vitess" />

## Vector type

PlanetScale MySQL provides a `VECTOR(X)` type that can be used to store vectors.
To add a vector column to a table, set it to type `VECTOR(X)` where `X` is the dimension of the vectors to be stored in this column.

### Example

```sql theme={null}
CREATE TABLE t1 (
  id INT PRIMARY KEY auto_increment,
  embedding VECTOR(4)
);
```

## Vector index

PlanetScale MySQL provides a new `VECTOR INDEX` to facilitate fast and scalable approximate nearest neighbor (ANN) search on vector data.

Statements that create a vector index may take optional parameters, which can be specified as JSON key-value pairs, via the `SECONDARY_ENGINE_ATTRIBUTE` variable.
There are four options that can be specified in the JSON. The first two are the following:

* `type`: specifies the algorithm used to build and query the vector index.
  * Supported values: `spann` (more info on the [SPANN algorithm](terminology-and-concepts.md#space-partitioned-approximate-nearest-neighbors-spann))
* `distance` specifies the distance metric that queries will use.
  * Supported values:
    * `dot` for the dot product
    * `cosine` for the cosine of the angle between the two vectors.
    * `l2` or `euclidean` for the length of a line between the ends of the vectors
    * `l2_squared` or `euclidean_squared` for the square of the Euclidean distance. This is the default.

The distance metric specified at index creation time must match the distance metric used at query time, or the index cannot be used, and MySQL will perform a full-table scan instead.

### Quantization options

Vector indexes in PlanetScale MySQL can be configured to use quantization to reduce the size of the vectors stored in the index. Quantization is a compression technique
that reduces the number of bits used to store each vector. This compression is only applied to the vectors as they're stored in the index: the vectors that you insert in
your MySQL table are always stored losslessly.

The default mode of quantization is `bfloat16`, which reduces the memory and disk space used by an index by half with little or no effect on recall. If you need full, 32-bit floating point numbers in a vector index, you must explicitly request `"fixed_quantization":"none"` when creating the index.

The quantization options can be specified alongside the other index options in the `SECONDARY_ENGINE_ATTRIBUTE` variable. The following quantization algorithms are currently supported:

* `product_quantization` (PQ) specifies that product quantization should be used for vector compression.
  * A value of the form `{"dimensions":X}` must be provided, where `X` is the number of dimensions to use for the quantization.
    `X` must be a divisor of the vector's dimension. As an example, vectors with 1536 dimensions can be quantized to 192 dimensions with minimal losses to recall, but overly small values of `X` may end up affecting
    the recall of the index.
  * Product quantization currently only works with the `l2` distance metric.

<Note>
  Product Quantization is a learned quantization algorithm, which means that an existing vector dataset is required to train the quantization model.
  To use Product Quantization on your indexes, begin by inserting a large number of vectors into your table. The more vectors you insert, the better the resulting quantization will be. Then, create the index with the Product Quantization options you want.
  Once the index has been created, you can continuously insert, update, and delete vectors from your table, but beware that if the distribution of the new vectors changes significantly (e.g. because they come from a different source), the
  recall on the index may be affected.
  If you create a Product Quantized index on an empty table, you will need to insert at least 1000 vectors in a single transaction before the index is usable.
</Note>

* `"fixed_quantization" : "float16"` specifies that fixed quantization into [Float16](https://en.wikipedia.org/wiki/Half-precision_floating-point_format) should be used for vector compression. No extra arguments are required.
* `"fixed_quantization" : "bfloat16"` specifies that fixed quantization into [Brain Float 16](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format) should be used for vector compression. No extra arguments are required. This setting is the default.
* `"fixed_quantization" : "none"` specifies that no quantization should occur; the index will use full, 32-bit floats. No extra arguments are required.
* `"fixed_quantization" : "onebit"` specifies that fixed quantization into 1 bit should be used for vector compression. No extra arguments are required.
  * This is a lossy compression method that can be used to store vectors in a very compact form. It is not suitable for all use cases, as it can significantly affect the recall of the index.
  * This quantization method currently only works with the `l2` distance metric, which effectively calculates the Hamming Distance between the vectors.

### Examples

```sql theme={null}
CREATE /*vt+ QUERY_TIMEOUT_MS=0 */
  VECTOR INDEX embedding_index ON t1(embedding);
```

```sql theme={null}
CREATE /*vt+ QUERY_TIMEOUT_MS=0 */
  VECTOR INDEX embedding_index ON t1(embedding)
  SECONDARY_ENGINE_ATTRIBUTE='{"type":"spann", "distance":"cosine"}';
```

```sql theme={null}
CREATE  /*vt+ QUERY_TIMEOUT_MS=0 */
  TABLE t1(
    title VARCHAR(250),
    vec VECTOR(1536),
    VECTOR KEY k(vec) SECONDARY_ENGINE_ATTRIBUTE='{"type":"spann", "distance":"l2", "product_quantization":{"dimensions":96}}'
  );
```

## Vector functions

PlanetScale MySQL includes several new functions for working with vectors.

## `TO_VECTOR(string)` or `STRING_TO_VECTOR(string)`

Converts a text string to a binary vector value. The text string is an array of floating point numbers in JSON format.

### Example

```sql theme={null}
SELECT TO_VECTOR('[1, 2.78, 3.14]');
  -> 0x0000803F85EB3140C3F54840
```

## `FROM_VECTOR(string)` or `VECTOR_TO_STRING(vector)`

Converts a binary vector to a human-readable string.

### Example

```sql theme={null}
SELECT FROM_VECTOR(0x0000803F85EB3140C3F54840);
  -> [1.00000e+00,2.78000e+00,3.14000e+00]
```

## `VECTOR_DIM(string)`

Calculates the dimension of a vector.

### Example

```sql theme={null}
SELECT VECTOR_DIM(TO_VECTOR('[1,2,3]'));
  -> 3
```

## `DISTANCE(vector1, vector2, [metric])`

Calculates the distance between `vector1` and `vector2`.
The optional third parameter specifies which distance metric is to be used: `dot`, `cosine`, `l2` (`euclidean`), or `l2_squared` (`euclidean_squared`).

* `dot` means the dot product.
* `cosine` means the cosine of the angle between the two vectors.
  This is mathematically defined as the dot product divided by the magnitude of the two vectors, which yields a value between `-1` and `1`.
  Some vector database vendors do additional math on the result to normalize the value to be between `0` and `1` or between `0` and `2`.
  Our implementation normalizes the output, and returns a value between `0` and `2`.
  A result of `0` means the vectors are proportional (point in the same direction) and `2` means the vectors are opposite.
* `l2` (or `euclidean`) means the length of a line between the ends of the vectors.
* `l2_squared` (or `euclidean_squared`) is the square of the Euclidean distance

If the distance metric is omitted, it defaults to `dot`.

### Examples

```sql theme={null}
SELECT DISTANCE(TO_VECTOR('[1,2]'), TO_VECTOR('[5,4]'), 'dot');
  -> 13
```

```sql theme={null}
SELECT DISTANCE(TO_VECTOR('[1,2]'), TO_VECTOR('[5,4]'), 'cosine');
  -> 0.9079593845004517
```

```sql theme={null}
SELECT DISTANCE(TO_VECTOR('[1,2]'), TO_VECTOR('[5,4]'), 'l2');
  -> 4.47213595499958
```

```sql theme={null}
SELECT DISTANCE(TO_VECTOR('[1,2]'), TO_VECTOR('[5,4]'), 'l2_squared');
  -> 20
```

```sql theme={null}
SELECT id, price, seller_id
  FROM products
  WHERE price < 20.0
  ORDER BY DISTANCE(TO_VECTOR('[1.2, 3.4, 5.6]'), embedding, 'l2_squared')
  LIMIT 10;
```

## `DISTANCE_DOT(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'dot')`

## `DISTANCE_COSINE(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'cosine')`

## `DISTANCE_L2(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'l2')`

## `DISTANCE_EUCLIDEAN(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'l2')`

## `DISTANCE_L2_SQUARED(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'l2_squared')`

## `DISTANCE_EUCLIDEAN_SQUARED(vector1, vector2)`

Is the same as `DISTANCE(vector1, vector2, 'l2_squared')`

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
