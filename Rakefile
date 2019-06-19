require 'pry'
require 'octokit'
require 'tmpdir'

INTERNAL_REPO = "tenjin/ios-sdk"

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

def commit_and_tag(version)

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
  #update_pod(version)
  #commit_and_tag(version)
  #push_pod()
end

