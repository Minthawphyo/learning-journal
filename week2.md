# Week 2 - Joomla CMS

## Learning Activities & Resources

* Read Joomla 4 beginner documentation to understand the admin panel structure and how CMS differs from static sites: https://docs.joomla.org/J4.x:Getting_Started_with_Joomla
* Watched Joomla installation guide to understand the 1-click installer process on shared hosting
* Read InfinityFree support docs to understand how Joomla installs on shared hosting environments: https://infinityfree.com/support/
* Reviewed Joomla template customisation docs to understand what the Cassiopeia template allows you to change: https://docs.joomla.org/J4.x:Cassiopeia_Template_Customisation
* Researched the PHP `get_current_user()` error on the Joomla issue tracker to understand why the template file editor was crashing on InfinityFree: https://issues.joomla.org/tracker/joomla-cms/35196

## Estimated Hours of Explicit Learning Activity

Approximately 3 hours

## Content Insights

* Joomla is a CMS (Content Management System), which means instead of writing HTML and CSS files manually like in Week 1, you manage your site through an admin panel. Content is stored in a MySQL database and rendered dynamically using PHP. On the surface this sounds easier  but coming from a background where I built my Week 1 site by hand, Joomla felt surprisingly restrictive.

* Installing Joomla on InfinityFree was straightforward using the 1-click installer in the hosting control panel. It automatically set up the database, extracted the files, and configured the basic settings. That part was genuinely easy and much faster than manual deployment.

* The real challenge started when I tried to customise the site. Joomla's Cassiopeia template gives you a limited set of options  you can change the colour theme, font, title, and tagline, but that's about it without touching code. Compared to Week 1 where I could make the site look exactly how I wanted by writing my own Bootstrap components, Joomla felt like working with both hands tied behind my back.

* I ran into a frustrating server-level error when trying to edit the `user.css` file through Joomla's template editor. InfinityFree disables the PHP function `get_current_user()`, which causes Joomla's file editor to crash with a fatal error every time you try to save. This meant I could not customise the CSS through the normal Joomla workflow at all.

* I tried multiple workarounds  injecting CSS through a Custom HTML module using a `<style>` tag, editing the file through InfinityFree's File Manager, and creating the `user.css` file in different locations. Some partially worked but the styles didn't always apply correctly due to how Cassiopeia loads its stylesheets. It was genuinely frustrating to spend so much time on something that should have been a simple CSS edit.

* Despite the CSS issues, I successfully created three articles (Home, About, Contact), linked them to the Main Menu, moved the navigation to the topbar, unpublished the Login Form module, and customised the template title and tagline to match FactoryBridge branding.

## Career/Employability/Learning Insights

This week gave me a much more honest perspective on CMS platforms. Before this prac, I thought Joomla would make things easier. In some ways it does  non-technical users can update content without touching code, and installation is fast. But as someone who already knows HTML and CSS, I found it more frustrating than helpful. I kept wanting to just open a file and edit it directly, but Joomla puts layers of abstraction between you and the actual code.

The most eye-opening part of this week was hitting the InfinityFree PHP restriction. A core Joomla feature  editing template files   was completely broken because the hosting provider disabled a standard PHP function. This is the kind of problem you would never encounter on localhost, and it taught me something important: the hosting environment is not just a place to put your files. It actively shapes what you can and cannot do with your application. In a professional setting, this would be a conversation with a sysadmin or a reason to switch hosting providers entirely.

I also started to understand why developers often prefer building custom sites over using a CMS. When I built the FactoryBridge site in Week 1 with plain HTML and Bootstrap, I had complete control  every pixel, every colour, every animation was exactly what I wanted. With Joomla, I spent most of my time fighting the system instead of building something. That said, I can see the value of Joomla for clients who need to manage their own content without a developer, which is a real and common use case.

I'm curious to see how WordPress compares in the next prac. WordPress has a much larger ecosystem of themes and plugins, so hopefully the customisation experience will feel less limiting. But this week made me appreciate that knowing how to hand-code a site is still a genuinely useful skill  even in a world full of CMS platforms.
