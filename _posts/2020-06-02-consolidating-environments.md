---
layout: post
title: Consolidating Environments in Ansible
---


A recent project I was working on required that we deploy the same web application in a fairly high number of environments - around 10. Each environment was supposed to be more or less the same, with a few tweaks, and they were completely separate. These environments had grown organically and as such they weren't uniform - they didn't have a common deploy method, file layout, or even infrastructure layout.

We decided to resolve this issue by building one environment using Ansible and then migrating the others to use that method. We managed this pretty quickly and smoothly, using a few techniques that we came up with ourselves, so I wanted to write them up. I'm sure we aren't the first to invent most of them - we certainly aren't Ansible experts - but hopefully writing them down helps someone out!

Some notes about our Ansible setup: we started out with a very static set of servers, so we used host files committed to our git repository to define our environments and put all our servers into a top level group for each environment. We used group variables to define the differences between each group.

So, here are the techniques we used:


## The Incomplete Playbook

When we started out, we had pretty much nothing in Ansible, so where should we start? We ended up starting when we needed to change a config file. We made a new role which just updated that config file and nothing else, and we committed it. We wrote a note at the top of the file reminding other users that the file was now under Ansible control, and the next time we needed to change that file, we used Ansible instead.

Unfortunately, not every environment had the same config files in the same place, so we couldn't then go and apply that Ansible role to every environment - we were forced to have some “Ansible managed” environments and some not. Over time, we were able to migrate environments to use Ansible, using a Migration Playbook.


## The Migration Playbook

Moving our files from one location to another was a thing we needed to get right, first time, and we needed to do it on a number of servers in an environment, so a playbook made perfect sense. It meant we could write the playbook, code review it, make copies of our environment, test it, and iterate on the migration until we were totally happy with it.

By the time we got here, we had every config file controlled by Ansible, so we could safely ignore most of the existing files and just re-deploy a new set of files. We went out of our way to make the files identical to the existing ones so we could compare them using diff - we could clean them up later (see “Checking Entire Config Files into Git”). The only real danger came from places where we had state on disk - we had to make sure we copied these over carefully and, as much as possible, non-destructively.

In writing our migration playbooks I actually wrote a playbook which migrated in the opposite direction, which allowed me to build a clone of our production environment in Vagrant, migrate it to test the playbook, and then quickly reset it. This made my iteration loop nice and quick.


## The Ugly Catch-all Playbook

While we did do both of the above steps, we didn't actually set out to make one perfect playbook and migrate everything to it.

Instead, in most cases, we made a playbook, then added every edge case to it from each environment until we had a superset of every possible configuration. From there we could remove edge cases one by one, making small, easy to review changes.

This meant that we ended up with some ugly roles filled with feature switches, or overcomplicated loops that looped over an array containing a single item. Once we had every environment managed by Ansible, cleaning up these roles became much easier - we could run checks and diffs against every environment, and we could roll the changes out on test environments before we moved to production.


## Provisioning Playbooks vs Deploying Playbooks

We found that we could split up our roles into provisioning - things that needed to be placed on the server once and didn't change much - and deploying - things that we changed almost every release. By and large, this split everything up into “our software” and “vendor software”. We found that our vendor software was very common across all our environments, because we largely installed software and configured it using conventional file locations, while our software was often deployed inconsistently. This meant that we could write the provisioning side easily and apply it to all of our environments while we continued to iterate on our deployment side.


## The Facade

We built a facade over the ansible-playbook command line tool that helped our team with building Ansible command lines. It automated adding logging, stopped people from deploying to production outside approved hours, checked the validity of environment names, and added helpful error messages. As we moved to dynamic inventories, we were able to hide that behind the facade.

We used a Python library called Invoke for this, but Click would probably work better.


## Checking Entire Config Files into Git

There were a few situations where we had entire files written in Python that needed to be deployed into one particular environment. We chose to build a filename using the environment name, and then using that filename to look up an Ansible template. If that template existed, we deployed it.

For example, if the environment was called “production”, we would use this:

```
# roles/deploy/templates/production/settings.py.j2

CUSTOM_SETTING = True

# roles/deploy/tasks/main.yml

- name: copy settings template
  template:
    src: "{{ environment_name }}/settings.py.j2"
    dest: "{{deploy_dir}}/settings.py"
  ignore_errors: true
```

This meant that we could deploy entire files into particular environments that needed them while ignoring this feature for environments that didn't. This technique is pretty powerful for moving the refactoring of configuration files to after your migration to Ansible, and doing it in that order is preferable - you can commit your refactors and roll them out across all your environments in a controlled fashion.
