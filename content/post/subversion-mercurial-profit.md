+++
date = "2013-12-24T12:00:00"
draft = "false"
title = "Subversion, Mercurial, ..., Profit!"
slug = "subversion-mercurial-profit"

+++

## tl;dr:

Workflow to use Subversion and Mercurial version control side by side.

# Preface

Distributed source code management (DSCM) is very popular these days and might take over the world but the current reality is most companies still cling to centralized source control. Most often you will find [Subversion (svn)](http://subversion.apache.org/) or for the unlucky even [CVS](http://cvs.nongnu.org).

I won't reiterate the advantages of distributed vs. centralized version control but if you are interested watch this video of [Linus Torvalds trashing SVN](http://youtu.be/4XpnKHJAok8).

After going through the five stages of grief lets start with **acceptance**. Here is a list of the stuff that makes centralized version control a bit painful for me.


	* All branches are public
	* All commits are public
	* History requires connection
	* Switching branches is slow
	* Complete checkout with branches and tags unnecessary big


With that list in mind lets leverage the flexibility of DSCM to minimize those pain points.

My goals:


	* Separation of local and remote version control
	* Complete offline history with branches and tags
	* Synchronization with central repository
	* Push to central repository using native client


I want to live two version control lives. Central repository for working with team members and representing the source of truth plus a rouge local version control regime where I can experiment, branch, fix, delete without affecting other team members.

To achieve these goals I will now present my current work flow in a [Subversion](http://subversion.apache.org/) vs. [Mercurial](http://mercurial.selenic.com/) scenario.

The flow itself is technology  agnostic so implementing the same flow with [CVS](http://cvs.nongnu.org) and [git](http://git-scm.com/) is straight forward.

# Pre-Requirements

* [Subversion](http://subversion.apache.org/)
* [Mercurial](http://mercurial.selenic.com/)
* [hgsubversion](http://bitbucket.org/durin42/hgsubversion/overview/) (Mercurial plugin for svn integration)


## Subversion and Mercurial

[Subversion](http://subversion.apache.org/) and [Mercurial](http://mercurial.selenic.com/) installation packages and instructions can be found at the corresponding websites. I won't go into the details here as they are pretty standard.

One tip though: If you are installing [Subversion](http://subversion.apache.org/) on OS X using brew, you will need to install the [Subversion](http://subversion.apache.org/) python bindings as well.

<script src="https://gist.github.com/olsio/faebdb20dcb6970a2ae1.js"></script>

## hgsubversion

### Installation

Mercurial offers one time svn conversion out of the box but as Subversion will stay my source of truth I use [hgsubversion](http://bitbucket.org/durin42/hgsubversion/overview/) as a svn integration layer with push and pull support. I don't use the [hgsubversion](http://bitbucket.org/durin42/hgsubversion/overview/) push functionality as I don't want to risk leaking Mercurial branches or other meta data to svn but you could easily do so and avoid using svn all together.

<script src="https://gist.github.com/olsio/909e91e113d1cff7c772.js"></script>

#### Testing
<script src="https://gist.github.com/olsio/eb6ac21e7a0c1d6a804b.js"></script>

# Workflow

## Overview

The workflow itself looks like the normal DSCM flow of **pull**, **work**, **push** but each action is taking place in its own directory and repository clone.

![Overview diagram showing pull, work, push segments](/images/2013/12/overview.png)


### Pull
The pull directory is a staging directory for using [hgsubversion](http://bitbucket.org/durin42/hgsubversion/overview/) to pull changes form svn into an initial hg repository.

This hg repository keeps an exact copy of svn and is the source of truth. All other repositories are clones from **pull** and sync the incoming svn changes from here.

### Work

Work is initially a clone of pull but completely separate. Its focus is purely on introducing changes, fixing bugs and working with hg. Here all features of Mercurial can be used to track work like feature branches, rebasing, many small commits or whatever your style is.

### Push

Push is the final staging area for pushing changes back to svn.

Simply put **push** contains real svn checkouts and a clone of **work** in the same directory.

To avoid having the complete svn tree with all tags and branches on disk I only checkout specific svn branches on demand. I clone my work repository into those checkouts.

Updating to the same branch in svn and hg should result in the same content without any conflicts.

To introduce changes done in **work** I just update to my feature branch and override all files that changed. Now svn sees the changes and can be used to commit the changes to the central svn repository.

# Walk through

After I covered the requirements and gave an overview on how it works here comes the complete flow shown on a contrived rubbish example.

## Init directory structure

![Diagram of directory structure pull, work, push](/images/2013/12/directory_structure.png)

Starting with the initial directory structure.

<script src="https://gist.github.com/olsio/41bc68e5e0d55906022a.js"></script>

## Import svn project
You use the usual hg notation for clone as it will automatically fall back to svn.

### Quick and easy

<script src="https://gist.github.com/olsio/024dd944c07f7b5fa8fe.js"></script>

This assumes that you use the normal svn branches/tags/trunk layout. There are some parameters to map non-standard layouts but I never used them.

### My preferred manual approach

For password protected svns I prefer to set up the hg repository manually to provide the svn path and user + password before starting the clone.

<script src="https://gist.github.com/olsio/6a2b818f1b66cbb19d8f.js"></script>

This will pull the complete history and branch/tag structure of the svn repository. This will take some time, depending on your svn size. I had cases that took several hours to complete.

This is a one-time effort and works fast afterwards.

## Clone work

<script src="https://gist.github.com/olsio/024dd944c07f7b5fa8fe.js"></script>

## Do some work

Ok let's checkout trunk, create a feature branch and add orm support.

<script src="https://gist.github.com/olsio/25ed09e098e2289cace9.js"></script>

## Init Push

Before pushing to svn we need to checkout the branch we are interested. So I checkout trunk, clone my work directory that contains the feature branch.

As hg won't clone into a directory that is not empty I clone it first to a temp directory and then just move the .hg directory into the svn checkout.

<script src="https://gist.github.com/olsio/fe275e7215f2460962c3.js"></script>

## Push to svn

To finally push the changes to svn we update to the feature branch and replace all files. Using the svn client we can then push the changes to svn.

<script src="https://gist.github.com/olsio/cc6929d45058df2f32b8.js"></script>

## Pull changes from svn and close feature branch

Now we have come full circle and have our changes in svn and can pull them back in from pull to work etc. To tidy up I am also closing the feature branch as it is not necessary anymore.

<script src="https://gist.github.com/olsio/21c06ff628dc66a51288.js"></script>

# Conclusion

This approach might seem like overkill but having the security of many commits and easy revert without sharing all those mini steps and potential conflicts with the team is liberating.

Personally I have semi-automated some steps using scripts (e.g. recursive pull for **pull** -> **work** -> **push**) to avoid repetitive tasks and also use tools like [tortoisehg](http://tortoisehg.bitbucket.org/) and [tortoisesvn](http://tortoisesvn.net/).