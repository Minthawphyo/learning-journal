# Learning Journal — PHP Fundamentals

**Project:** Forked [The Advice Shop](https://github.com/lindsaymarkward/theadviceshop)

## Why I forked instead of starting from scratch

For this task I forked The Advice Shop, a sample PHP site used in JCU's web design units, instead of writing a brand-new site. I wanted to work with existing code rather than a blank page, since that's closer to what real development work looks like.

## Getting it running

The repo hadn't been touched since 2016, so my first job was just getting it to run at all. PHP wasn't installed on my machine, so I installed PHP 8.3 and had to manually enable the `pdo_sqlite` extension in `php.ini` before the site's SQLite-backed quotes page would work. A small reminder that "clone and run" almost never actually means clone and run.

## What I found

Reading through the original code, the "login system" mentioned in the README turned out to be a joke — `login.php` just took whatever username you typed and stuck it straight in `$_SESSION`, with no password check at all. `quote.php` also built its SQL query by concatenating `$_GET['id']` directly into the string, and `process.php` echoed a user-supplied name back into the page without escaping it. So beyond the "make a proper login system" enhancement the task suggested, there were two real vulnerabilities — SQL injection and reflected XSS — sitting in a site that's handed to students as an example to learn from.

## What I built

- **A real login system** — a `users` table, `password_hash()` / `password_verify()` for storing and checking passwords, and a `register.php` page so new accounts can actually be created instead of just seeded.
- **Fixed the SQL injection** in `quote.php` by switching to a prepared statement, and the **XSS** in `process.php` with `htmlspecialchars()`.
- **Pulled shared logic into `functions.php`** (authentication, star-rating rendering, page-name sanitising) so it's reusable across pages instead of copy-pasted.
- **Practiced the different loop types with original content**: a `while` loop that drains an array of quick-tip strings on the homepage, a `for` loop that renders star ratings on the advisors page, and converted the hardcoded advisor list and nav links into PHP arrays looped with `foreach`.
- **Gave the site a visual pass** — card-based layout, consistent buttons/badges, and a responsive breakpoint for mobile (the original had no viewport meta tag at all, so it would've rendered zoomed out and tiny on a phone).

## Skills demonstrated

| Skill | Where |
|---|---|
| `echo` (varied HTML) | every page |
| `if` / `else` | login errors, rating badges, current-page nav highlighting |
| `for` / `while` / `foreach` (array) | star ratings / quick tips / advisor + nav arrays |
| Functions with parameters | `authenticateUser()`, `createUser()`, `renderStars()` |
| `include` | `inc_header.php` / `inc_nav.php` / `inc_footer.php` shared across all 7 pages |

## What I'd do differently next time

I'd add CSRF protection to the login/register forms — right now they're plain POSTs with no token, which is fine for a course demo but not something I'd ship in anything real. I'd also like to explore rate-limiting failed login attempts, since right now the login form will happily let you guess passwords as many times as you like.
