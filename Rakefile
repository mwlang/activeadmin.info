desc "copies the versioned documentation from activeadmin.git to here with the appropriate jekyll layout prefix"
task :update do 
  clone_active_admin_repo

  # update docs on master branch
  update_files ""

  # also process each version
  major_versions.each do |version|
    update_versioned_files version
  end
end

REPO_FOLDER          = 'repo'
INCLUDES_FOLDER      = '_includes'
LAYOUTS_FOLDER       = '_layouts'
EXCLUDED_LINKS       = [ /CONTRIBUTING/ ]
ACTIVEADMIN_REPO_URL = 'git@github.com:activeadmin/activeadmin.info.git'

def git cmd
  `cd #{repo_path} && git #{cmd}`
end

def root_path
  @root_path ||= File.dirname(File.expand_path(__FILE__))
end

def repo_path
  @repo_path ||= File.join(root_path, REPO_FOLDER)
end

def includes_path
  @includes_path ||= File.join(root_path, INCLUDES_FOLDER)
end

def layouts_path
  @layouts_path ||= File.join(root_path, LAYOUTS_FOLDER)
end

def clone_active_admin_repo
  if File.exist? repo_path
    git "pull"
  else
    git "clone git@github.com:activeadmin/activeadmin.git #{repo_path}"
  end
end

def major_versions
  all_versions = git("tag").split("\n")
  all_versions.map{ |ver| ver.scan(/v\d\.\d/) }.flatten.uniq
end
  
def latest_version
  major_versions.last
end

def latest_minor_version major_version
  all_versions = git("tag -l '#{major_version}*'").split("\n")
  all_versions.last
end

def update_versioned_files major_version
  minor_version = latest_minor_version major_version
  begin
    git "checkout -b #{major_version} #{minor_version}"
    puts "Using #{minor_version} as documentation source"
    update_files "", major_version
  ensure
    git "checkout master"
    git "branch -D #{major_version}"
  end
end

def update_files sub_path, version = ''
  project_docs_path = File.expand_path File.join(repo_path, "docs", sub_path)
  website_docs_path = File.expand_path File.join('.', "docs", version, sub_path)
  layout = version == '' ? "default" : version

  return unless File.exist? project_docs_path
  
  `mkdir -p #{website_docs_path}`

  table_of_contents = []
  
  Dir.glob(File.join(project_docs_path, '*')).each do |fn|
    # recurse into sub-directories
    if File.directory?(fn)
      dir_name = File.basename(fn)
      update_files File.join(sub_path, dir_name), version
      next
    end

    # process the file
    puts "processing #{fn}..."
    
    # change *.md links to *.html
    new_fn = File.join(website_docs_path, File.basename(fn))
    content = replace_md_links File.read(fn), version
    
    # add headers to the table of contents
    headers = content.split("\n").select{|c| c =~ /^#+\s/}
    
    table_of_contents << headers.map do |header| 
      header = header.gsub(/^#+\s/, '')
      {
        fn: File.join("docs", version, sub_path, File.basename(fn).gsub(".md", ".html")), 
        title: header,
        param: header.downcase.gsub(" ", "-")
      } 
    end

    # render the page source
    File.open(new_fn, 'w') do |of|
      of.puts "---\nlayout: #{layout}\n---"
      of.puts content
    end
  end

  # Render the table of contents
  return if version == ''
  render_toc_file table_of_contents, version, layout
  render_toc_layout layout
end

def render_toc_file table_of_contents, version, layout
  version_title = version == latest_version ? "#{version} (latest)" : version
  toc_filename = "toc_#{layout}.html"
  File.open(File.join(includes_path, toc_filename), 'w') do |toc_file|
    toc_file.puts "<h4>Version: #{version_title}</h4>"
    toc_file.puts "<ol class='level-1'>"
    table_of_contents.each do |section_headers|
      header = section_headers.shift
      toc_file.puts "<li><a href='{{ site.baseurl }}/#{header[:fn]}'>#{header[:title]}</a></li>"
      toc_file.puts "    <ol class='level-2'>"
      section_headers.each do |header|
        toc_file.puts "        <li><a href='{{ site.baseurl }}/#{header[:fn]}##{header[:param]}'>#{header[:title]}</a></li>"
      end
      toc_file.puts "    </ol>"
    end
    toc_file.puts "</ol>"
  end
  
  if version == latest_version
    src_fn = File.join(includes_path, toc_filename)
    dest_fn = File.join(includes_path, toc_filename.gsub(version, 'default'))
    File.open(dest_fn, 'w'){ |of| of.puts File.read(src_fn) }
  end
end

# I would've simply replaced "toc.html" with {{page.toc_file}} but such 
# functionality was broken after Jekyll > 1.5.1
# https://github.com/jekyll/jekyll/issues/2844
# http://jekyllrb.com/docs/templates/#includes
def render_toc_layout layout
  layout_filename = "#{layout}.html"
  layout_filename_full = File.join(layouts_path, layout_filename)
  toc_filename = "toc_#{layout}.html"

  content = File.read(File.join(layouts_path, 'default.html'))
  content.gsub!("{% include toc_default.html %}", "{% include #{toc_filename} %}")

  File.open(layout_filename_full, 'w'){ |f| f.puts content }
end

def exclude_link? link
  EXCLUDED_LINKS.detect{|pattern| link =~ pattern }
end

def replace_md_links content, version
  all_md_links = content.scan(/\[[^\(]+\((.+\.md)/).flatten.uniq
  links_to_change = all_md_links.reject{ |link| exclude_link? link }

  links_to_change.each do |link|
    puts "version: #{version} link: #{link}"
    content.gsub!(link, ["/docs", version, link.gsub(/\.md\z/, '.html')].join("/"))
  end
  content
end
