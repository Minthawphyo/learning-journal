# Week 2 - Joomla CMS

## Learning Activities & Resources

* Read Joomla 4 beginner documentation to understand the admin panel structure and how CMS differs from static sites: https://docs.joomla.org/J4.x:Getting_Started_with_Joomla
* Watched Joomla installation guide to understand the 1-click installer process on shared hosting
* Read InfinityFree support docs to understand how to install Joomla via the control panel: https://infinityfree.com/support/
* Reviewed Joomla template customisation docs to understand Cassiopeia template options: https://docs.joomla.org/J4.x:Cassiopeia_Template_Customisation

## Estimated Hours of Explicit Learning Activity

Approximately 2.5 hours

## Content Insights

* Joomla is a CMS (Content Management System) — unlike my Week 1 static HTML site, Joomla stores all content in a MySQL database and uses PHP to render pages dynamically. I don't write HTML directly; instead I create "Articles" through an admin panel and Joomla handles the output.

* I installed Joomla on InfinityFree using the 1-click installer through the hosting control panel. This was much faster than manually uploading files — the installer handled the database setup and file extraction automatically.

* Customising the Cassiopeia template was done through **System → Site Template Styles → Advanced**, where I set the site title to "FactoryBridge", added the tagline "Connect Directly With Chinese Manufacturers", changed the colour theme to Dark, and set the font to Roboto.

* I created three articles in Joomla — Home, About, and Contact — and linked them to the Main Menu so they appear in the navigation bar. This is different from Week 1 where I manually wrote anchor tags in HTML.

* I ran into a major issue trying to customise the CSS: InfinityFree disables the PHP function `get_current_user()`, which causes Joomla's template file editor to crash with a fatal error when trying to save files. This meant I could not edit `user.css` through the Joomla admin panel directly.

* As a workaround, I tried injecting CSS through a Custom HTML module using a `<style>` tag, and also attempted to edit the file through InfinityFree's File Manager directly. This taught me that budget hosting providers often have PHP restrictions that can break CMS features unexpectedly.

## Career/Employability/Learning Insights

This week highlighted a key difference between building a static site and using a CMS. With static HTML, I have full control over every line of code. With Joomla, the CMS abstracts a lot of that away — which is great for non-technical users but can feel limiting when you want to do something custom.

The most valuable lesson this week was dealing with the InfinityFree PHP restriction. In a real work environment, hitting a server-level error like `Call to undefined function get_current_user()` would require either escalating to a sysadmin or migrating to a better hosting provider. It taught me that deployment environments matter — the same Joomla installation can behave very differently depending on the server configuration.

I also noticed that Joomla's admin panel has a much steeper learning curve than I expected. Finding where to change templates, modules, menus, and articles requires navigating a complex backend. WordPress is generally considered more beginner-friendly, so I'm curious to compare them in the next prac.

Understanding how a CMS separates content from presentation (articles vs templates vs modules) is a fundamental web development concept that will be useful when working with any CMS platform in a professional setting.
