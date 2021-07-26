---
title: "Ruby Setup on a Macbook"
date: "26.07.2021"
draft: false
tags: ['cheat sheet']
---
# Ruby Setup

First install rbenv and ruby-build to have different ruby versions. Could have used rvm, but it looks a little bit like rotten foot.
```bash
$ brew update
$ brew install rbenv ruby-build
$ echo 'eval "$(rbenv init -)"' >> ~/.zshrc
```


# list all available versions:
    $ rbenv install -l

# install a Ruby version:
    $ rbenv install 2.0.0-p247

# switch to a specific ruby version only for the current session

    $ rbenv local 1.9.3-p327

# switch to a ruby version in all shell

    $ rbenv global 1.9.3-p327

Command Reference here: https://github.com/sstephenson/rbenv
```bash
$ gem update
$ gem install rails
```
# to locate a gem
    $ gem which gemname

# show the local gem version
    $ gem list -l gemname

# uninstall a gem
    $ gem uninstall gemname

# to show some infos about the current gem enviroment, including the gem directories
    $ gem environment

# print the ruby on rails version for the project in the current directory
# or it prints the default ruby on rails version
```bash
$ rails --version
> "Rails 4.1.0"
```
# update rails with
```
$ gem install rails
> "Rails 4.1.4"
```
# install specific rails version
    $ gem install rails --version 3.1.0

# run a command with a specific rails version (won't affect already created rails projects)
    $ rails _3.1.0_ --version

# to update/downgrade ruby on rails change the rails version in the gem file and run
```
$ bundle update
$ bundle install
```
# to update/downgrade only a specific section (eg. and ignore production)
    $ bundle install --without production

### bundler and mojave

To install older nokogiri gems (1.8.5) add this to ./bundle/config within the project folder or in your dotfiles

	BUNDLE_BUILD__NOKOGIRI: "--use-system-libraries=true --with-xml2-include=/usr/local/opt/libxml2/include/libxml2"
	BUNDLE_JOBS: "7"
	BUNDLE_PATH: "vendor/bundle"


To install mysql2 gem on mojave

	sudo installer -pkg /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg -target /

