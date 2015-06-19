# What is it?

Configuration Management is at one of the process at the core of the modern and
trendy _DevOps_ movement. It is however commonly misunderstood.

   - Is it the Automation? Yes ... and no. Automation is part of it.
   - Is it Patch Management? I would say no.
   - Is it the process of launching processes on multiple systems in parallel? Again, I would say no

Officially (as in, what Wikipedia says) the definition is:

> A process for establishing and maintaining the consistency of a product's performance , functional and physical attributes with its requirements, design and operational information throughout its life

That is a bit generic over-complex, so lets try and break it down a bit. I should say that this will be fairly Puppet-centric as it what I use on a day to day basis. The ideas should apply to the alternatives too.

The basic process is something comprises of 4 steps that look something like:

![Configuration Management Process](https://docs.google.com/drawings/d/1eKeczoV34BMq3NqBXvVFqweSYgMtTYlZCMZXhlnpcpw/pub?w=808&h=540)

   1. Define: A totally non-ambiguous method to describe how a system should be configured.
   2. Simulate: Test the configuration to be certain of exactly what will happen
   3. Enforce: Force the systems into the state defined in 1
   4. Report: Provide a mechanism to collect the results of the _Enforce_ stage and compare it to the _Define_ and _Simulate_ stages

## Define

This is where you tell your system what it should be doing. Each tool will have some sort of language that allows you to describe not only the configuration, but also relationships between different resources. This could be:

   1. Install a package
   2. Put a configuration file in place
   3. Ensure a service is running
   4. Watch that configuration file; if it changes, restart the service

Different tools do this in different ways. In Chef you basically write Ruby, Puppet has its own language geared to the job in hand (Domain Specific Language, or DSL), Ansible uses YAML files. Each has its strengths and weakness, but what you end up with is effectively code - the fabled _Infrastructure as Code_.

## Simulate

As we have our infrastructure describes as source code, we can start to use all those wonderful tools and techniques that developers have been using for years: version control, unit tests and the like.

All the main tools provide various ways to do simulated runs. For example, Puppet has a `--noop` flag, and syntax checking built in. In addition, either Puppetlab or the community have created tools for _linting_ (making sure your code follows the standards and guidelines) and even acceptance testing (deploying in a test environment, perhaps using Docker). 

These mean that, if you do this step well, you can be fairly confident that what you have written will perform as it should once you go to production.

## Enforce

This is the step where configuration management really comes into its own. It is also the step that causes the most problems for some people.

The inital run will go through your code and start pulling everything into place. It should also do it in such an order it will not fail because another step has not finished - it will not try and start a service that has not been installed yet for example.

This is not a one time operation though. It will periodically check the system and if someone has changed something, it will be pulled back in to place.

## Report

All this would be useless if no-one had any idea what was happening. All the major tools provide some mechanism for this. In Puppet the agents all report back to the server what has occured when they run:

   - Packages installed
   - Configuration files changed
   - Services refreshed
   - etc
   
These are then stored in PuppetDB which provides access to the information via a REST API. You can then use this to generate your own reports, hook up a dashboard of some description, whatever you want.

# What Configuration Management is not

This is another major area of confusion. Partly because marketing deptartments do not like a tool that only does one thing, so they claim they can do everything.

They are not _provisioning_ tools. These are tools for preparing a new machine/instance/VM. In my opinion they should do the minimum possible to prepare it to connect to the CM tool - install the base OS, deploy agents, tell it to start on boot up. An example of a provisioning tool is [Cobbler](https://www.cobblerd.org/) - it installs an OS and it knows about a few CM tools, so it can configure the new machine to hook up to it.

Configuration management is not about Orchestration, which is about doing multiple things in parallel. This does have a place in a Configration Managed environment. You may want to trigger all your agents to check in after doing a major update for example. This will be a seperate tool, although it may be provided by the same developers (Puppet and MCollective for example).
