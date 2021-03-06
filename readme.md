## Capistrano-Deploy-Tagger

### About

Capistrano-Deploy-Tagger creates and updates certain lightweight tags in Git each time you perform a deploy.

There are two types of tag, the first defaults to `inproduction`, and is always updated to refer to the revision that was just deployed. This gives you a tag which always points to the latest release to production, or to a staging or test server (in which case you can configure the tag to be intest or instaging in your test or staging deploy script). This can be used in many ways, for example, in an autoscaling environment, to clone the correct release of software.

The second type of tag is a timestamp, and is applied to every revision that is deployed to a server, giving you an easy way to see exactly which revisions have been deployed to a server in the past. This is helpful if attempting to diagnose issues that may not have become immediately apparent.  The prefix is configurable here as well, so you can distinguish between different environments/deploy scripts if necessary (default is "deploy-YYYYMMDD-hh-mm-ss").

__Note__: Capistrano 3 is completely incompatible with Capistrano 2, and therefore Capistrano-Deploy-Tagger will not function with Capistrano 3. If you are using Capistrano 3, you can use [Cap-Deploy-Tagger](https://github.com/forward3d/cap-deploy-tagger), however note that the configuration is not compatible between Capistrano-Deploy-Tagger and Cap-Deploy-Tagger.

### Installing

  - `gem install capistrano-deploy-tagger`

In your deploy.rb:

  - `require 'capistrano/deploy/tagger'`

Now when you run a standard `cap deploy`, upon success you will see the following line in your output:

  - [Capistrano-Deploy-Tagger] Updating deployment git tags...

### Overriding Defaults

By default, the first tag which represents the latest deploy is named `inproduction`. You can change this globally, or for certain cap tasks by setting the following in your `deploy.rb`:
  
  - `set :latest_deploy_tag, "your_tag_name_here"`

Likewise, the prefix for the timestamp tag (by default `deploy`) can be changed globally, or for certain cap tasks or environments by setting the following in your `deploy.rb`:
  
  - `set :latest_deploy_timestamp_tag_prefix, "your_timestamp_tag_prefix_here"`

You can also change the timestamp format (by default `%Y%m%d-%H%M-%S`), again globally or for specific cap tasks, by setting the following in your `deploy.rb`:

  - `set :latest_deploy_timestamp_tag_format, "%Y%m%d-%H%M%S"`

where the format is any valid `strftime` format.

You can also disable updating tags, again globally or for specific cap tasks, by setting the following in your `deploy.rb`:

  - `set :update_deploy_tags, false`

You can also disable timestamp type tags by setting the following in your `deploy.rb`:

  - `set :update_deploy_timestamp_tags, false`

### Special Usage

You can force Capistrano to deploy from the `inproduction` tag, and prevent Capistrano-Deploy-Tagger from updating any tags by running your deployment like so:

  - `cap automatic deploy`

The `automatic` task overrides `:branch` to set it to the value of `:latest_deploy_tag`, then sets `:update_deploy_tags` to `false`.

This becomes useful if you have an automated deployment system, for example AWS Autoscaling, and need to clone the revision currently in production, rather than just pulling HEAD.

### Notes

Originally I wanted to allow the timestamp tags to be deleted, keeping only a configurable amount of the most recent, however there are problems with this. When another user pulls the repository, they pull down the tags. If one of those tags is then deleted, they retain their local copy of it, and things then become confusing for everyone. If you update the revision a tag refers to, this doesn't seem to be an issue however. If this changes in the future I'll take another look.
