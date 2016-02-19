# vagrant-render-node

## Overview

Initial demonstration of using Vagrant as a method of starting Blender render
nodes.

## Usage

* Download Hashicorp`s vagrant, and virtualbox 5.0
```
sudo apt-get install virtualbox-5.0
wget https://releases.hashicorp.com/vagrant/1.8.1/vagrant_1.8.1_x86_64.deb && sudo dpkg --install vagrant_1.8.1_x86_64.deb
```

* Clone this repository
```
git clone {this_repo}
```

* create ```data```, ```import```, and ```export``` directories
```
mkdir -p ../data; mkdir -p ../export; mdir -p ../import
```

* Run ```BLEND_FILE="example.blend" vagrant up``` to create new virtual machine and run a test render

* Once complete run ```vagrant ssh``` or ```ssh vagrant@{host_name}```

* If a render is trigged, i.e. a blend file exists in ```import``` then check
```export``` once the job is complete for the rendered image.

* Delete the vagrant ```vagrant destroy --force```

* Repeat for new nodes

### To Do

* Look at spinning up multiple nodes at once - _possible, but how will it work with Blender files - can we read the frame range and divide the sections?_

* Look at possible environments for Vagrant - _at the moment VirtualBox is good for local development, but AWS, vSphere, other platforms are all targets!! :)_

* Write some Chef recipes to replace Bash scripts.

* Incorporate some Service Discovery magic for forming Render Clusters.

* Write a small Python-Flask interface for managing configuration and viewing
 live render information and statistics from the Render Nodes.

### Info

Author: {danielwray}
