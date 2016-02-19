# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Import json library
require 'json'

# Read in configuration files
cwd = File.dirname(File.expand_path(__FILE__))
vagrant_configuration = File.read("#{cwd}/config/vagrant_config.json")
blender_configuration = File.read("#{cwd}/config/blender_config.json")
source_configuration = File.read("#{cwd}/config/source_config.json")

# Read json files and convert to hashes
vagrant_dict = JSON.parse(vagrant_configuration)
blender_dict = JSON.parse(blender_configuration)
source_dict = JSON.parse(source_configuration)

# Generate random number for hostname
host_id = Random.new.rand(8196)

# Set banner message
vagrant_message = "Render Node :: #{vagrant_dict['host']}#{host_id} :: Finished"

# Get Blender file from commandline
blend_file = ENV['BLEND_FILE']
if @blend_file == ""
  puts "A blend file is required!"
  puts "Example: BLEND_FILE='/tmp/my_scene.blend'"
  exit
end
start_frame = ENV['START_FRAME']
if @start_frame == ""
  puts "Setting start frame to 1"
  start_frame = "1"
end
end_frame  = ENV['END_FRAME']
if @end_frame == ""
  puts "Setting end frame to 2"
  end_frame = "2"
end

# Output settings
puts "::Blend file ------------------------------"
puts "[Blend file to render    ]|[ #{blend_file} ]"
puts "[Blend file start frame  ]|[ #{start_frame} ]"
puts "[Blend file end frame    ]|[ #{end_frame} ]"
puts "::Blender paths ---------------------------"
puts "[Blender host import path]|[ #{source_dict['host_source_import_path']} ]"
puts "[Blender host export path]|[ #{source_dict['host_source_export_path']} ]"
puts "[Blender import path     ]|[ #{source_dict['guest_source_import_path']} ]"
puts "[Blender export path     ]|[ #{source_dict['guest_source_export_path']} ]"
puts "::Blender conf ----------------------------"
puts "[Blender binary path     ]|[ #{blender_dict['blender_loc']}/#{blender_dict['blender_ver']}/ ]"
puts "[Blender version         ]|[ #{blender_dict['blender_ver']} ]"
puts "::Vagrant conf ----------------------------"
puts "[Hostname                ]|[ #{vagrant_dict['host']}-#{host_id} ]"
puts "[RAM amount              ]|[ #{vagrant_dict['ram']} ]"
puts "[CPU count               ]|[ #{vagrant_dict['cpu']} ]"

Vagrant.configure(2) do |config|
  # Set up virtual machine parameters
  config.vm.box = "#{vagrant_dict['box']}"
  config.vm.hostname = "#{vagrant_dict['host']}#{host_id}"
  # Set up virtual machine shared directories
  config.vm.synced_folder "#{vagrant_dict['host_vagrant_data_path']}", "#{vagrant_dict['guest_vagrant_data_path']}"
  # Import / Export directories for blender to read from / write to
  config.vm.synced_folder "#{source_dict['host_source_import_path']}", "#{source_dict['guest_source_import_path']}"
  config.vm.synced_folder "#{source_dict['host_source_export_path']}", "#{source_dict['guest_source_export_path']}"

  # Expect virtualbox as provider, and we set some values here
  config.vm.provider "virtualbox" do |vb|
    vb.name = "#{vagrant_dict['host']}-#{host_id}"
    vb.memory = "#{vagrant_dict['ram']}"
    vb.cpus = "#{vagrant_dict['cpu']}"
  end

  # Useful banner message to display after vagrant UP
  config.vm.post_up_message = "#{vagrant_message}"

  # Some quick hacks with bash (we're not using Windows! :D )
  # Download required Blender libraries, binary, and install
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install -y vim git curl
    # Install required libraries for Blender
    sudo apt-get install -y libfreetype6 libglu1-mesa libxi6 libgconf-2-4
    # Move to /tmp and download blender source specified in blender_config.json
    cd /tmp/
    wget #{blender_dict['blender_url']}
    bzip2 -d #{blender_dict['blender_pkg']}.tar.bz2
    tar xf #{blender_dict['blender_pkg']}.tar
    mkdir -p #{blender_dict['blender_loc']}/#{blender_dict['blender_ver']}
    cp -R /tmp/#{blender_dict['blender_pkg']}/* #{blender_dict['blender_loc']}/#{blender_dict['blender_ver']}
    ln -s #{blender_dict['blender_loc']}/#{blender_dict['blender_ver']}/blender /usr/local/bin/blender
  SHELL

  # Quick example of Rendering an Blend files
  config.vm.provision "shell", inline: <<-SHELL
    # Overview:
    # Start Blender in background mode with no audio
    # Open blend file specified on command line
    # Export to path set in source_config.json with image names 'render_xxxx'
    # Format is set in source_config.json and by default is EXR
    # Start Frame, End Frame are set in source_config.json
    # -a tells Blender to render entire animation
    blender #{blender_dict['blender_args']} #{source_dict['guest_source_import_path']}#{blend_file} -o #{source_dict['guest_source_export_path']}render_#### -F #{source_dict['source_export_format']} -s #{start_frame} -e #{end_frame} -a
  SHELL
end
