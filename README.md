Sunzi
=====

```
"The supreme art of war is to subdue the enemy without fighting." - Sunzi
```

Sunzi is the easiest server provisioning utility designed for mere mortals. If Chef or Puppet is driving you nuts, try Sunzi!

Sunzi assumes that modern Linux distributions have (mostly) sane defaults and great package managers.

Its design goals are:

* **It's just shell script.** No clunky Ruby DSL involved. Sunzi recipes are written in a plain shell script. Why? Because, most of the information about server configuration on the web is written in shell commands. Just copy-paste them, why should you translate it into a proprietary, inconvenience DSL? Also, shell script is the greatest common denominator on minimum Linux installs.
* **Focus on diff from default.** No big-bang overwriting. Append or replace the smallest possible piece of data in a config file. Loads of custom configurations makes it difficult to understand what you are really doing.
* **Always use the root user.** Think twice before blindly assuming you need a regular user - it doesn't add any security benefit for server provisioning, it just adds extra verbosity for nothing. However, it doesn't mean that you shouldn't create regular users with Sunzi - feel free to write your own recipes.
* **Minimum dependencies.** No configuration server required. You don't even need a Ruby runtime on the remote server.

Quickstart
----------

Install:

    $ gem install sunzi

Go to your project directory, then:

    $ sunzi create

It generates a `sunzi` folder along with subdirectories and templates. Inside `sunzi`, there's `attributes.yml`, which defines dynamic attributes to be used from recipes. Also there's the `remote` folder, which will be transferred to the remote server, that contains recipes and dynamic variables compiled from `attributes.yml`.

Go into the `sunzi` directory, then run the `sunzi deploy`:

    $ cd sunzi
    $ sunzi deploy root@example.com

Now, what it actually does is:

1. SSH to `example.com` and login as `root`
1. Transfer the content of the `remote` directory to the remote server and extract in `$HOME/sunzi`
1. Run `install.sh` in the remote server

As you can see, what you need to do is edit `install.sh` and add some shell commands. That's it.

Directory structure
-------------------

Here's the directory structure that `sunzi create` automatically generates:

```
sunzi/
  attributes.yml    ---- add custom attributes here
  recipes.yml       ---- add remote recipes here
  remote/           ---- everything under this folder will be transferred to the remote server
    attributes/     ---- compiled attributes from attributes.yml at deploy (do not edit directly)
      env
      ssh_key
    recipes/        ---- put commonly used scripts here, referred from install.sh
      ssh_key.sh
    install.sh      ---- main scripts that gets run on the remote server
```

Remote Recipes
--------------

Recipes can be retrieved remotely via HTTP. Put the URL in `recipes.yml`, and Sunzi automatically loads the content and put it into the `remote/recipes` folder.

For instance, if you have the following line in `recipes.yml`,

```
rvm: https://raw.github.com/kenn/sunzi-recipes/master/ruby/rvm.sh
```

`rvm.sh` will be available and you can refer to that recipe by `source recipes/rvm.sh`.

Vagrant
-------

If you're using Sunzi with [Vagrant](http://vagrantup.com/), you need to specify the port number 2222.

    $ vagrant up
    $ sunzi deploy root@localhost 2222
