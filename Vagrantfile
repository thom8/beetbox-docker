# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'
require 'yaml'

Vagrant.require_version '>= 1.8.5'

Vagrant.configure("2") do |config|

  ssh_port = "2224"

  config.vm.synced_folder ".", "/var/beetbox"

  config.vm.define "beetbox", primary: true do |bb|
    bb.vm.provider "docker" do |d|
      d.name = "beetbox"
      d.build_dir = ".beetbox"
      d.ports = ["80:80", "#{ssh_port}:22"]
      d.force_host_vm = false
      d.has_ssh = true
      d.link("beetbox_db:beetbox_db")
    end

    bb.ssh.username = "root"
    bb.ssh.insert_key = false
    bb.ssh.host = "localhost"
    bb.ssh.port = ssh_port

    bb.vm.provision "beetbox", type: "shell", inline: "BEET_PROFILE=docker /beetbox/provisioning/beetbox.sh"
    bb.vm.provision "php-fpm", type: "shell", inline: "service php5.6-fpm start"

  end

  config.vm.define "db" do |db|
    db.vm.provider "docker" do |d|
      d.name = "beetbox_db"
      d.image = "mariadb"
      d.ports = ["3306:3306"]
      d.expose = ["3306"]
      d.force_host_vm = false
      d.env = {
        'MYSQL_ROOT_PASSWORD' => 'root',
        'MYSQL_DATABASE' => 'beetbox',
        'MYSQL_USER' => 'beetbox',
        'MYSQL_PASSWORD' => 'beetbox'
      }
    end
  end

end
