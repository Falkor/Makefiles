-*- mode: markdown; mode: auto-fill; fill-column: 80 -*-
`README.md`

Copyright (c) 2012 [Sebastien Varrette](mailto:<Sebastien.Varrette@uni.lu>) [www](http://varrette.gforge.uni.lu)

        Time-stamp: <Wed 2012-10-03 00:03 svarrette>

-------------------

# LaTeX project 


## Git Branching Model

The Git branching model for this repository follows the guidelines of [gitflow](http://nvie.com/posts/a-successful-git-branching-model/). 
In particular, the central repository holds two main branches with an infinite lifetime: 

* `production`: the branch holding tags of the successive releases of these slides
* `devel`: the main branch where the sources are in a state with the latest delivered development changes for the next release. This is the *default* branch you get when you clone the repo, and teh one on which developments will take places. 

You should therefore install [git-flow](https://github.com/nvie/gitflow), and probably also its associated [bash completion](https://github.com/bobthecow/git-flow-completion).

      $> apt-get install git-flow # On Debian-like systems
      
      $> brew install git-flow    # On Mac-OS using Homebrew

Also, to facilitate the tracking of remote branches, you need to install [grb](https://github.com/webmat/git_remote_branch), typically via ruby gems: 

      $> gem install git_remote_branch

Then, to make your local copy of the repository ready to use my git-flow workflow, you have to run the following commands once you cloned it for the first time:

      $> make setup

## Compilation 

Once your local copy of the repository is configured, simply run `make` to
generate the PDF file.  
You'll find the latest releases of the slides in the `release/` folder

# Advanced operations

## Releasing mechanism

The operation consisting of releasing a new version of this repository is automated by a set of tasks within the `Makefile`. 

In this context, a version number have the following format: 

      <major>.<minor>.<patch>-b<build>
      
where:

* `<major>` corresponds to the major version number
* `<minor>` corresponds to the minor version number
* `<patch>` corresponds to the patching version number
* `<build>` states the build number _i.e._ the total number of commits within the `master` branch. 
      
Example: `1.0.0-b28`

The current version number is stored in the file `VERSION`. __/!\ NEVER MAKE ANY MANUAL CHANGES TO THIS FILE__

For more information on the version, run:

     $> make versioninfo

If a new  version number such be bumped, you simply have to run:

      $> make start_bump_{major,minor,patch}

This will start the release process for you using `git-flow`. Probably after that, the first things to do is to change within the main LaTeX document the version number and commit this change. 
Then, to make the release effective, just run: 

      $> make release

it will finish the release using `git-flow`, create the appropriate tag in the `production` branch and merge all things the way they should be. 
Also, you will have the generated PDF for the freshly released version as a file named `release/intro_HPC_platforms_v<major>.<minor>.<patch>-b<build>.pdf`.



