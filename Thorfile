# See https://github.com/skynetservices/skydns
user_name = ENV['USER']
$app_name = "gitolite"
$server_image = "#{user_name}/#{$app_name}"
$data_container = "#{$app_name}data"
$data_dir = "/srv"


$port = "9022"

class Default < Thor
  include Thor::Actions

  desc 'restart','All in one'
  def restart
    run "thor image_build"
    run "thor dataimage_build"
    run "thor container_kill"
    run "thor container_start"
    run "thor show_samplecall"
  end

  # Server
  desc 'image_build','Create the server image'
  def image_build
    run "docker build -t #{$server_image} ."
  end

  desc 'container_start', "run #{$app_name}"
  def container_start
    run "docker run -d --name #{$app_name} -p #{$port}:22 --volumes-from #{$data_container} #{$server_image}"
  end

  desc 'container_kill', 'Kill server'
  def container_kill
    run "docker kill #{$app_name}"
    run "docker rm   #{$app_name}"
  end

  desc 'show_samplecall', "show how to use #{$app_name}"
  def show_samplecall
    puts "Edit $HOME/.ssh/config to add Host gitolite"
    puts "git clone gitolite:gitolite-admin"
  end

  # Data

  desc 'datacontainer_build', 'Create data container'
  def datacontainer_build
    run "docker run  --name #{$data_container} -v #{$data_dir} busybox /bin/true"
  end

  desc 'datacontainer_destroy', 'Destructive destroy of data container'
  def datacontainer_destroy
    run "docker rm #{$data_container}"
  end

  desc 'datacontainer_backup', 'Create tar of data in container'
  def datacontainer_backup
    run "mkdir -p $HOME/4backup", verbose: false
    run "docker run --rm --workdir #{$data_dir} --volumes-from #{$data_container} busybox tar cf - . | gzip > $HOME/4backup/#{$data_container}_`date '+%w'`.tz", verbose: false
    run "docker run --rm --workdir #{$data_dir} --volumes-from #{$data_container} busybox tar cf - . | gzip > $HOME/4backup/#{$data_container}_`date '+%U'`.tz", verbose: false
  end

  desc 'datacontainer_shell', 'Shell to data container'
  def datacontainer_shell
    run "docker run --interactive=true --tty=true --rm --workdir #{$data_dir} --volumes-from #{$data_container} busybox /bin/sh"
  end


end

