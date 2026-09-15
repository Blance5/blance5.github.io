# Personal Site Transition Notes

This document is for another agentic model or engineer taking over work on this repository.

## Repository Model

This repo uses a two-branch workflow:

- `source`: editable Jekyll source code
- `master`: generated static site that GitHub Pages publishes

The live site is currently served from `master`, not `gh-pages`.

Important consequence:
- Never treat `master` as the source of truth for editing.
- Make source changes on `source`.
- Deploy generated output from `source` to `master`.

## What Was Reconstructed

Historically, some changes were made directly in compiled output on `master`. During this handoff, the missing customizations were pushed back into `source` so a fresh Jekyll build reproduces the live site.

The main restored customizations are:

- Homepage/about page content and formatting
- Resume tab behavior at `/blog/`
- Current profile photo
- Project portfolio entries that previously only existed in compiled output
- New baseball scheduler portfolio entry

## Current Important Site Behavior

### Homepage

The live homepage is generated from:

- [_pages/about.md](/home/blance5/code/side_projects/personal_site/_pages/about.md:1)
- [_layouts/about.html](/home/blance5/code/side_projects/personal_site/_layouts/about.html:1)
- [_config.yml](/home/blance5/code/side_projects/personal_site/_config.yml:1)

Key current values:

- `first_name: Ben`
- `last_name: Lance`
- location text is `Located in Atlanta, Georgia`
- navbar social icons are enabled on the about page

### Resume Tab

The navbar label is controlled by `site.blog_name` in:

- [_config.yml](/home/blance5/code/side_projects/personal_site/_config.yml:1)

The `/blog/` page is not a real blog index anymore. It is a custom resume page implemented in:

- [blog/index.html](/home/blance5/code/side_projects/personal_site/blog/index.html:1)

The header template was updated to use `{{ site.blog_name }}` instead of hardcoding `blog`:

- [_includes/header.html](/home/blance5/code/side_projects/personal_site/_includes/header.html:1)

### Footer

The footer is intentionally pinned to 2023 to match the live site, using `footer_year` in:

- [_config.yml](/home/blance5/code/side_projects/personal_site/_config.yml:1)
- [_includes/footer.html](/home/blance5/code/side_projects/personal_site/_includes/footer.html:1)

### Projects

Projects are driven by the `_projects` collection and rendered on:

- [_pages/projects.md](/home/blance5/code/side_projects/personal_site/_pages/projects.md:1)
- [_includes/projects.html](/home/blance5/code/side_projects/personal_site/_includes/projects.html:1)

Current project source files:

- [_projects/1_project.md](/home/blance5/code/side_projects/personal_site/_projects/1_project.md:1)
- [_projects/2_project.md](/home/blance5/code/side_projects/personal_site/_projects/2_project.md:1)
- [_projects/3_project.md](/home/blance5/code/side_projects/personal_site/_projects/3_project.md:1)
- [_projects/4_project.md](/home/blance5/code/side_projects/personal_site/_projects/4_project.md:1)
- [_projects/5_project.md](/home/blance5/code/side_projects/personal_site/_projects/5_project.md:1)
- [_projects/6_project.md](/home/blance5/code/side_projects/personal_site/_projects/6_project.md:1)
- [_projects/7_project.md](/home/blance5/code/side_projects/personal_site/_projects/7_project.md:1)

The project ordering is controlled by each file's `importance` value.

### Profile Image

The current live image is:

- [assets/img/prof_pic.jpg](/home/blance5/code/side_projects/personal_site/assets/img/prof_pic.jpg:1)

Do not replace it accidentally with an older version from history unless explicitly requested.

## Local Build Setup

This repo is Jekyll/Ruby-based.

Relevant files:

- [Gemfile](/home/blance5/code/side_projects/personal_site/Gemfile:1)
- [_config.yml](/home/blance5/code/side_projects/personal_site/_config.yml:1)
- [bin/deploy](/home/blance5/code/side_projects/personal_site/bin/deploy:1)

### WSL/Linux Notes

`wdm` is Windows-only and has already been scoped correctly in the `Gemfile`:

- load `wdm` only on `:mingw`, `:mswin`, `:x64_mingw`

Do not revert that change or Jekyll builds will fail on WSL/Linux.

### One-Time Setup

System packages typically needed:

```bash
sudo apt-get update
sudo apt-get install -y ruby-dev build-essential zlib1g-dev
```

Recommended Bundler config in this repo:

```bash
bundle config set --local path vendor/bundle
bundle install
```

Also ensure git identity is configured before deploying:

```bash
git config user.name "blance5"
git config user.email "benhlance@gmail.com"
```

Using repo-local `git config` is safer than relying on environment-specific global config.

## Local Development

Build:

```bash
bundle exec jekyll build
```

Serve locally:

```bash
bundle exec jekyll serve
```

If Bundler/Gem resolution is inconsistent in your environment, re-run:

```bash
bundle install
```

## Deployment

### Correct Deploy Command

Run this from `source`:

```bash
bash bin/deploy -u
```

The `-u` flag is essential in this repo. It means:

- source branch = `source`
- deploy branch = `master`

Without `-u`, the script defaults to `master -> gh-pages`, which is not the live workflow.

### What `bin/deploy -u` Does

1. Verifies there are no uncommitted or untracked files
2. Checks out `source`
3. Deletes local `master`
4. Creates a fresh local `master`
5. Runs `bundle exec jekyll build`
6. Replaces branch contents with `_site/*`
7. Commits generated output
8. Force-pushes `master` to `origin`
9. Checks out `source` again

### Pre-Deploy Checklist

Before running deploy:

```bash
git checkout source
git status
bundle exec jekyll build
```

Expected state:

- on branch `source`
- working tree clean
- local build succeeds

### Post-Deploy Verification

After deploy:

```bash
git fetch origin
git log origin/master -1 --oneline
```

Then check the live site after GitHub Pages updates.

## Recovery If Deploy Fails Midway

This happened during the current work and is the most common footgun in this repo.

Symptom:

- you run `bin/deploy -u`
- deploy switches to a generated local `master`
- commit or push fails
- `source` now appears to have massive staged deletions and generated files
- `bin/deploy` may appear missing because you are still on `master`

Recovery:

```bash
git reset --hard origin/source
git checkout source
git branch -D master
```

Why this works:

- `origin/source` contains the real editable state
- the bad local `master` is just a half-finished generated branch

Only do this if the real source work has already been committed or pushed to `source`.

## Branch and Push Rules

- Commit all real changes to `source`
- Push `source` normally:

```bash
git push origin source
```

- Deploy separately with:

```bash
bash bin/deploy -u
```

Do not use:

```bash
git push source
```

That is invalid because `source` is a branch, not a remote.

## Files Another Agent Should Read First

If taking over this repo, read these first:

- [TRANSITION.md](/home/blance5/code/side_projects/personal_site/TRANSITION.md:1)
- [_config.yml](/home/blance5/code/side_projects/personal_site/_config.yml:1)
- [Gemfile](/home/blance5/code/side_projects/personal_site/Gemfile:1)
- [bin/deploy](/home/blance5/code/side_projects/personal_site/bin/deploy:1)
- [_pages/about.md](/home/blance5/code/side_projects/personal_site/_pages/about.md:1)
- [blog/index.html](/home/blance5/code/side_projects/personal_site/blog/index.html:1)
- [_pages/projects.md](/home/blance5/code/side_projects/personal_site/_pages/projects.md:1)
- [_projects/7_project.md](/home/blance5/code/side_projects/personal_site/_projects/7_project.md:1)

## Editing Guidance

- Preserve the `source -> master` deploy model unless the owner explicitly chooses to modernize it.
- Treat `master` as generated output only.
- When matching the live site, verify whether a customization currently lives in source or only in compiled output.
- If the live site and source diverge again, use `master` as a reference for reconstruction, but do not continue editing `master` directly.
- For project additions, update `_projects/*.md` and supporting assets in `assets/img/`.
- For navbar/resume/homepage regressions, inspect `_config.yml`, `_pages/about.md`, `blog/index.html`, `_includes/header.html`, and `_includes/footer.html` before assuming the problem is in generated HTML.

## Current New Project Added During This Work

The latest added portfolio item is:

- [_projects/7_project.md](/home/blance5/code/side_projects/personal_site/_projects/7_project.md:1)

It currently uses:

- title: `Baseball Game Scheduler`
- thumbnail: [assets/img/baseball-game-scheduler.png](/home/blance5/code/side_projects/personal_site/assets/img/baseball-game-scheduler.png:1)

This entry was intentionally written as a professional product description rather than a casual or narrative writeup.
