# Libraries
require 'rubygems'
require 'rake'
require 'yaml'
require 'fileutils'

# Load the configuration file
config = YAML.load_file("_config.yml")

# Set "rake watch" as default task
task :default => :watch

# rake build
desc "Build the site"
task :build do
  sh "jekyll build --safe"
  puts "Build is ready"
end

# rake watch
# rake watch[number]
desc "Generate and watch the site (with an optional post limit)"
task :watch, :number do |t, args|
  number = args[:number]

  if number.nil? or number.empty?
    sh "jekyll server --watch --baseurl='/'"
  else
    sh "jekyll server --watch --baseurl='/' --limit_posts=#{number}"
  end
end

# rake preview
desc "Launch a preview of the site in the browser"
task :preview do
  require 'launchy'

  Thread.new do
    puts "Launching browser for preview..."
    sleep 2
    Launchy.open("http://localhost:4000/")
  end

  Rake::Task[:watch].invoke
end

# rake deploy
desc "Deploy to Github Pages"
task :deploy => [:build] do
  # Stops if has pending files to commit
  if /nothing to commit/ !~ `git status`
    raise "Commit your modifications before to make the deploy!"
  end

  # Sets the deploy commit
  head = `git log --pretty="%h" -n1`.strip
  message = "Site updated to #{head}"

  # Create or updated the deploy directory
  if ! File.directory?(config["deploy"])
    puts "Cloning the #{config["git"]["branch"]} branch"
    sh "git clone -b #{config["git"]["branch"]} git@github.com:#{config["git"]["user"]}/#{config["git"]["repo"]}.git _deploy"
  else
    cd config["deploy"] do
      puts "Updating the gh-pages branch"
      sh "git pull"
    end
  end

  # Copy build files to deploy directory.
  FileUtils.cp_r(Dir["#{config["destination"]}/*"], config["deploy"])

  # Make the deploy
  cd config["deploy"] do
    sh 'git add -A'
    if /nothing to commit/ =~ `git status`
      raise "No changes to commit."
    else
      sh "git commit -m \"#{message}\""
    end
    sh "git push origin gh-pages"
  end

  puts "Deploy is ready!"
end

# rake new_post["Post title","Categories"]
desc "Create a post"
task :new_post, :title, :categories do |t, args|
  title = args[:title]
  categories = args[:categories]

  if title.nil? or title.empty?
    raise "Please add a title to your post."
  end

  if categories.nil? or categories.empty?
    categories = ""
  end

  date = Time.now.strftime("%Y-%m-%d")
  post_date = Time.now.strftime("%Y-%m-%d %H:%M")
  filename = "#{date}-#{title.gsub(/(\'|\!|\?|\:|\s\z)/, "").gsub(/\s/, "-").downcase}.md"
  path = File.join("_posts", filename)
  content = <<-HTML
---
layout: post
title: "TITLE"
date: DATE
categories: CATEGORIES
---

HTML

  if File.exists?(path)
    raise "The post already exists."
  else
    content.gsub!("TITLE", title).gsub!("DATE", post_date).gsub!("CATEGORIES", categories)
    File.open(path, "w") do |file|
      file.puts content
    end
    puts "Post \"#{title}\" created!"
  end
end

# rake new_page["Post title","layout","path/to/page"]
desc "Create a page"
task :new_page, :title, :layout, :path do |t, args|
  title = args[:title]
  layout = args[:layout]
  path = args[:path]

  if title.nil? or title.empty?
    raise "Please add a title to your page."
  end

  if layout.nil? or layout.empty?
    layout = "default"
  end

  if path.nil? or path.empty?
    path = "./"
  else
    FileUtils.mkdir_p("#{path}")
  end

  filename = "#{title.gsub(/(\'|\!|\?|\:|\s\z)/,"").gsub(/\s/,"-").downcase}.md"
  filepath = "#{path}/#{filename}"
  content = <<-HTML
---
layout: LAYOUT
title: "TITLE"
---

HTML

  if File.exists?(filepath)
    raise "The post already exists."
  else
    content.gsub!("LAYOUT", layout).gsub!("TITLE", title)
    File.open(filepath, "w") do |file|
      file.puts content
    end
    puts "Page \"#{title}\" created!"
  end
end
