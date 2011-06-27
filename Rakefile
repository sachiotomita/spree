require 'rake'
require 'rake/gempackagetask'
require 'thor/group'

spec = eval(File.read('spree.gemspec'))
Rake::GemPackageTask.new(spec) do |pkg|
  pkg.gem_spec = spec
end

def run_all_tests(database_name)
  %w(api auth core promo).each do |gem_name|
    puts "########################### #{gem_name}|#{database_name} (features) ###########################"
    cmd = "rm #{gem_name}/Gemfile*"; puts cmd; system cmd
    sh "cd #{gem_name} && #{$0} test_app DB_NAME='#{database_name}'"
    sh "cd #{gem_name} && bundle exec cucumber -p ci"
  end

  %w(api auth core dash promo).each do |gem_name|
    puts "########################### #{gem_name}|#{database_name} (spec) ###########################"
    cmd = "rm #{gem_name}/Gemfile*"; puts cmd; system cmd
    sh "cd #{gem_name} && #{$0} spec"
  end
end

task :default => :all_tests

desc "Run all tests for sqlite3 only"
task :all_tests do
  run_all_tests("sqlite3")
end

desc "Run all tests for all supported databases"
task :ci do
  cmd = "bundle update"; puts cmd; system cmd;

  %w(sqlite3 mysql).each do |database_name|
    run_all_tests(database_name)
  end
end

desc "clean the whole repository by removing all the generated files"
task :clean do
  cmd = "rm -rf sandbox"; puts cmd; system cmd
  %w(api auth core dash promo).each do |gem_name|
    cmd = "rm #{gem_name}/Gemfile*"; puts cmd; system cmd
    cmd = "cd #{gem_name}/spec &&  rm -rf test_app"; puts cmd; system cmd
  end
end

namespace :gem do
  desc "run rake gem for all gems"
  task :build do
    %w(core auth api dash promo sample).each do |gem_name|
      puts "########################### #{gem_name} #########################"
      cmd = "rm -rf #{gem_name}/pkg"; puts cmd; system cmd
      cmd = "cd #{gem_name} && bundle exec rake gem"; puts cmd; system cmd
    end
    cmd = "rm -rf pkg"; puts cmd; system cmd
    cmd = "bundle exec rake gem"; puts cmd; system cmd
  end
end

namespace :gem do
  desc "run gem install for all gems"
  task :install do
    version = File.read(File.expand_path("../SPREE_VERSION", __FILE__)).strip

    %w(core auth api dash promo sample).each do |gem_name|
      puts "########################### #{gem_name} #########################"
      cmd = "rm #{gem_name}/pkg"; puts cmd; system cmd
      cmd = "cd #{gem_name} && bundle exec rake gem"; puts cmd; system cmd
      cmd = "cd #{gem_name}/pkg && gem install spree_#{gem_name}-#{version}.gem"; puts cmd; system cmd
    end
    cmd = "rm -rf pkg"; puts cmd; system cmd
    cmd = "bundle exec rake gem"; puts cmd; system cmd
    cmd = "gem install pkg/spree-#{version}.gem"; puts cmd; system cmd
  end
end

namespace :gem do
  desc "Release all gems to gemcutter. Package spree components, then push spree"
  task :release do
    version = File.read(File.expand_path("../SPREE_VERSION", __FILE__)).strip

    %w(core auth api dash promo sample).each do |gem_name|
      puts "########################### #{gem_name} #########################"
      cmd = "cd #{gem_name}/pkg && gem push spree_#{gem_name}-#{version}.gem"; puts cmd; system cmd
    end
    cmd = "gem push pkg/spree-#{version}.gem"; puts cmd; system cmd
  end
end

desc "Creates a sandbox application for testing your Spree code"
task :sandbox do
  require 'rails/generators'
  require File.expand_path('../core/lib/generators/spree_core/site/site_generator', __FILE__)
  require File.expand_path('../core/lib/generators/spree_core/sandbox/sandbox_generator', __FILE__)

  SpreeCore::Generators::SandboxGenerator.start
end
