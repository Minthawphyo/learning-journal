# Learning Journal — Prac 4: Local Development & Deployment

**Date:** 17 June 2026
**Project:** FactoryBridge — a PHP/MySQL marketplace connecting buyers with verified Chinese manufacturers

## What I set out to do

For this prac, the goal was to get a dynamic, database-backed version of my site running on localhost rather than only on shared hosting. My FactoryBridge project is a custom PHP + MySQL application (not a CMS like WordPress or Joomla), so I needed a local environment that could run PHP, serve it over HTTP, and connect it to a real MySQL database — ideally in a way that mirrors how it will eventually run in production.

I had three different builds sitting in my project folder from earlier experimentation: a WordPress theme, a Joomla template, and my custom PHP/MySQL build. I made a deliberate decision to commit to the custom PHP/MySQL build going forward, since it already had the most functionality built out (authentication, RFQs, saved factories, reviews) and didn't depend on a separate CMS core being installed. I left the WordPress and Joomla folders untouched in the project for reference but excluded them from anything I actually deploy.

## Choosing Docker over XAMPP

I considered using XAMPP, which is the simplest way to get Apache + PHP + MySQL running locally with almost no configuration. I chose Docker instead, mainly because it more closely mirrors a real deployment workflow — the same containerised approach that's used when deploying to cloud platforms — and because it keeps my local environment completely isolated from anything else installed on my machine. If something breaks, I can just delete the containers and rebuild from scratch instead of troubleshooting a system-wide PHP/MySQL install.

## Setting up the environment

I wrote a `Dockerfile` to build a custom PHP + Apache image:

```dockerfile
FROM php:8.2-apache

RUN docker-php-ext-install pdo pdo_mysql \
    && a2enmod rewrite headers

RUN printf '<Directory /var/www/html>\n    AllowOverride All\n</Directory>\n' \
    > /etc/apache2/conf-available/htaccess-allow.conf \
    && a2enconf htaccess-allow

WORKDIR /var/www/html
```

The base `php:8.2-apache` image doesn't include the MySQL driver by default, so I had to explicitly install `pdo_mysql` — this is the extension my `config.php` uses to open a database connection via PDO. I also enabled `mod_rewrite`, because my `.htaccess` file depends on it to route clean URLs like `/api/factories` through to `api.php`, and `mod_headers`, which is needed for the security headers I added earlier (`X-Frame-Options`, `X-Content-Type-Options`, etc.). One thing that tripped me up initially is that Apache's default config in this image has `AllowOverride None`, which silently ignores `.htaccess` files entirely — I had to add a small Apache config snippet to explicitly allow overrides for my project directory, otherwise none of my rewrite rules or security headers would have taken effect, with no error message to explain why.

Alongside the Dockerfile, I wrote a `docker-compose.yml` to orchestrate two services — the web app and the database — plus later a third for database administration:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
    volumes:
      - ./:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: factorybridge
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin:latest
    ports:
      - "8081:80"
    environment:
      PMA_HOST: db
      PMA_USER: root
      PMA_PASSWORD: rootpass
    depends_on:
      - db

volumes:
  db_data:
```

The `web` service mounts my entire project folder into the container as a volume (`./:/var/www/html`), which means I can edit any PHP or HTML file on my actual computer and see the change immediately just by refreshing the browser — no rebuild needed, since the container is just reading the live files off my disk. The `db` service uses the official MySQL 8 image and persists its data in a named volume (`db_data`), so my data survives container restarts. I added `phpmyadmin` afterwards as a convenience — it gives me a web-based GUI at `localhost:8081` to browse tables and run SQL queries without needing the MySQL command line, which made debugging much faster later on.

## How configuration and secrets are handled

Earlier in this project I refactored credential handling so nothing sensitive is hardcoded in PHP files. `config.php` now reads a `.env` file at runtime:

```php
$envFile = __DIR__ . '/.env';
if (file_exists($envFile)) {
    $lines = file($envFile, FILE_IGNORE_NEW_LINES | FILE_SKIP_EMPTY_LINES);
    foreach ($lines as $line) {
        if (str_starts_with(trim($line), '#') || !str_contains($line, '=')) continue;
        [$key, $val] = explode('=', $line, 2);
        $_ENV[trim($key)] = trim($val);
    }
}
```

This was useful for the Docker setup specifically: my local `.env` points the app at the `db` container (`DB_HOST=db`, since Docker Compose resolves service names as hostnames on its internal network) with throwaway credentials, while my real InfinityFree production credentials live in a separate `.env.production` file that I never touch when working locally. Both files are excluded from version control via `.gitignore`, so secrets never end up in Git history regardless of which environment I'm working in.

## Making `.htaccess` work in both environments

My `.htaccess` file forces HTTPS and routes `/api/*` requests to `api.php`:

```apache
RewriteCond %{HTTP_HOST} !^localhost
RewriteCond %{HTTP_HOST} !^127\.0\.0\.1
RewriteCond %{HTTPS} off
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_URI} ^/api/
RewriteRule ^api/(.*)$ api.php [QSA,L]
```

The first rule needed special handling for local development: without the `HTTP_HOST` conditions, every request to `localhost:8080` would get redirected to `https://localhost:8080`, which doesn't exist locally (there's no SSL certificate in my dev container), so the site would just fail to load. Adding the exception for `localhost` and `127.0.0.1` means the same `.htaccess` file works unmodified in both my local Docker environment and on production — I don't have to remember to swap files before deploying.

## Bugs I found and fixed while testing locally

Running the site locally surfaced a few real bugs that weren't obvious before:

**Password fields were unclickable.** When testing the login and registration forms, I found I couldn't type into the password field at all — clicking it just toggled the show/hide eye icon instead of focusing the input. Digging into the CSS, I found the cause was a specificity conflict: a generic rule `.input-icon i { left: 12px; ... }` was unintentionally applying to *every* icon inside the input wrapper, including the eye icon, which already had `right: 12px` set by its own `.password-toggle` rule. With both `left` and `right` set on an absolutely-positioned element with no fixed width, the browser stretched the icon to span the *entire* width of the input box, turning it into an invisible click-trap over the whole field. I fixed this by excluding the toggle icon from the generic rule (`:not(.password-toggle)`) and giving it an explicit `left: auto` so it only anchors to the right edge as intended.

**Email verification silently blocked every new signup locally.** I added an email verification step earlier in the project so new accounts can't log in until they click a link sent to their email. Locally, PHP's `mail()` function has no actual mail server behind it, so the verification email is never delivered — meaning every account I registered for testing was permanently stuck unverified. I worked around this by using phpMyAdmin to manually flip the `email_verified` column to `1` for my test accounts, which let me confirm the rest of the login flow worked correctly. In a real deployment this wouldn't be an issue since the hosting provider's mail server would actually deliver the email.

**Empty factories list after a database reset.** At one point my factories page showed no categories and no results, and the site behaved as if I'd been logged out. The API was still responding with `200 OK`, just with empty data — which told me the issue wasn't a broken endpoint but an empty database. I'd run `docker compose down -v`, which deletes the named volume holding all MySQL data, including my seeded factories and user accounts. Since my session referenced a user ID that no longer existed, the app correctly treated me as logged out. Re-running `setup.php?key=setup2026` reseeded the 15 factory records and recreated the admin account, which resolved it. This taught me the difference between `docker compose down` (stops containers, keeps data) and `docker compose down -v` (also wipes data) — a distinction that matters a lot once there's real data to lose.

## What I'd do differently / next steps

If I were setting this up again, I'd add a local mail-catching tool like MailHog to the Docker Compose stack so I could actually see verification and password-reset emails during testing instead of manually editing the database each time. I'd also consider adding a database seed/reset script that's safer to run repeatedly, since right now `setup.php` is meant to be deleted after first use in production, which makes "reset my local dev data" slightly more awkward than it needs to be.

Overall, this prac forced me to actually run my code rather than just write it, and several bugs only became visible once I had a real local environment with a real database in front of me — the CSS click-trap in particular is the kind of thing that's invisible just from reading the code.
