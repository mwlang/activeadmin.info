desc "copies the documentation from activeadmin.git to here with the appropriate layout prefix"
task :update do 
  clone_active_admin_repo
  update_files 'docs'
  update_files File.join('docs', '3-index-pages')
end

REPO_FOLDER = 'repo'
EXCLUDED_LINKS = [
  /CONTRIBUTING/
]

ACTIVEADMIN_REPO_URL = 'git@github.com:activeadmin/activeadmin.info.git'

def clone_active_admin_repo
  repo_path = File.join(File.dirname(File.expand_path(__FILE__)), REPO_FOLDER)

  if File.exist?(repo_path) 
    update_activeadmin_repo 
  else
    clone_activeadmin_repo
  end
end

def update_files sub_path
  project_docs_path = File.expand_path File.join('.', REPO_FOLDER, sub_path)
  website_docs_path = File.expand_path File.join('.', sub_path)

  Dir.glob(File.join(project_docs_path, '*.md')).each do |fn|
    new_fn = File.join(website_docs_path, File.basename(fn))
    content = replace_md_links(File.read fn)
    File.open(new_fn, 'w') do |of|
      of.puts "---\nlayout: default\n---"
      of.puts content
    end
  end
end

def update_activeadmin_repo
  `cd #{REPO_FOLDER} && git pull`
end

def clone_activeadmin_repo
  `git clone --depth 1 git@github.com:activeadmin/activeadmin.git #{REPO_FOLDER}`
end

def exclude_link? link
  EXCLUDED_LINKS.detect{|pattern| link =~ pattern }
end

def replace_md_links content
  all_md_links = content.scan(/\[[^\(]+\((.+\.md)\)/).flatten.uniq
  links_to_change = all_md_links.reject{ |link| exclude_link? link }

  links_to_change.each do |link|
    content.gsub!(link, link.gsub(/\.md\z/, '.html'))
  end
  content
end
