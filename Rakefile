require 'jasmine'
load 'jasmine/tasks/jasmine.rake'

def porthole_sources
  sources = ["src/porthole.js"]
  sources
end

desc "Build documentation"
task :doc do
  puts 'Creating Documentation'
  require 'rubygems'
  require 'jsdoc_helper'

  FileUtils.rm_r "pages/jsdoc", :force => true

  JsdocHelper::Rake::Task.new(:lambda_jsdoc) do |t|
    t[:files] = porthole_sources
    t[:options] = ''
    t[:out] = 'pages/jsdoc'
  end
  Rake::Task[:lambda_jsdoc].invoke
end

namespace :minifier do
  require "yui/compressor"
  
  def minify(files)
    files.each do |file|
      # skip if already minified
      next if file.include?(".min.")
      min_file = file.split(".")
      extension = min_file.pop
      min_file.push "min"
      min_file.push extension
      min_file = min_file.join(".")
      
      if extension == "js"
        compressor = YUI::JavaScriptCompressor.new(:munge => true)
      elsif
        compressor = YUI::CssCompressor.new
      else
        puts "Don't know how to minify this extension: #{file}"
      end
      code = File.read(file)
      puts "Compressing #{file} -> #{min_file}"
      code_min = compressor.compress(code)
      File.open(min_file, 'w') {|f| f.write(code_min) }
    end
  end

  desc "minify"
  task :minify => [:minify_js]

  desc "minify javascript"
  task :minify_js do
    minify(FileList['src/**/*.js'])
  end
end

namespace :example do  
desc "Build example"
task :build => 'minifier:minify' do
  FileUtils.mkdir_p 'example/sandbox.ternarylabs.com/porthole/js'
  FileUtils.cp_r Dir.glob('src/**/*.js'), 'example/sandbox.ternarylabs.com/porthole/js'
end

desc "Publish example"
task :publish => :build do
  system("rsync -avz --delete 'example/sandbox.ternarylabs.com/porthole' 'ternarylabs.com:sandbox.ternarylabs.com/'")
  system("rsync -avz --delete 'example/demo.auberger.com/porthole' 'auberger.com:demo.auberger.com/'")
end
end