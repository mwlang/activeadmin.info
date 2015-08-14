desc "copies the documentation from activeadmin.git to here with the appropriate layout prefix"
task :update do 
  update_files 'docs'
  update_files File.join('docs', '3-index-pages')
end

def update_files sub_path
  project_docs_path = File.expand_path File.join('..', 'activeadmin', sub_path)
  website_docs_path = File.expand_path File.join('.', sub_path)
  Dir.glob(File.join(project_docs_path, '*.md')).each do |fn|
    new_fn = File.join(website_docs_path, File.basename(fn))
    puts [fn, new_fn].join(" => ")
    content = File.read fn
    File.open(new_fn, 'w') do |of|
      of.puts "---\nlayout: default\n---"
      of.puts content
    end
  end
  
end