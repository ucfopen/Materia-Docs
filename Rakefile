require 'html-proofer'


task :build do
  sh "bundle exec jekyll build"
end

task :dev do
  sh "bundle exec jekyll serve --livereload"
end

task :test do
  # build into a test directory that has the 'baseurl' encorperated
  sh "bundle exec jekyll build -d _test/Materia-Docs"

  # run proofer
  options = {
    :assume_extension => true,
    :url_ignore => ['#']
  }
  HTMLProofer.check_directory("./_test", options).run
end


task :png_crush do
  sh "find assets/img -type f -name '*.png' -exec pngcrush -ow -brute {} \;"
end
