# Database Testing

## Getting started

- Clone this repo, and run `npm install`
- Run `npm start` to run the server at port 4321
- Run `npm test` to see the tests

## How it works

Once the server is running at localhost:4321, you can access a basic HTML page.
There is a very limited API functionality at /cheeses which returns a list of
the cheeses in the database. A `PUT` request to /cheeses/yourcheesename will add
a new cheese to the database, and a GET request to that same URL will then
subsequently return that cheese from the database.

## Environment variables

The `.env` file for this project has been included. I recommend that you use a
.gitignore to exclude yours in your projects.

You'll notice there are two URLs, one "normal" one and one for tests. The test
database will be erased and rebuilt for every test run, whereas the "normal"
database will remain populated with whatever you put in it, unless you clear it
out yourself. We keep the data separate by giving the databases different names.

## Managing the database connection

The database is initialised once, upon running `app.js`. After this, we can
retrieve the open database connection in each handler using `getDb`

Each query function takes `db` as its first argument. This allows us to pass
through an instance of a database connection, meaning our queries are
independent of the connection itself. In the handlers we pass through the
instance of `db` we get from `getDb`.

In our tests, we can also close the database connection using `closeDb`. This
removes the database assignment on `_db` and invokes a method to close the Mongo
client.

## Writing tests

You'll notice in `tests/queries.test.js` that the first test doesn't actually
test anything. Tape runs tests in series, so it will (very helpfully) wait for
each test to be resolved before moving to the next one. When we're dealing with
async tests this is very important, as we could easily end up in the situation
where one set of database tests is still running while another one starts.

The most crucial part of getting async tests to work nicely is to know that they
run in series, and then to control when each test ends. In the setup test, we
have put `t.end()` within a `.then()` statement in order to run it after each
query has executed.

In the tests that run the query, we have used `t.plan()` to state how many
assertions will be made in our test before it ends. By doing this we make sure
that the test will not end early, and that Tape will not move onto the next test
while the existing database connection is open.
