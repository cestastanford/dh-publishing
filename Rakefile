require "rake/clean"

desc "Preview the site with Jekyll with POW"
task :preview do
  puts "Previewing the site locally with Jekyll."

  jekyllPid  = Process.spawn("jekyll build --watch --config _config.yml,_config-pow.yml")

  trap("INT") {
    [jekyllPid].each { |pid| Process.kill(9, pid) rescue Errno::ESRCH }
    exit 0
  }

  [jekyllPid].each { |pid| Process.wait(pid) }
end

desc "Build the production version of the site"
task :build do
  puts "\nBuilding the production version of the site ..."
  ok_failed system "jekyll build"
end

desc "Deploy the site via rsync"
task :rsync do
  puts "\nDeploying the site via rsync..."

  ssh_port       = "22"
  ssh_user       = ""
  rsync_delete   = true
  rsync_options  = "--checksum --stats -avze"
  public_dir     = "public" 
  document_root  = "~/public_html/jasonheppler.org/teaching/data-dh.2016/"

  exclude = ""
  if File.exists?('./rsync-exclude')
    exclude = "--exclude-from '#{File.expand_path('./rsync-exclude')}'"
  end

  ok_failed system("rsync #{rsync_options} 'ssh -p #{ssh_port}' #{exclude} #{"--delete" unless rsync_delete == false} #{public_dir}/ #{ssh_user}:#{document_root}")
end

desc "Build and deploy the production version of the site"
task :deploy => [:build, :rsync]

def ok_failed(condition)
  if (condition)
    puts "OK"
  else
    puts "FAILED"
  end
end

task :syllabus_cat do
  sh %[cd source; cat index.md assignments.md schedule.md policies.md > ../dh-publishing.print-syllabus.md]
end

task :syllabus_pdf do
  sh %[pandoc dh-publishing.print-syllabus.edited.md -o religion-capitalism.print-syllabus.pdf]
end

CLOBBER.include('public/*')
