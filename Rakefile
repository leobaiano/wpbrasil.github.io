# Standard library
require 'rake'
require 'yaml'

# Load the configuration file
config = YAML.load_file("_config.yml")

# Set "rake watch" as default task
task :default => :watch

# rake build
desc "Generate the site (no server)"
task :build do
  system "jekyll build"
end

# rake watch
# rake watch[number]
desc "Generate and watch the site (with an optional post limit)"
task :watch, :number do |t, args|
  number = args[:number]

  if number.nil? or number.empty?
    system "jekyll server --watch"
  else
    system "jekyll server --watch --limit_posts=#{number}"
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
