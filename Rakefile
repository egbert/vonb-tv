PUBLISH_BRANCH = 'gh-pages'
DEST_DIR = 'build'

def master_repository
  "https://#{ENV['GH_TOKEN']}@github.com/#{github_username}/#{github_repo}.git"
end

def origin_url
  `git config remote.origin.url`
end

def github_username
  origin_url[/github\.com[:\/]([^\/]*)/, 1]
end

def github_repo
  origin_url[/github.com[:\/][^\/]*\/(.*)\.git/, 1]
end

def initialize_repository(repository, branch)
  require 'fileutils'

  FileUtils.mkdir DEST_DIR unless File.directory? DEST_DIR
  system "git clone #{repository} #{DEST_DIR}"
  Dir.chdir DEST_DIR do
    sh "git checkout #{branch}"
  end
end

def update_repository(branch)
  Dir.chdir DEST_DIR do
    sh 'git fetch origin'
    sh "git reset --hard origin/#{branch}"
  end
end

def build
  sh 'bundle exec middleman build'
end

def clean
  require 'fileutils'

  Dir["#{DEST_DIR}/*"].each do |file|
    FileUtils.rm_rf file
  end
end

def push_to_gh_pages(repository, branch)
  sha1, _ = `git log -n 1 --oneline`.strip.split(' ')

  Dir.chdir DEST_DIR do
    sh "git config user.email '#{`git log -1 --pretty=format:%ae`}'"
    sh "git config user.name '#{`git log -1 --pretty=format:%an`}'"
    sh 'git add -A'
    sh "git commit -m 'Update with #{sha1}'"
    system "git push #{repository} #{branch}"
  end
end

desc 'Setup origin repository for GitHub pages'
task :setup do
  initialize_repository master_repository, PUBLISH_BRANCH
  update_repository PUBLISH_BRANCH
end

desc 'Clean built files'
task :clean do
  clean
end

desc 'Build sites'
task :build do
  clean
  build
  push_to_gh_pages master_repository, PUBLISH_BRANCH
end

desc 'Publish website'
task :publish do
  push_to_gh_pages master_repository, PUBLISH_BRANCH
end
