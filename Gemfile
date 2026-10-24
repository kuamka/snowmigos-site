source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

gem "tzinfo-data"
gem "wdm", "~> 0.1.0" if Gem.win_platform?

# Only the plugins _config.yml declares. GitHub Pages honours that list and
# nothing else, so a plugin auto-required here but missing there would work
# locally and fail in production.
group :jekyll_plugins do
  gem "jekyll-include-cache"
  gem "jekyll-sitemap"
end

gem "webrick", "~> 1.7"


# Ruby 3.0.0's RubyGems loads uri before Bundler does, so the bundle has to
# settle for the uri already activated or `bundle exec` refuses to start.
gem "uri", "0.11.0"
