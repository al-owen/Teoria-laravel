Por defecto las rutas que se suelen usar de base para generar las operaciones CRUD, son las siguientes:

| Verb       | URI                   | Action   | Route Name     |
|------------|-----------------------|----------|----------------|
| GET        | /movies               | index    | movies.index   |
| GET        | /movies/create        | create   | movies.create  |
| POST       | /movies               | store    | movies.store   |
| GET        | /movies/{movie}       | show     | movies.show    |
| GET        | /movies/{movie}/edit  | edit     | movies.edit    |
| PUT/PATCH  | /movies/{movie}       | update   | movies.update  |
| DELETE     | /movies/{movie}       | destroy  | movies.destroy |
