require 'pry'
require 'octokit'
require 'tmpdir'

INTERNAL_REPO = "tenjin/ios-sdk"
REPO_NAME = "tenjin/tenjin-ios-sdk"
BRANCH = "ci_setup"

def get_release
  puts "Fetching latest github release from #{INTERNAL_REPO}..."
  client = Octokit::Client.new(access_token: ENV["GITHUB_TOKEN"])

  release = client.latest_release INTERNAL_REPO
  notes, version, assets = release[:body], release[:tag_name], release[:assets]

  assets.each do |a|
    f = client.release_asset(a[:url], accept: "application/octet-stream")
    File.open(a[:name],"wb"){|file| file << f}
  end

  [version, assets, notes]
end

def update_pod(version)

end

def push_pod

end


desc "Get new release assets and publish"
task :release do
  version, assets, notes = get_release

  puts "Version : #{version}"
  puts notes
  puts " "
  puts "Files: "
  puts `ls -thor #{assets.map(&:name).join(" ")}`

  Rake::Task['commit_and_push'].invoke([version])
  Rake::Task['github_release'].invoke([version, notes])
  #cocoapods release

end

desc "Create new github release"
task :github_release, [:version, :description] do |t,args|
  unless args[:version] && args[:description]
    raise "Need new version and release notes to perform a release!"
  end

  assets = ["libTenjinSDKUniversal.a","libTenjinSDK.a", "TenjinSDK.h"]
  assets.each { |a| raise "Couldn't find release asset at #{a}" unless File.exists?(a) }

  token = ENV["GITHUB_TOKEN"] || ""
  raise "Github auth token required" if token.empty?

  client = Octokit::Client.new(access_token: token)

  unless client.releases(REPO_NAME).select{ |r| r.name == version}.empty?
    raise "Release #{version} already present on the repo #{REPO_NAME} - please update the release notes!"
  end

  release = client.create_release(REPO_NAME, version, name: version, body: description)
  assets.each { |a| client.upload_asset(release[:url], a, content_type: "text/plain") }
end

desc "Commit and push current changes"
task :commit_and_push,[:version] do |t, args|
  raise "No version provided!" unless args[:version]

  `git diff --exit-code`
  if $?.exitstatus == 0
    raise "No changes to commmit!"
  end

  puts "Updated files:"
  puts `git diff --name-only`

  `git config user.name "Tenjin"`
  `git config user.name "support@tenjin.com"`

  `git commit -am "Version #{args[:version]}"`
  `git push origin #{BRANCH}`
end

