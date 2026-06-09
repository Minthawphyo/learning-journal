# Learning Journal — Week 3
## CP3402 | WordPress CMS & Group Formation

---

## Part 1: WordPress Setup & Theme Development

This week I set up a WordPress site for my startup, FactoryBridge, and built a custom theme from scratch to match the original static site I made in Prac 1.

### Setting Up WordPress on InfinityFree

I already had InfinityFree hosting from the previous pracs, so I logged into the control panel and used **Softaculous** to install WordPress. It only took a few minutes — Softaculous handles the database creation and file installation automatically. Once it finished, I had access to my WordPress admin at:

```
http://factorybridge.infinityfreeapp.com/wp/wp-admin
```

### Building the Theme

Rather than using a pre-built theme, I coded my own custom WordPress theme to keep the same look and feel as my original FactoryBridge static site. The theme folder structure I built:

```
factorybridge-theme/
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── front-page.php
└── page-templates/
    ├── page-how-it-works.php
    ├── page-pricing.php
    ├── page-factories.php
    └── page-login.php
```

**style.css** — Every WordPress theme requires this file. It contains the theme header comment that WordPress reads to identify the theme, plus the base font imports:

```css
/*
Theme Name: FactoryBridge
Theme URI: http://factorybridge.infinityfreeapp.com/wp
Description: Custom theme for FactoryBridge startup
Version: 1.0
*/
```

**functions.php** — This is where I registered everything WordPress needs to know about the theme. I enqueued Bootstrap 5 and Font Awesome from CDN, registered the navigation menu location, and defined the Factory custom post type:

```php
<?php
function factorybridge_enqueue() {
    wp_enqueue_style('bootstrap', 
        'https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css');
    wp_enqueue_style('factorybridge-main', 
        get_template_directory_uri() . '/css/factorybridge.css');
    wp_enqueue_script('bootstrap-js', 
        'https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js', 
        [], null, true);
}
add_action('wp_enqueue_scripts', 'factorybridge_enqueue');

function factorybridge_menus() {
    register_nav_menus(['primary' => 'Primary Menu']);
}
add_action('after_setup_theme', 'factorybridge_menus');
```

**header.php / footer.php** — I ported the navbar and footer HTML directly from my original static site and replaced the hardcoded links with WordPress functions like `get_template_directory_uri()` for asset paths and `wp_nav_menu()` for the navigation.

**Page templates** — For each page (How It Works, Pricing, Browse Factories, Login), I created a separate PHP template file. Each file starts with a template comment so WordPress can detect it:

```php
<?php
/*
 * Template Name: FactoryBridge — Pricing
 */
get_header(); ?>

<!-- Pricing page HTML here -->

<?php get_footer(); ?>
```

### Uploading and Activating the Theme

Once all the files were ready, I zipped the `factorybridge-theme` folder into `factorybridge-theme.zip` and uploaded it via:

**Appearance → Themes → Add New → Upload Theme**

After activating the theme, I:
1. Created the five pages (Home, How It Works, Pricing, Browse Factories, Login) under **Pages → Add New**
2. Assigned each page its matching template under **Page Attributes → Template**
3. Set the static homepage under **Settings → Reading**
4. Built the nav menu under **Appearance → Menus** and assigned it to the Primary Menu location

### WordPress vs Joomla

Having built the same site in both CMSes now, the key differences I noticed:

- WordPress uses `functions.php` to register everything (menus, post types, scripts) — Joomla handles this through XML manifests and module positions
- WordPress template hierarchy (front-page.php, page.php, index.php) is more logical once you understand it
- Joomla's PHP restrictions on InfinityFree caused CSS issues last week — WordPress had no such problems
- WordPress's hook system (`add_action`, `add_filter`) is more developer-friendly for customization

**Live site:** http://factorybridge.infinityfreeapp.com/wp/

---

## Part 2: Group Formation — Interview Exercise

### Overview
This week we completed a group formation exercise over Zoom/Slack, conducting short one-minute interviews with classmates ahead of the major group project.

### My Approach
I focused on **technical skills** to understand what each person could contribute to the project. My questions included:
- What programming languages or tools are you comfortable with?
- Have you worked with CMS platforms or databases before?
- Do you have any experience with backend or API development?

### What I Found
The short format made it hard to assess people properly. A one-minute conversation gives a surface-level impression, but deeper qualities like reliability and communication style are difficult to gauge that quickly. Some people seemed technically confident, but it was hard to tell if that reflected real experience or just how they present themselves.

That said, it helped narrow down who I'd be comfortable working with based on shared technical interests.

### Reflections
If I were to do this again, I'd ask more behavioural questions like:
- *"Tell me about a time a group project went wrong — what did you do?"*
- *"How do you handle deadlines when things get busy?"*

### Employment Insight
This exercise mirrors real hiring — you're making a judgment call with limited information. First impressions matter but can be misleading in both directions. Being aware of this bias is a valuable professional skill, both as a candidate and as someone evaluating others.

---

## Summary
This week I learned that building a custom WordPress theme from scratch is very doable once you understand the template hierarchy and how `functions.php` ties everything together. The group formation exercise reminded me that structured, behavioural questions are far more revealing than technical ones alone.
