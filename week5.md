# Learning Journal — WordPress Child Themes

**Project:** PeerSkill child theme, built on top of the custom PeerSkill theme from CP3402 Assignment 1

## Why a child theme, not just editing the theme directly

The practical asked for a child theme rather than modifying the parent theme's files in place, and once I actually built one the reason stopped being abstract. A child theme's `style.css` just needs a `Template:` header pointing at the parent's folder name, and WordPress's own template lookup (`get_header()`/`get_footer()`) checks the child theme's directory *before* the parent's — so dropping a `header.php` or `footer.php` into the child folder overrides the parent's version automatically, with zero changes to the original theme. Anything I didn't override (like `index.php`) just falls through to the parent unchanged. That's a much cleaner mental model than I expected: it's not really "copying and hacking a theme," it's closer to a compile-time override in a config system.

## What I built

Seven visible customisations layered on the parent PeerSkill theme:

- A new brand colour palette (violet/green → amber/teal) via CSS custom-property overrides in `style.css`
- A custom Google Font (Poppins) for headings, enqueued conditionally in `functions.php`
- Squared-off buttons replacing the parent's pill-shaped ones
- A site-wide announcement bar, added by overriding `header.php`
- A rebranded footer (reworded copyright, dynamic year, credit strip), by overriding `footer.php`
- Shorter blog excerpts with a custom "Continue reading →" link, via the `excerpt_length` / `excerpt_more` filters
- A rebranded `wp-login.php` screen (text logo, matching colours), via `login_enqueue_scripts` and the `login_headerurl` filter

## What went wrong — and what it taught me about deployment vs. code

The theme code itself worked first try. What didn't was getting it *onto* the server, and the failures were more educational than the actual theming:

1. **PowerShell's `Compress-Archive` writes backslashes as path separators inside the zip.** That's invalid — the zip spec requires forward slashes. Windows tools don't care and extract it fine locally, but when InfinityFree's Linux server unzipped it, `peerskill-child\style.css` wasn't read as "folder `peerskill-child`, containing `style.css`" — it was one flat file with a literal backslash *in the filename*. WordPress's installer looked for `style.css` in a real subfolder, didn't find one, and threw "The theme is missing the style.css stylesheet" — a completely accurate error pointing at a completely non-obvious cause. I had to bypass `Compress-Archive` entirely and build the zip manually via .NET's `ZipArchive` class, forcing forward slashes on every entry.
2. **A half-broken upload leaves debris that later uploads can't clean up.** Once the corrupted zip had partially installed, WordPress's "update" flow tried to delete the old folder before writing the new one — and the delete itself failed, apparently a permissions/ownership quirk common on InfinityFree's shared hosting (not something fixable from wp-admin or even their web File Manager). The pragmatic fix wasn't fighting the stuck folder; it was sidestepping it — installing the parent theme under a brand-new folder name so WordPress had a clean install with nothing to remove, and updating the child theme's `Template:` header to match.

## Skills demonstrated

| Skill | Where |
|---|---|
| Child theme architecture (`Template:` header, template-hierarchy overrides) | `style.css`, `header.php`, `footer.php` |
| WordPress hooks/filters | `wp_enqueue_scripts`, `excerpt_length`, `excerpt_more`, `login_enqueue_scripts`, `login_headerurl` |
| CSS custom properties for theme-wide restyling | `style.css` `:root` overrides |
| Diagnosing a deployment failure by reasoning about file formats, not guessing | zip path-separator bug |
| Working around a hosting-platform limitation instead of forcing a broken flow | folder-rename workaround for the stuck InfinityFree delete |

## What I'd do differently next time

I'd zip theme folders with a proper cross-platform tool (or at least verify entry names) *before* the first upload, rather than discovering the backslash issue after a failed install. I'd also lean on a local WordPress install (e.g. via Local or a Docker stack) to test theme uploads before pushing to InfinityFree — most of the pain in this practical was hosting-specific and had nothing to do with the theme code itself, which worked correctly the whole time.
