# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'json'
require 'yaml'


VAGRANTFILE_API_VERSION ||= "2"
confDir = $confDir ||= File.expand_path(File.join("config"))

homesteadYamlPath = confDir + "/Homestead.yaml"
homesteadJsonPath = confDir + "/Homestead.json"
afterScriptPath = confDir + "/after.sh"
aliasesPath = confDir + "/aliases"

require File.expand_path(File.dirname(__FILE__) + '/scripts/homestead.rb')

Vagrant.require_version '>= 1.8.4'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.network :public_network

    if File.exists? aliasesPath then
        config.vm.provision "file", source: aliasesPath, destination: "~/.bash_aliases"
    end

    if File.exist? homesteadYamlPath then
        settings = YAML::load(File.read(homesteadYamlPath))
    elsif File.exist? homesteadJsonPath then
        settings = JSON.parse(File.read(homesteadJsonPath))
    end

    Homestead.configure(config, settings)

    if File.exist? afterScriptPath then
        config.vm.provision "shell", path: afterScriptPath, privileged: false
    end

    if defined? VagrantPlugins::HostsUpdater
        config.hostsupdater.aliases = settings['sites'].map { |site| site['map'] }
    end
end
