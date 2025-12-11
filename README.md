[![npm version](https://img.shields.io/npm/v/@itrocks/crud-pack?logo=npm)](https://www.npmjs.org/package/@itrocks/crud-pack)
[![npm downloads](https://img.shields.io/npm/dm/@itrocks/crud-pack)](https://www.npmjs.org/package/@itrocks/crud-pack)
[![GitHub](https://img.shields.io/github/last-commit/itrocks-ts/crud-pack?color=2dba4e&label=commit&logo=github)](https://github.com/itrocks-ts/crud-pack)
[![issues](https://img.shields.io/github/issues/itrocks-ts/crud-pack)](https://github.com/itrocks-ts/crud-pack/issues)
[![discord](https://img.shields.io/discord/1314141024020467782?color=7289da&label=discord&logo=discord&logoColor=white)](https://25.re/ditr)

# crud-pack

The it.rocks default CRUD pack.

## Installation

```bash
npm i @itrocks/crud-pack
```

Adding this single dependency pulls in a coherent set of packages that implement the typical CRUD flows,
with default implementations for the most common actions on your domain objects:

- [@itrocks/new](https://github.com/itrocks-ts/new):
  Generic action-based new object form in HTML and JSON,
- [@itrocks/edit](https://github.com/itrocks-ts/edit):
  Generic action-based object edit form in HTML and JSON,
- [@itrocks/delete](https://github.com/itrocks-ts/delete):
  Deletion action handling the button, user confirmation, data source deletion, and visual feedback,
- [@itrocks/list](https://github.com/itrocks-ts/list):
  Generic action-based object list navigation in HTML and JSON,
- [@itrocks/save](https://github.com/itrocks-ts/save):
  Persist object data, processing input from HTML or JSON sources,
- [@itrocks/output](https://github.com/itrocks-ts/output):
  Generic action-based object output in HTML and JSON,
- [@itrocks/summary](https://github.com/itrocks-ts/summary):
  Generic action-based object summary in JSON.

`@itrocks/crud-pack` itself does **not** expose extra runtime APIs.
It is primarily a convenience bundle that:

- groups these packages under a single, versioned dependency,
- provides a standard baseline for building CRUD screens and APIs.

## Usage

You typically use `@itrocks/crud-pack` in an application or library that defines
**CRUD actions and views for a business object**.

Instead of depending on each CRUD package individually, simply install `@itrocks/crud-pack`
and import the building blocks you need from their respective packages.

## Typical use cases

1. **Use the default CRUD pack:** If your app needs default CRUD actions, this pack provides them.
   It is the standard pack you can use in [it.rocks](https://it.rocks) applications,
   but you may replace it with any alternative CRUD implementation pack.
2. **Local override** of default CRUD behaviour for a specific entity directly inside your package.
3. **Global override** of default CRUD behaviour for *all* entities by inheriting and overriding components.

## Examples

### Example 1: Apply the default it.rocks CRUD pack to your application

Declare it as a dependency and the framework will automatically apply the default actions:

**package.json**
```json
{
  "dependencies": {
    "@itrocks/crud-pack": "latest",
    "@itrocks/framework": "latest"
  }
}
```

Then, for this simple `Movie` entity:

**app/movie.ts**
```ts
import { Route } from '@itrocks/route'
import { Store } from '@itrocks/storage'

@Route('/movie')
@Store()
export class Movie
{
  title = ''
  year?: number
}
```

You then automatically get all default CRUD routes, actions, and views:
- [/movie](https://localhost:3000/movie): alias for [/movie/list](https://localhost:3000/movie/list),
- [/movie/list](https://localhost:3000/movie/list): HTML or JSON data for a list of movies,
- [/movie/new](https://localhost:3000/movie/new): form or JSON structure for creating a movie,
- [/movie/1/output](https://localhost:3000/movie/1/output): HTML or JSON output for movie #1,
- [/movie/1/summary](https://localhost:3000/movie/1/summary): HTML or JSON summary,
- [/movie/1/edit](https://localhost:3000/movie/1/edit): edit form,
- [/movie/1/save](https://localhost:3000/movie/1/save): persist data for an existing or new entity,
- [/movie/1/delete](https://localhost:3000/movie/1/delete): delete request with confirmation and feedback.

Output format depends on the
[`Accept` request header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Accept):
- `application/json` → JSON response
- `text/html` → HTML response

### Example 2: Override CRUD for a simple `Movie` entity

**package.json**
```json
{
  "dependencies": {
    "@itrocks/action-pack": "latest",
    "@itrocks/crud-pack": "latest"
  }
}
```

Define the exposed business object:

**app/movie.ts**
```ts
import { Route } from '@itrocks/route'
import { Store } from '@itrocks/storage'

@Route('/movie')
@Store()
export class Movie
{
  title = ''
  year?: number
}
```

Override only the `new` action:

**app/movie/new.ts**
```ts
import { New }   from '@itrocks/new'
import { Movie } from '../movie.js'

@Route('/movie/new')
export class NewMovie extends New<Movie>
{

  async getObject(request: Request<T>)
  {
    const newMovie = await super.getObject(request)
    if (newMovie.title === '') {
      newMovie.title = 'New movie ' + (Math.floor(Math.random() * 90000) + 10000)
    }  
    return newMovie
  }

}
```

### Example 3: Override CRUD behaviour globally

If you want to customise all default CRUD actions for all business objects:

**package.json**
```json
{
  "dependencies": {
    "@itrocks/crud-pack": "latest"
  }
}
```

Then override any default CRUD component using inheritance
through [@itrocks/compose](https://github.com/itrocks-ts/compose).
