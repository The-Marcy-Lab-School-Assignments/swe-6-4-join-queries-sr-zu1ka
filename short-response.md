# Short Response: JOIN Queries and Connecting to Postgres

Answer each question below. Write in complete sentences (3–5 per answer).

---

## Question 1

What is the difference between `INNER JOIN` and `LEFT JOIN`? Give a concrete example of when you would use each.

**Your answer:**

---

An INNER JOIN only returns rows where there is a match in both tables, meaning any non-matching records are excluded. A LEFT JOIN returns all rows from the left table and the matching rows from the right table, and if there is no match, the result will still include the left row with NULL values for the right side.

For example, if you have a users table and an orders table, an INNER JOIN would show only users who have placed at least one order. A LEFT JOIN would show all users, even those who haven’t placed any orders yet, with NULL for order details. You would use INNER JOIN when you only care about related data on both sides, and LEFT JOIN when you want to keep all records from one primary table regardless of matches.

## Question 2

Look at this query. What will it return, and why do users with zero bookmarks still appear in the results?

```sql
SELECT users.username, COUNT(bookmarks.bookmark_id) AS total_bookmarks
FROM users
LEFT JOIN bookmarks ON users.user_id = bookmarks.user_id
GROUP BY users.user_id;
```

**Your answer:**

---

This query returns a list of all users along with the number of bookmarks each user has created. Each row shows the user’s username and the total count of matching bookmark_id values from the bookmarks table.

Users with zero bookmarks still appear because the query uses a LEFT JOIN, which keeps all rows from the users table even when there is no matching record in bookmarks. When no match exists, the joined bookmarks columns are NULL.

The COUNT bookmarks.bookmark_id only counts non-NULL values, so for users with no bookmarks, it returns 0 instead of excluding them. This is why those users still appear in the final grouped result.

## Question 3

What is the `pg` library and why can't you write SQL directly in a `.js` file without it? And what is a connection pool?

**Your answer:**

---

The pg library is a Node.js package that lets your JavaScript code connect to and communicate with a PostgreSQL database. It provides the tools needed to send SQL queries from your .js files and receive the results back in a usable format.

You can’t write SQL directly in a .js file without something like pg because JavaScript does not natively understand how to talk to a database. SQL needs to be sent through a database driver that manages the connection, sends the query, and handles the response safely and correctly.

A connection pool is a system that keeps a set of open database connections ready to use. Instead of creating a new connection every time your app needs to run a query, it reuses existing ones from the pool. This makes the application faster and more efficient, especially when handling many requests at once.

## Question 4

What is a parameterized query and what problem does it solve? Rewrite the unsafe query below as a safe parameterized query using `pg`:

```js
// Unsafe — never do this!
pool.query(`SELECT * FROM users WHERE username = '${username}'`);
```

**Your answer:**

---

A parameterized query is a way of writing SQL where you separate the SQL command from the user input. Instead of directly inserting variables into the query string, you use placeholders that are safely replaced by the database driver.

This prevents SQL injection, where someone could insert malicious SQL through user input and change the meaning of your query. It ensures that input is treated strictly as data, not executable code.

Correct version:

```js
pool.query("SELECT * FROM users WHERE username = $1", username);
```
