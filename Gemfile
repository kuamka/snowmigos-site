source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

gem "tzinfo-data"
gem "wdm", "~> 0.1.0" if Gem.win_platform?

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-sitemap"
  gem "jekyll-gist"
  gem "jekyll-feed"
  gem "jemoji"
  gem "jekyll-include-cache"
  gem "jekyll-algolia"
  gem "jekyll-redirect-from"
end

gem "webrick", "~> 1.7"


# Ruby 3.0.0's RubyGems loads uri before Bundler does, so the bundle has to
# settle for the uri already activated or `bundle exec` refuses to start.
gem "uri", "0.11.0"
