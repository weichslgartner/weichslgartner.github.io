Jekyll build instructions and Ruby version
======================================

This repository uses the `github-pages` gem and Jekyll versions that are not yet
compatible with Ruby 3.3. If you run `bundle exec jekyll build` with Ruby 3.3
you may see an error in Ruby's Logger (NoMethodError on `[]` for nil). To avoid
that, use Ruby 3.2.x for local builds.

Quick steps (using rbenv):

1. Install Ruby 3.2.2 with rbenv (or your Ruby version manager):

   # install rbenv and ruby-build first (platform-specific)
   rbenv install 3.2.2
   rbenv local 3.2.2

2. Install Bundler and the gems:

   gem install bundler
   bundle config set path 'vendor/bundle'
   bundle install

3. Build the site:

   bundle exec jekyll build --trace

If you prefer chruby, asdf, or system ruby, make sure to switch to a Ruby
3.2.x installation before running `bundle install` and `bundle exec jekyll build`.

If you want to upgrade this site to run on Ruby 3.3, we can either:
- update `github-pages` / `jekyll` versions (may require template/plugin changes), or
- wait for upstream gems to add compatibility fixes.

If you'd like, I can try upgrading the dependencies instead; that is a larger
change and may require template fixes in `_layouts` or plugins.
