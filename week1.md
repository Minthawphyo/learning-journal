# Week 1 - Basic Web Design

## Learning Activities & Resources
- Browsed Bootstrap 5 examples and templates to find a suitable 
  starting point for my startup site:
  https://getbootstrap.com/docs/5.3/examples/
- Read Bootstrap 5 component docs to customise navbar, cards, 
  and hero sections:
  https://getbootstrap.com/docs/5.3/components/navbar/
- Reviewed GitHub docs on pushing local files via command line:
  https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository
- Read InfinityFree support docs to understand file structure 
  and how to host a static site:
  https://infinityfree.com/support/

## Estimated Hours of Explicit Learning Activity
Approximately 2 hours

## Content Insights
- I already had basic Bootstrap knowledge going into this, so most 
  of my time was spent customising an existing Bootstrap template 
  rather than learning Bootstrap from scratch. I picked a template 
  that suited a B2B business look and modified the navbar, hero 
  section, colours and content for FactoryBridge.
- I used the command line to push my files to GitHub instead of 
  uploading manually through the browser:
```bash
  git init
  git add .
  git commit -m "Add files via upload"
  git remote add origin https://github.com/Minthawphyo/factorybridge.git
  git push -u origin main
```
- InfinityFree requires all files to sit directly inside `htdocs` —
  not inside any subfolder. I learned this the hard way after getting 
  a 403 error when I uploaded my files inside a `FactoryBridge/` 
  subfolder by mistake.
- DNS propagation is not instant — after uploading correctly the site 
  still took around 15-30 minutes to become accessible. I initially 
  thought something was broken but it just needed time.

## Career/Employability/Learning Insights
Since I already knew Bootstrap basics, this week wasn't about learning 
a new technology — it was more about applying what I know to build 
something with a real purpose. Building a site for FactoryBridge, my 
B2B startup idea for connecting businesses with Chinese manufacturers, 
made the task feel more real than just following a tutorial.

The more interesting challenge this week was hosting — I had never used 
InfinityFree before and ran into a few issues (wrong folder structure, 
password format restrictions, 403 errors). These weren't hard problems 
but they taught me that deployment is never as simple as it looks. Even 
a basic static site has steps that can go wrong, and knowing how to read 
error messages and troubleshoot is a skill on its own.

Using the command line to push to GitHub felt more professional than 
uploading files manually through the browser. I want to keep using this 
workflow going forward as it's closer to how real development teams work.

I looked into what hosting options are commonly used for WordPress and 
Joomla sites (coming in Pracs 2 and 3) and found that most entry-level 
web developer jobs expect you to have experience deploying to real 
hosting environments — not just localhost. So getting comfortable with 
InfinityFree and Git now feels like a useful head start.
