% OpenJDK Developers' Guide

# Introduction

Welcome to the OpenJDK Developers' Guide!

The OpenJDK Community is the place to collaborate on open-source implementations of the Java Platform, Standard Edition, and related projects.  It was created in November 2006, when initial portions of the JDK source code were published under the GPLv2 license.

In order to work together efficiently, clear directions are sometimes needed to avoid misconceptions and to align developers' views of terminology and process. The OpenJDK Community is a fairly pragmatic place. "Do the right thing" is most often the right course of action. Still, if people do things in the same right way then everyone's work becomes more transparent and easier for others to follow. For this reason most parts of the development process have standard flows that are the recommended ways to do things.

The goal of this guide is to answer questions that developers of the JDK might have around development process, tooling, standards, and so forth. The formal rules and processes are described in other documents, such as [JEP 1](https://openjdk.java.net/jeps/1) for the JDK Enhancement-Proposal & Roadmap Process, and [JEP 3](https://openjdk.java.net/jeps/3) for the JDK Release Process. This guide is meant to be a complement to such documents, with tutorials and examples for how to follow these rules and how to work together with the rest of the OpenJDK Community.

There are many common use cases that aren't detailed in the formal process. This guide suggests how to work in such cases.

# Repositories

This section describes the OpenJDK repository terminology and naming scheme. It also includes minimal instructions to acquire source from the OpenJDK repository, [`https://hg.openjdk.java.net/`](https://hg.openjdk.java.net/).

> ---
> Note that source may be available from other locations, for example `src.zip` from a full JDK distribution. However, OpenJDK contributions must use source from the [OpenJDK Mercurial repository](https://hg.openjdk.java.net/) since other source distributions may contain older code or code which differs due to licensing.

> ---

Operations which are performed repeatedly, such as creating changesets, merging, and pushing are described in [Producing a Changeset](#producing-a-changeset).

* [Terminology and Naming Scheme](#terminology-and-naming-scheme) defines the nomenclature used by OpenJDK.
* [Installing and Configuring Mercurial](#installing-and-configuring) enumerates the required software, provides links for download, and describes basic installation and configuration.
* [Cloning](#clone) covers the procedure for obtaining private copies of source repositories.

This document assumes familiarity with the first two chapters of the free on-line book [Mercurial: The Definitive Guide](http://hgbook.red-bean.com).

## Terminology and Naming Scheme

The OpenJDK code base for all [Projects](https://openjdk.java.net/bylaws#_6) is stored in Mercurial repositories which contain the source files and their change history. Some Projects may choose to organize their code into multiple, possibly related, Mercurial repositories. For instance, [JDK 9](https://openjdk.java.net/projects/jdk9) uses a forest of multiple related repositories which contain components of the entire JDK. Projects which are based on the JDK, such as [IcedTea](https://openjdk.java.net/projects/icedtea) and [Jigsaw](https://openjdk.java.net/projects/jigsaw) also use this model. In contrast, [Code Tools](https://openjdk.java.net/projects/code-tools) uses an unrelated repository for each tool and [Graal](https://openjdk.java.net/projects/graal) uses only a single repository. Regardless of how a Project has chosen to store their code, each [Contributor](https://openjdk.java.net/bylaws#contributor) clones the repository associated with the code they are modifying.

> ---
> Consult the Project's documentation or [mailing list](https://mail.openjdk.java.net) to determine the appropriate repository, development conventions, and helpful tools.

> ---

This is the typical development model:

::: {style="text-align:center;"}
![Diagram of server repos and user's clone](devModel.gif)
:::

A Contributor creates a _clone_ (a local copy called "9dev") of a read/write repository ("jdk9/dev") which resides on the OpenJDK Mercurial server. They work on their change in the clone and locally _commit_ a changeset. After the new changeset(s) are verified, they're either _pushed_ directly from the clone or _exported_ and delivered to a [Committer](https://openjdk.java.net/bylaws#committer) who can perform the push into the server repository for all to see.

> ---
> The use of _gate repositories_ was eliminated when the Mercurial servers were upgraded in March 2014.

> ---

The repositories use the following naming scheme:

> `<project>/<component_path>`

where

> ------------------  ----  ----------------------------------------------------
> _project_            ::   the short name of an OpenJDK [Project](https://openjdk.java.net/bylaws#_6) such as "jdk9", "openjfx", or "sumatra"
> _component_path_     ::   the path to a repository containing the code base as designated by the [Project Lead](https://openjdk.java.net/bylaws#project-lead).
> ------------------  ----  ----------------------------------------------------

## Installing and Configuring Mercurial

Mercurial is a free, cross-platform, distributed source management tool. Source bundles and binary packages for Mercurial are available at [https://www.selenic.com/mercurial/wiki/index.cgi](https://www.selenic.com/mercurial/wiki/index.cgi/Mercurial). The OpenJDK repositories recommend installation of Mercurial 2.6.3 (or later). A Mercurial installation is sufficient to clone a repository. Contributors who wish to submit changes will need some additional configuration as described below.

Some Projects may recommend additional tools or scripts that help with repository manipulation and code development. For instance, in JDK 9, the utility script `common/bin/hgforest.sh` may be used to apply commands to all the repositories in the [forest](#forest). Popular extensions for OpenJDK developers include [jcheck](https://openjdk.java.net/projects/code-tools/jcheck/), [trees](https://openjdk.java.net/projects/code-tools/trees/), and [Mercurial Queues](http://hgbook.red-bean.com/read/managing-change-with-mercurial-queues.html) (mq). Note that `trees` is enabled on the OpenJDK Mercurial server.

Create and edit the `~/.hgrc` file to minimally contain the following entry:

    [ui]
    username = <openjdk_username>

_openjdk\_username_ is a plain lowercase, alphanumeric token (not an e-mail address) with twelve characters or less. The first character should be alphabetic. This username will be publicly visible in all Mercurial changeset logs. It will be used to verify that the changeset author is at least an [Author](https://openjdk.java.net/bylaws#author) for the Project and that the person pushing the changeset is at least a [Committer](https://openjdk.java.net/bylaws#committer). It is recommended that the _openjdk\_username_ be somehow related to the Author's full name, such as the first character of the Author's first name followed by the Author's last name. Refer to the [Becoming an Author](../projects/index.html#project-author) section of the [Project](../projects/index.html) page for additional information.

An alphabetical list of all of the assigned _openjdk\_usernames_ may be found on the [OpenJDK people](https://db.openjdk.java.net/people) list. The [Census](../census) shows the structure of the OpenJDK Community.

#### Verifying the Configuration

After installing and configuring Mercurial, validate the configuration using the following steps.

#. Verify that Mercurial is version 2.6.3 (or newer).

       $ hg version
       Mercurial Distributed SCM (version 2.9)
       (see http://mercurial.selenic.com for more information)

       Copyright (C) 2005-2014 Matt Mackall and others
       This is free software; see the source for copying conditions. There is NO
       warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

#. Verify that the list of enabled extensions includes fetch and mq.

       $ hg help

#. Verify that the `~/.hgrc` configuration looks correct. Minimally it should contain the following entries:

       $ hg showconfig
       ui.username=iris

At this point, it should be possible to start retrieving source from the repositories.

## Cloning

With Mercurial each developer works with a clone of the repository which is a snapshot of the files at the time the clone was taken. To update the clone, see [Producing a Changeset](#producing-a-changeset).

#### ... a Sandbox Repository {#cloneSandbox}

In addition to the Project repositories, there are some test repositories that may be used to run test commands against Mercurial without fear of causing damage to development source. Use them freely but with discretion; content in them may be deleted at any time.

    $ mkdir sandbox; cd sandbox
    $ hg clone http://hg.openjdk.java.net/sandbox/box
    destination directory: box
    requesting all changes
    adding changesets
    adding manifests
    adding file changes
    added 23 changesets with 24 changes to 5 files
    4 files updated, 0 files merged, 0 files removed, 0 files unresolved
    $ du -s box
    46      box

#### ... a Forest {#cloneForest}

If a Project uses a forest, It is strongly recommended for developers to clone an entire forest, rather than a single repository. This is the only means to ensure consistency in builds. The following examples illustrate two alternatives for cloning the entire _jdk9/dev_ forest into the directory `9dev`.

#. To clone the forest using the [trees](https://openjdk.java.net/projects/code-tools/trees/) extension just use `tclone`:

       $ hg tclone http://hg.openjdk.java.net/jdk9/dev 9dev

#. To clone the forest using `get_source.sh`, first clone the main tree:

       $ hg clone http://hg.openjdk.java.net/jdk9/dev 9dev
       requesting all changes
       adding changesets
       adding manifests
       adding file changes
       added 997 changesets with 1477 changes to 138 files
       updating to branch default
       82 files updated, 0 files merged, 0 files removed, 0 files unresolved

   Then clone the repositories in the forest:

       $ cd 9dev
       $ sh ./get_source.sh

Regardless of how the forest was cloned, this is the resulting populated forest.

    $ du -s
    934532  .
    $ ls
    ASSEMBLY_EXCEPTION  hotspot    LICENSE   README-builds.html
    common              jaxp       make      test
    configure           jaxws      Makefile  THIRD_PARTY_README
    corba               jdk        nashorn
    get_source.sh       langtools  README

#### ... a Single Repository {#cloneSingle}

If the source for the Project is contained within a single repository or reading a limited portion of the source is the only goal, it is possible to clone a single repository (even if it's part of a forest). For instance, this example shows how to clone the `langtools` repository from _jdk9/dev_ into the default destination directory.

    $ hg clone http://hg.openjdk.java.net/jdk9/dev/langtools
    destination directory: langtools
    requesting all changes
    adding changesets
    adding manifests
    adding file changes
    added 2289 changesets with 21194 changes to 7004 files
    updating to branch default
    6212 files updated, 0 files merged, 0 files removed, 0 files unresolved
    $ du -s langtools
    84396   langtools

# Mailing Lists

The mailing lists are the key communications mechanism for all OpenJDK work. All participation in an OpenJDK project starts with joining the relevant mailing list. A subscriber to an OpenJDK mailing list is referred to as a Participant in the [Bylaws](https://openjdk.java.net/bylaws). As a general recommendation we suggest to subscribe to [announce](https://mail.openjdk.java.net/mailman/listinfo/announce), [discuss](https://mail.openjdk.java.net/mailman/listinfo/discuss), and the `-dev` lists covering your explicit area of interest. All OpenJDK mailing lists are found here:

> [`mail.openjdk.java.net`](https://mail.openjdk.java.net/mailman/listinfo)

The OpenJDK community is a friendly place. To keep it that way it's important to keep a professional tone in emails and be aware that the community is global. Many different people with different backgrounds collaborate in these lists. Even though English is the required language for all lists, many Participants speak other languages as their native language. A high tolerance for non-perfect English is expected from anyone joining these lists.

You must be in the list to send to the list. Some lists are moderated to keep the content on topic. Each list has its own archive where you can browse older conversations on the list.

There are a few different types of lists. The list name has two parts to explain what the list is intended for, `<name>-<suffix>`. The name often refers to the project that owns the list or a specific area of interest that the list focuses on. The suffix is explained below. Not all projects or areas have all types of lists described here.

> `-dev`
> :    Technical discussions around the implementation of the project artifacts. This is also where code reviews happen.

> `-use`
> :    Technical discussions around the usage of the project artifacts.

> `-discuss`
> :    General discussions around the project. The special case `discuss(at)openjdk.java.net` is used for general discussions around the OpenJDK project. Discussions around new project proposals usually happens here.

>  `-changes`
> :    Changeset notifications from the source code repositories maintained by the project.

> `-announce`
> :    General project announcements. These lists are tightly moderated and are expected to be low traffic. The special case `announce(at)openjdk.java.net` is used for announcements for the OpenJDK project.

> `-experts`
> :    Expert group discussions. The list is restricted; only members of the expert group can subscribe.

> `-observers`
> :    Open for anyone to subscribe to see what the experts are discussing and potentially to have some dialog with other non-experts. There is no guarantee that an expert is subscribed to the `-observers` list or will see any responses on that list.

> `-comments`
> :    Used by observers to directly provide feedback/comments to the experts (typically a lead will process the comments list and forward things on to the experts list).

# Code Conventions

* [Java code conventions](https://www.oracle.com/technetwork/java/codeconvtoc-136057.html)

# Change Planning and Guidelines

This section describes the development process of identifying a bug or enhancement (formerly "Request for Enhancement" (RFE)) and providing a solution. The instructions assume that the [Contributor](/bylaws#contributor) already has a working build environment and has some familiarity with an existing OpenJDK [Project](bylaws#_6) and its repositories.

## Fixing a Bug

This is the list of steps which should be performed when fixing a small bug. Small bugs include typos in code or specification, algorithm improvements for correctness or performance, and code changes required to correctly implement the specification.

> ---
> Some steps refer to operations which can not be performed directly without the assistance of a [Project Committer](https://openjdk.java.net/bylaws#committer). For example, any changes to the [bug database](https://bugs.openjdk.java.net) fall into this category. Since these steps are required, [Contributors](https://openjdk.java.net/bylaws#contributor) are urged to work with their [Sponsors](/sponsor/) to complete these tasks.

> ---

For the purposes of brevity this document will use the term "bug" to refer to both bugs and enhancements unless otherwise noted. Hence "fix for a bug" could also imply "implementation for an enhancement".

#. **Discuss the intended change**

   Send an e-mail to the appropriate development mailing list for the Project that maintains the code. The e-mail should have a subject line of the form:

       6543210: My favorite bug

   where `6543210` is replaced with the actual bug id number or "[NEW BUG]" if the bug id is not known and `My favorite bug` is replaced with the bug's summary.  The message should describe the intended change, which components may be affected, and any other risks or concerns.

#. **Does a bug id exist for the work?**

   **Yes**
   :   Continue

   **No**
   :   Create a bug in an appropriate component/sub-component.

#. **Set the bug status to "Open"**

   This communicates intent to fix the bug to other members of the Project. It also sets the expectation for downstream teams such as SQE and JCK that the bug will be fixed in an upcoming integration.

#. **Does the fix for the bug require a specification change, directly affect an external interface, or otherwise have a compatibility impact?**

   **Yes**
   :   Submission of a CSR request is required. The CSR must be [approved](#approved) before the bug fix is pushed to a feature release or update release repository. The work may begin concurrently with the CSR review, but may need to be modified in response to CSR feedback.

   **No**
   :   Continue

#. **Fix the bug**

   Assuming that the development team approves of the intended approach, begin working on the code using the latest source available from the appropriate OpenJDK Project [repository](https://hg.openjdk.java.net).

#. [**Is it possible to write a test to detect the bug?**]

   **Yes**
   :   For bugs, provide a [jtreg](../jtreg/) regression test as part of the changeset. For enhancements that directly affect one or more exported interfaces, add an adequate set of jtreg unit tests as part of the changeset. By convention all regression and unit tests should contain a [\@bug tag](https://openjdk.java.net/jtreg/tag-spec.html#INFORMATIONAL_TAGS9) referencing at least one bugid.

   :   An entirely new test (or tests) may not be required. For example, if the bug is an existing regression test failure, then when fixing the bug you should just add the new bug ID to the list of space-delimited bugs in the [\@bug tag](https://openjdk.java.net/jtreg/tag-spec.html#INFORMATIONAL_TAGS) of the failing regression test, even if the test did not need to be updated.

   **No**
   :   An explanation for why a test is unnecessary or infeasible is required. Such an explanation is recorded by adding a label, and possibly a comment, to the bug report. The label has the prefix **`noreg`** for regression tests and **`nounit`** for unit tests. The suffix of the label is described in detail in the [JBS Label Dictionary](#noreg)

#. **Is modification of shared Java code needed?**

   **Yes**
   :   It is often sufficient to build and test on a single platform, but sometimes it is not. Use discretion.

   **No**
   :   Continue

#. **Is modification of shared C code needed?**

   **Yes**
   :   Build and test on at least one instance of all three of the supported operating systems (Solaris, Linux, and Windows).

   **No**
   :   Continue

#. **Is modification of C or Java platform-specific code needed?**

   **Yes**
   :   Build and test on all relevant platforms.  Code under `src/solaris` builds on Solaris, Linux, and MacOS X despite its name.

   **No**
   :   Continue

#. **Run relevant regression and unit tests on all relevant platforms**

   These include tests for external interfaces as well as other kinds of tests, e.g., HotSpot tests that use internal verification mechanisms.

#. **Run relevant JCK tests on all relevant platforms**

   Running JCK tests is particularly important if the change may have unexpected side-effects.

#. **Request a review of the changes by sending an e-mail to the development alias**

   A patch can be submitted as described in [Contributing](../contribute/). Alternatively, a ["webrev"](webrevHelp.html) may be generated and uploaded to the [community code review](https://cr.openjdk.java.net/) server. The complete webrev generation and upload procedure is described at [https://cr.openjdk.java.net](https://cr.openjdk.java.net/).

   Changeset pushes before the [Feature Complete](/projects/jdk8/milestones#Feature_Complete) require at least one [Reviewer](/bylaws#reviewer); pushes after the Feature Complete require at least two Reviewers. In either case, the more the merrier. Some teams may require more Reviewers. Check with members of the Project.

   Reviewers should examine not only the code being added or changed but also the relevant unit or regression tests.

   A change may require multiple Reviewers because it affects multiple areas.  Reviewers should be aware that they take full responsibility for the appropriateness and correctness of any changes in their area of expertise.  If something goes wrong (e.g., the build breaks) and the change&apos;s author is unavailable, they may be asked to deal with the problem.  Potential Reviewers are encouraged to refuse to review code for which they are not qualified.

#. **Create a changeset**

   Follow the instructions in [Producing a Changeset](#producing-a-changeset).

#. **Update the bug content**

   Bug descriptions and comments should be written in a professional manner.

#. **Push the changeset into the Project's forest**

   Follow the instructions in [Producing a Changeset](#producing-a-changeset). If working with a Sponsor, send the changeset to the development mailing list so that they can handle the final push.

   The push will trigger a update to the bug which will make the following changes:

    * Set the bug's "Status" to "Resolved" and "Resolution to "Fixed".
    * Set the bug's "Fix Version/s" to an appropriate release.
    * Set the bug's "Resolved in Build" to "team".
    * Add a Comment containing a reference to the changeset.

_Congratulations!_ Your changeset will now make its way towards a promoted build. When the changeset becomes part of a promoted build, the bug's "Resolved in Build" will have a value of \"b\[1-9\]\[0-9\]&ast;\" to indicate the build number.

# Producing a Changeset

This section is confined to the actual Mercurial mechanics required to produce a changeset:

* [Setting a JDK User Name](#setting-a-jdk-user-name) reminds the user to confirm proper configuration.
* [Creating](#creating) includes instructions for the required format of a changeset comment and describes how to commit files to a changeset.
* [Merging](#merging) describes how to pull changes from a parent forest or clone and incorporate the changes into the current working set of files.
* [Pushing](#pushing) covers configuring ssh, setting the `default-push` path, and pushing changesets.

## Setting a JDK User Name

Ensure that _ui.username_ has a value in the `~/.hgrc` file as described in [Verifying the Configuration](#verifying-the-configuration).

## Creating

The timing for creating a changeset is important. Creating the changeset long before it gets pushed into the parent repository may require complex merges. If a changeset is created before sufficient review or testing, a rollback may be required and a new changeset may be required to correct previous mistakes. The [mq extension](http://hgbook.red-bean.com/hgbookch12.html#x16-26500012) is recommended for managing changes before they become committed to a changeset.

In the examples below, the script `common/bin/hgforest.sh` can be used to apply the Mercurial command to all the repositories in the forest. So when you see _**`hg`**_, if you are dealing with one repository, just use "`hg`", if it's a forest, use "`sh common/bin/hgforest.sh`".


Each repository in the forest is managed independently. After editing files in the individual cloned repositories of the forest, the `hg status` command may be used to see the changes in a single repository.

    $ hg root
    /u/iris/sandbox/box
    $ hg status
    ? duke/images/DukeTubbingSmall.png
    $ hg add duke/images/DukeTubbingSmall.png
    $ hg status
    A duke/images/DukeTubbingSmall.png

To see changes made to the repositories use _**`hg`**_ `status`:

    $ hg status
    [.]
    A duke/images/DukeTubbingSmall.png

In this example, the repository was previously cloned as described in [Cloning a Sandbox Repository](#clone). A new file `DukeTubbingSmall.png` was added to a new subdirectory.

#### Formatting a Changeset Comment

A single change is described by a block of text of the following form:

    <bugid>: <synopsis-of-symptom>
    Summary: <summary-of-code-change>
    Reviewed-by: <reviewer>+
    Contributed-by: <contributor-email>

There may be more than one _bugid_ line, but there must be at least one.

The _summary_ line is optional, but authors are strongly encouraged to include one if the nature of the change is not obvious from the synopsis. It's just one line, meant to give the reader a clue as to how the code changed. A more complete description of the change belongs in the bug report.

A _reviewed-by_ line is required. Reviewers must have the ability to deal with any adverse consequences of the change, and so must themselves be authors. They are therefore identified by their OpenJDK usernames rather than full e-mail addresses.

The _contributed-by_ line is optional. If present, it is a list of comma-separated email addresses. It should be included only when the author or authors of the change do not have commit rights to the target repository and thus would not otherwise receive acknowledgment.

There will be exceptions for merge changesets, tag changesets, etc.

Example:

    1234567: NPE thrown on FileInputStream("")
    Summary: Rewrite precondition-checking code in io.c
    Reviewed-by: mr
    Contributed-by: Ben Bitdiddle <ben at bits.org>

If a changeset contains multiple unrelated changes (this is frowned upon, but may happen from time to time) then its comment will contain multiple blocks of the above form, separated by blank lines.

The required format of the comments will be enforced whenever the changeset is pushed into the JDK master or team repository forests. Other Projects may copy these conventions, adopt some other conventions, or have no conventions, depending upon their goals.

#### Committing a Changeset

The following commands commit all of the changes in a repository to a changeset.

    $ cat ../message
    1111111: Missing Duke gif
    Summary:  Add missing file
    Reviewed-by: iag
    $ hg commit -l ../message
    $ hg toutgoing
    [.]
    comparing with http://hg.openjdk.java.net/sandbox/box
    searching for changes
    changeset:   23:fb12953f3a35
    tag:         tip
    user:        iris
    date:        Wed Dec 12 21:05:59 2007 -0800
    summary:     1111111: Missing Duke gif

## Merging

It is often necessary to merge local changes with those made in the parent repositories. The first step in a merge process is to retrieve (or pull) the collection of changesets which have been pushed since the last merge or initial clone. If there if there are merge conflicts, then they must be resolved. [Chapter 3](http://hgbook.red-bean.com/hgbookch3.html#x7-530003) of the Mercurial book contains detailed information on the merging process.

There are two basic ways to update the working set files in the repositories:

Option 1: _**`hg`**_ `pull`

> One way to merge the parent repository with the working set of files is to use _**`hg`**_ `pull` all by itself. This option allows merging off-line or at a later time.
>
>     $ hg pull
>     [.]
>     pulling from http://hg.openjdk.java.net/sandbox/box
>     searching for changes
>     no changes found
>
> In Mercurial, pulling changesets will not update or merge into the working set of files. To update the clone, run _**`hg`**_ `update`. If the update reports conflicts, run _**`hg`**_ `merge` to resolve them.

Option 2: _**`hg`**_ `fetch`

> Alternatively, use _**`hg`**_ `fetch` to pull the changes, update the working set files, and create simple merge changesets as necessary. The fetch extension is distributed with Mercurial but needs to be enabled. Edit the `.hgrc` to include the following entries:
>
>     [extensions]
>     fetch=
>
> Once the fetch extension has been enabled, _**`hg`**_ `fetch` may be invoked as follows:
>
>     $ hg fetch
>     [.]
>     pulling from http://hg.openjdk.java.net/sandbox/box
>     searching for changes
>     no changes found

> ---
> Actual file merging will be done with the selected Mercurial merging tool see [MergeProgram](https://www.selenic.com/mercurial/wiki/index.cgi/MergeProgram) for the details on how to define the selected merge tool in ` ~/.hgrc`.

> ---

## Pushing

In order to push changesets into the parent repository, some additional configuration is required. The following sections describe the operations that will be performed by users with push access.

#### Generating an SSH Key

All pushes require an ssh key which must be installed on the Mercurial server. The `ssh-keygen` command generates an ssh key. The `-b` option overrides the default number of bits for the key. Allow a few minutes to generate a 4096 bit key; a key of at least 2048 bits is recommended. While it is possible to use ssh without a passphrase, this is **strongly** discouraged. Empty or insecure passphrases may be reset using `ssh-keygen -p`; this does not change the keys.

    $ ssh-keygen -t rsa -b 4096
    Enter file in which to save the key(/u/iris/.ssh/id_rsa):
    Generating public/private rsa key pair.
    Enter passphrase(empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /u/iris/.ssh/id_rsa.
    Your public key has been saved in /u/iris/.ssh/id_rsa.pub.
    The key fingerprint is:
    md5 4096 c2:b7:00:e6:4b:da:ea:ec:32:30:f5:bd:12:26:04:83 iris@duke
    The key's randomart image is:
    +--[ RSA 4096]----+
    |    E.=          |
    |     . *         |
    |      o .   .    |
    |         + o     |
    |        S + .    |
    |       .   + .   |
    |        + + +..  |
    |       * . oo+.  |
    |      o . .o..   |
    +-----------------+

The `~/.ssh/id_rsa.pub` is a text file containing the public ssh key. This file should be mailed as an attachment along with the JDK username to [keys(at)openjdk.java.net](mailto:keys-at-openjdk.java.net). An administrator will install your key on the server and notify you on completion. This process may take a couple of days.

> ---
> Users behind a SOCKS firewall can add a directive to the `~/.ssh/config` file to connect to the OpenJDK Mercurial server:
>
>     Host *.openjdk.java.net
>     ProxyCommand /usr/lib/ssh/ssh-socks5-proxy-connect -h [socks_proxy_address] %h %p
>
> See the `ssh-socks5-proxy-connect` man page and `ssh-config` man page for more information. Other systems may require proxy access via other programs. Some Linux distributions provide the `corkscrew` package which provides ssh access through HTTP proxies.
>
> **It is recommended that all users check with their network administrators before installing any kind of TCP forwarding tool on their network. Many corporations and institutions have strict security policies in this area.**

> ---

#### SSH Shortcuts

The following section provides some tips for improving the usability of ssh-related operations.

* _Using SSH in multiple shells_

  To avoid having to constantly type in the passphrase, use the ssh-agent on your local client to cache your pashphrase:

      $ eval `ssh-agent`
      Agent pid 17450
      $ ssh-add
      Enter passphrase for /u/iris/.ssh/id_rsa:
      Identity added: /u/iris/.ssh/id_rsa(/u/iris/.ssh/id_rsa)

  The same ssh-agent process can be shared with multiple shells. There are various ways to do this. Bash users can accomplish this with the following code in `.bashrc`:

      if [ "$PS1" -a -d $HOME/.ssh ]; then
        if [ "x$SSH_AUTH_SOCK" = x ]; then
          eval `ssh-agent | grep -v 'echo Agent pid'`
          ssh-add
          trap "echo Killing SSH agent $SSH_AGENT_PID; kill $SSH_AGENT_PID" 0
        fi
      fi

  For secure operation, only start an ssh-agent when needed and kill it off when the shell completes. Test this by running `ssh 'hostname' echo hello` multiple times.

* _Logging in without a password_

  To avoid needing to constantly type in the password, add the public key to the list of ssh authorized keys.

      $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
      $ chmod 600 ~/.ssh/authorized_keys

#### Setting the `default-push` Path to the Server Repositories

<!--
#. Option 1: Add default-push path to every <code>.hg/hgrc</code>
-->

This is the typical development model:

:::{style="text-align:center;"}
![Diagram of server repos and user's clone](devModel.gif)
:::

Changesets need to be _pushed_ via ssh to the read/write repository which resides on the OpenJDK Mercurial server. The easiest way to do this is to have each repository define the "default-push" path in every repository's `.hg/hgrc` file. The `.hg/hgrc` file in is not a managed file - it is private to the repository. The following example defines the "default" and "default-push" paths for clones of the jdk9/dev team repository.

    [paths]
    default = http://hg.openjdk.java.net/jdk9/dev
    default-push = ssh://<JDK_username>@hg.openjdk.java.net/jdk9/dev

Given a `JDK_username` this simple script will attempt to do this for all the repositories:

    #!/bin/sh
    username=$1
    hgdirs="`find . -type d -name .hg`"
    for i in ${hgdirs}; do
      d="`dirname ${i}`"
      defpush="`(cd ${d} && hg paths default-push 2> /dev/null)`"
      if [ "${defpush}" = "" ] ; then
        defpath="`(cd ${d} && hg paths default 2> /dev/null)`"
        if [ "${defpath}" != "" ] ; then
          defpush="`echo ${defpath} | sed -e 's@http://\([^/]*/[^/]*/[^/]*\)/\(.*\)@ssh://$username\@\1/\2@'`"
          cp ${i}/hgrc ${i}/hgrc.orig
          echo "default-push = ${defpush}" >> ${i}/hgrc
          echo "Added default-push: ${defpush}"
        fi
      fi
    done
    for i in ${hgdirs}; do
      d="`dirname ${i}`"
      echo "(cd ${d} && hg paths)"
      (cd ${d} && hg paths)
    done
    exit 0

<!--
#. Option 2: Use the <code>defpath</code> ExtensionAnother way to setup the default-push path is to use the Mercurial defpath extension, which is available in /java/jdk/lib/hgext/defpath.py. To enable this extension add the following to the <code>~/hgrc</code> file:
<blockquote><pre>
[extensions]
defpath = /java/jdk/lib/hgext/defpath.py  # Or the pathname of a local copy
</pre></blockquote>
     The defpath extension actually adds two new Mercurial subcommands:
    defpath to operate upon a single repository and fdefpath for an entire
    forest. hg help defpath or hg help fdefpath will tell you more. For a
    freshly cloned open/closed forest the command you probably want is
<blockquote><pre>
$ hg fdefpath -dgv
</pre></blockquote>
     Be sure to try this with the -n flag first to see if it will do what
    you actually expect. If your OpenJDK username differs from your Oracle
    username then add -u name to the command line.
     The defpath extension defines the default-push path, which is the path
    that Mercurial uses by default in push operations.
-->

#### Pushing a Changeset

[Committers](https://openjdk.java.net/bylaws#committer) can use the _**hg**_ `push` command to propagate changesets into the repositories.

Most developers will only find a need to create changesets in one or two repositories. However, it is important that before any changesets are pushed, the corresponding forest pull and merge with the destination forest be performed; otherwise there is a risk of breaking the build.

    $ hg push

After the push has been accepted, an automatic e-mail notification will be sent to the [mailing list](https://mail.openjdk.java.net) associated with the repository. In most cases notifications are sent to the Project's _-dev_ mailing list. Some Projects with high traffic _-dev_ mailing lists use a dedicated _-changes_ list for notifications.

> ---
> Who has push access?
>
> All of a Project's [Committers](https://openjdk.java.net/bylaws#committer) can push to all of the the Project's repositories.
>
> Some Projects may chose to restrict the set of Committers with push to key repositories. For instance, JDK Release Projects restrict push access to MASTER repositories to Committers who are either integrators or members of the Release Engineering Team.
>
> Refer to the [Nominating a Contributor or Author to be a Committer](../projects/index.html#project-committer) section of the [Project](../projects/index.html) page for information about becoming a Project Committer.

> ---

# What Happens Next

This section describes what might happen after a changeset gets into the build, for example a bug might be filed or a backport could be requested.

## Filing a Bug

When a new failure is found in the JDK a bug should be filed to describe and track the issue. Depending on your role in the OpenJDK you can either use the [Bug Report Tool](https://bugreport.java.com/) or, if you are Author in an OpenJDK Project, report the bug directly in the [JDK Bug System](https://bugs.openjdk.java.net/). Try to make the bug report as complete as possible to make it easier to triage and investigate the bug. It's not the reporter's responsibility to set a correct priority, but a qualified guess is always better than a default value. To help with setting the right priority consider things like how the bug impacts the product and our testing, how likely is it that the bug triggers, how difficult is it to work around the bug if it's not fixed soon, and whether it's a recent regression, since that may break existing applications. Regressions are often higher priority than long standing bugs and may block a release if not fixed.

A few things to keep in mind when filing a new bug:

* Before filing a bug, verify that there isn't already a bug filed for this issue.
* If you suspect that the bug is a vulnerability, don't file a JBS issue. Instead send your bugreport to [vuln-dev@openjdk.java.net](mailto:vuln-dev@openjdk.java.net)
* Make a reasonable attempt to narrow down which build or release the failure first appeared in.
* Add relevant labels like `intermittent`, `regression`, `noreg-self` etc.
* Set affects version to the JDK version(s) where the failure was seen.
  * If the failure is found in an update train of the JDK (e.g. 11.0.x), please make an effort to see if the bug is also present in [mainline](https://hg.openjdk.java.net/jdk/jdk/).
* In the description, always include (if possible):
  * full name of the failing tests
  * error messages
  * assert messages
  * stack trace
  * command line information
  * relevant information from the logs
* If the failure is not reproducible with an existing OpenJDK test, attach a reproducer if possible.
* Only set CPU and/or OS fields if the bug **ONLY** happens on that particular platform.
* Always file separate bugs for different issues.
  * If two crashes looks related but not similar enough to for sure be the same, it is easier to close a bug as a duplicate than it is to extract the relevant info from a bug to create a new one later.

## Excluding a Test

Sometimes tests break. It could be e.g. due to bugs in the test itself, due to changed functionality in the code that the test is testing, or changes in the environment where the test is executed. While working on a fix, it can be useful to stop the test from being executed in everyone else's testing to reduce noise, especially if the test is expected to fail for more than a day. There are two ways to stop a test from being run in standard test runs: ProblemListing and using the `@ignore` keyword. Removing tests isn't the standard way to remove a failure. A failing test is often a regression and should ideally be handled with high urgency.

I'll say it right away so that it's not forgotten at the end: Remember to remove the JBS id from the ProblemList or the test when the bug is closed. This is especially easy to forget if a bug is closed as a duplicate or 'Will Not Fix'. jcheck will report an error and prohibit a push if a PR is created for an issue that is found in a ProblemList if the fix doesn't remove the bug ID from the ProblemList.

### ProblemListing jtreg tests

ProblemListing should be used for a short term exclusion while a test is being fixed, and for the exclusion of intermittently failing tests that cause too much noise, but can still be useful to run on an ad-hoc basis. ProblemListing is done in the file `ProblemList.txt`. There are actually several ProblemList files to choose from. Their location and name hint about what area or feature each file belongs to. Each file has sections for different components. All ProblemList files complement each other to build the total set of tests to exclude in JTReg runs.

~~~
test/hotspot/jtreg/ProblemList.txt
test/hotspot/jtreg/ProblemList-aot.txt
test/hotspot/jtreg/ProblemList-graal.txt
test/hotspot/jtreg/ProblemList-non-cds-mode.txt
test/hotspot/jtreg/ProblemList-Xcomp.txt
test/hotspot/jtreg/ProblemList-zgc.txt
test/jaxp/ProblemList.txt
test/jdk/ProblemList.txt
test/jdk/ProblemList-aot.txt
test/jdk/ProblemList-graal.txt
test/jdk/ProblemList-Xcomp.txt
test/langtools/ProblemList.txt
test/langtools/ProblemList-graal.txt
test/lib-test/ProblemList.txt
~~~

Use the suitable ProblemList and add a line like this in the proper section:

~~~
foo/bar/MyTest.java                        4711   windows-all
~~~

In this example, `MyTest.java` is ProblemListed on windows, tracked by bug `JDK-4711`.

Currently there's [no support for multiple lines for the same test](https://bugs.openjdk.java.net/browse/CODETOOLS-7902481). For this reason it's important to always make sure there's no existing entry for the test before adding a new one, as multiple entries might lead to unexpected results, e.g.

~~~
foo/bar/MyTest.java                        4710   generic-all
...
foo/bar/MyTest.java                        4711   windows-all
~~~

This would lead to `sun.tools.jcmd.MyTest.java` being ProblemListed only on `windows-all`. The proper way to write this is:

~~~
foo/bar/MyTest.java                        4710,4711   generic-all,windows-all
~~~

Although `windows-all` isn't strictly required in this example, it's preferable to specify platforms for each bugid (unless they are all `generic-all`), this makes it easier to remove one of the bugs from the list.

#### ProblemListing some, but not all, test cases in a file

Some tests contain several test cases and there may be a need to ProblemList only a few of them. To do this use the full test name, i.e. `<filename> + # + <test case id>`, where test case id can be specified in the test header. If no id is specified each test case can be referenced with `id` + ordinary number of the test case in the test file.

Let's assume we have four test cases in `foo/bar/MyTest.java`:

~~~
/* @test */
/* @test id=fancy_name */
/* @test */
/* @test */
~~~

A ProblemList entry that excludes the first, second, and third test case would look like this:

~~~
foo/bar/MyTest.java#id0          4720  generic-all
foo/bar/MyTest.java#fancy_name   4721  generic-all
foo/bar/MyTest.java#id2          4722  generic-all
~~~

Due to an issue described in [CODETOOLS-7902712](https://bugs.openjdk.java.net/browse/CODETOOLS-7902712) tests that contains more than one `@test` must actually use this way to specify all test cases if all of them should be ProblemListed. Specifying just the test name will not work.

#### Running ProblemListed tests

To run ad-hoc runs of ProblemListed tests use `RUN_PROBLEM_LISTS=true`.

~~~
make test TEST=... JTREG=RUN_PROBLEM_LISTS=true
~~~

### Exclude jtreg tests using `@ignore`

The `@ignore` keyword is used in the test source code. This is mainly used for tests that are so broken that they may be harmful or useless, and is less common than ProblemListing. Examples can be tests that don't compile because something changed in the platform; or a test which might remove your `/etc/shadow`. Use `@ignore` with a bug reference in the test case to prevent the test from being run.

~~~java
/**
 *  @test
 *  @ignore 4711
 *
~~~

In this example, `MyTest.java` is excluded, tracked by bug `JDK-4711`. `@ignore` should always be placed directly before the first `@run` line in the test.

### Dealing with JBS bugs for test exclusion

ProblemListing and `@ignore`-ing are done in the JDK source tree, that means a check-in into the repository is needed. This in turn means that a unique JBS issue and a code review are needed. This is a good thing since it makes test problems visible.

* **Code review**: This is considered a [trivial](#trivial) change.
* **JBS issue**: A JBS issue is obviously created for the bug that caused the failure, we call this the _main issue_. To exclude the test, create a subtask to the main issue. Also add the label `problemlist` to the main issue.

The fix for the main issue should remove the test from the ProblemList or remove the `@ignore` keyword from the test.

#### Triage excluded issues

After a failure is handled by excluding a test, the main JBS issue should be re-triaged and possibly given a new priority. This should be handled by the standard triage process. A test exclusion results in an outage in our testing. This outage should be taken into consideration when triaging, in addition to the impact of the bug itself.

## Backing Out a Change

If a change causes a regression that can't be fixed within reasonable time the best way to handle the regression can be to back out the change. Backing out means that the inverse (anti-delta) of the change is pushed to effectively undo the change in the repository. There are two parts to this task, how to do the bookkeeping in JBS, and how to do the actual backout in mercurial.

The backout is a regular change and will have to go through the standard code review process, but is considered a [trivial](#trivial) change. The rationale is that a backout is usually urgent in nature and the change itself is automatically created by hg, and reviewed by the person who is performing the backout, so even though only one additional Reviewer is required the change will in practice get two reviews.

### How to work with JBS when a change is backed out

#. Close the original JBS issue **(O)**.
   * "Verify" the issue and choose "Fix Failed".
#. If the intention is to fix the change and submit it again, create a redo-issue **(R)** to track that the work still needs to be done.
   * Clone **(O)** and add the prefix `[REDO]` on the summary - the clone becomes the redo-issue **(R)**.
   * Make sure relevant information is brought to **(R)**.
   * Remember that comments are not brought over when cloning.
#. Create a backout-issue **(B)**:
   * Alternative 1 - the regression is identified directly.
     * Create a sub-task to **(R)** with the same summary, prefix with `[BACKOUT]`.
   * Alternative 2 - an investigation issue is created **(I)**, and during the investigation backing out the change is identified as the best solution.
     * Use the investigation issue **(I)** for the backout.
     * Change summary of **(I)** to the same as **(O)** and prefix with `[BACKOUT]`.
     * Move and change type of **(I)** to become a sub-task of **(R)**.
   * Alternative 3 - no redo issue was created.
     * Create a backout-issue **(B)** with the same summary as **(O)**, prefix with `[BACKOUT]`.
     * Link **(B)** and **(O)**.

ProblemList entries and `@ignore` keywords will continue to point to the original bug (unless updated at back out). This is accepted since there is a clone link to follow.

### How to work with mercurial when a change is backed out

In order to backout a change, the `hg backout` command is recommended, which essentially applies the anti delta of the change. Make sure you perform the backout in the most upstream repostiory the change has escaped to.

~~~
hg backout [OPTION]... [-r] REV

reverse effect of earlier changeset

    Prepare a new changeset with the effect of REV undone in the current
    working directory.

    If REV is the parent of the working directory, then this new changeset is
    committed automatically. Otherwise, hg needs to merge the changes and the
    merged result is left uncommitted.
~~~

# About this Guide

This guide is being maintained through the [OpenJDK Developers' Guide Project](https://openjdk.java.net/census#guide). The [source repository](https://github.com/openjdk/guide) is available at GitHub. The revision hash at the bottom of each page refers to the latest change that modified that particular page.

Comments and questions may be sent to [guide-dev (at) openjdk.java.net](mailto:guide-dev-at-openjdk.java.net). Please let us know if there's anything in the guide that isn't clear.

# Glossary

[**accepted**]{#accepted} (by the CCC)
:   The stage of the CCC process after "DRAFT", and "PROPOSED". At this stage the primary goals are to ensure that the proposed changes are suitable for the release in a general sense and that the requisite JCK and SQE resources will be available.

[**approved**]{#approved} (by the CCC)
:   The stage of the CCC process after "FINAL". The CCC has approved the final version of the request which permits push into the project forest.

[**changeset**]{#changeset}
:   A collection of changes with respect to the current clone of a repository.

[**development freeze**]{#developmentfreeze}
:   The date by which all planned work should be complete for a particular line of development. After a line's development freeze, only exit-criteria bugs may be fixed in that line.

[**forest**]{#forest}
:   A collection of Mercurial repositories which can be managed as a set of nested repositories. The name "Forest" originally came from the Mercurial "Forest Extension" which can be used with some versions of Mercurial, but in general is no longer recommended. The script `common/bin/hgforest.sh` can be used to apply a Mercurial `hg` command to all the repositories in a forest.

[**Group**]{#group}
:   A collection of [Participants](/bylaws#participant) who engage in open conversation about a common interest. That interest may be in the creation, enhancement, or maintenance of a specific body of code or it may lie in other areas, e.g., quality, documentation, or evangelism. See the [Group overview](../groups/).

[**Mercurial**]{#mercurial}
:   A free, cross-platform, distributed source management tool. Source bundles and binary packages for Mercurial are available at [https://www.selenic.com/mercurial/wiki/index.cgi](https://www.selenic.com/mercurial/wiki/index.cgi/Mercurial). The main Mercurial documentation is available at [http://hgbook.red-bean.com](https://hgbook.red-bean.com/).

[**Project**]{#project}
:   A collaborative effort to produce a specific artifact, which may be a body of code, or documentation, or some other material. See the [Project overview](../projects/).

[**repository**]{#repository}
:   A directory tree in the filesystem that Mercurial treats specially. This tree contains the source files and their revision history.

[**trivial**]{#trivial}
:   A change that is small, well contained, and makes no semantic changes. Typically fixing obvious typos or renaming some local identifier. Trivial changes can also be pushing an already reviewed change that was missed in an earlier push (e.g. forgot to add a file) or generated changes like an [`hg backout`](#backing-out-a-change). It's up to the author of a change to claim that the change is trivial in the RFR, and it's up to the Reviewer whether to approve such a claim. A change is only trivial if the Reviewer says so. Trivial changes does not have to wait 24 hours before being pushed, and only needs one Reviewer, even in areas where stricter rules for pushing normally applies.

[**webrev**]{#webrev}
:   A tool and its output. In JDK release forests, the script, [`webrev.ksh`](https://hg.openjdk.java.net/code-tools/webrev/raw-file/tip/webrev.ksh), examines a forest or repository to generate a set of web-based views of differences.

# Code Owners

This list is intended to make it easier to identify which email list to include in code reviews when making changes in different areas. The list may also help when assigning bugs based on which code they are found in. Please note that some directories may have been created or removed between releases. The intention here is to include directories that exists in mainline, LTS releases and other releases (post JDK 9) commonly being updated.

## Area mailing lists

* Build: [`build-dev@openjdk.java.net`](mailto:build-dev@openjdk.java.net)
* Client
  * AWT: [`awt-dev@openjdk.java.net`](mailto:awt-dev@openjdk.java.net)
  * Beans: [`beans-dev@openjdk.java.net`](mailto:beans-dev@openjdk.java.net)
  * Java 2D: [`2d-dev@openjdk.java.net`](mailto:2d-dev@openjdk.java.net)
  * Java FX: [`openjfx-dev@openjdk.java.net`](mailto:openjfx-dev@openjdk.java.net)
  * jpackage: [`core-libs-dev@openjdk.java.net`](mailto:core-libs-dev@openjdk.java.net)
  * Sound: [`sound-dev@openjdk.java.net`](mailto:sound-dev@openjdk.java.net)
  * Swing: [`swing-dev@openjdk.java.net`](mailto:swing-dev@openjdk.java.net)
* Core Libs: [`core-libs-dev@openjdk.java.net`](mailto:core-libs-dev@openjdk.java.net)
  * Net: [`net-dev@openjdk.java.net`](mailto:net-dev@openjdk.java.net)
  * NIO: [`nio-dev@openjdk.java.net`](mailto:nio-dev@openjdk.java.net)
* HotSpot: [`hotspot-dev@openjdk.java.net`](mailto:hotspot-dev@openjdk.java.net)
  * Compiler: [`hotspot-compiler-dev@openjdk.java.net`](mailto:hotspot-compiler-dev@openjdk.java.net)
  * GC: [`hotspot-gc-dev@openjdk.java.net`](mailto:hotspot-gc-dev@openjdk.java.net)
  * Runtime: [`hotspot-runtime-dev@openjdk.java.net`](mailto:hotspot-runtime-dev@openjdk.java.net)
  * Serviceability: [`serviceability-dev@openjdk.java.net`](mailto:serviceability-dev@openjdk.java.net)
* I18n: [`i18n-dev@openjdk.java.net`](mailto:i18n-dev@openjdk.java.net)
* LangTools
  * Amber Project: [`amber-dev@openjdk.java.net`](mailto:amber-dev@openjdk.java.net)
  * Javac: [`compiler-dev@openjdk.java.net`](mailto:compiler-dev@openjdk.java.net)
  * Javadoc: [`javadoc-dev@openjdk.java.net`](mailto:javadoc-dev@openjdk.java.net)
  * JShell: [`kulla-dev@openjdk.java.net`](mailto:kulla-dev@openjdk.java.net)
  * Panama Project: [`panama-dev@openjdk.java.net`](mailto:panama-dev@openjdk.java.net)
  * Valhalla Project: [`valhalla-dev@openjdk.java.net`](mailto:valhalla-dev@openjdk.java.net)
* Security: [`security-dev@openjdk.java.net`](mailto:security-dev@openjdk.java.net)

## Directory to area mapping

* `make` – Build team
* `hotspot` – HotSpot
  * `cpu` – Compiler, Runtime
  * `jdk.*` – Compiler
  * `os` – Runtime
  * `os_cpu` – Compiler
  * `share`
    * `adlc` – Compiler
    * `aot` – Compiler
    * `asm` – Runtime
    * `c1` – Compiler
    * `ci` – Compiler
    * `classfile` – Runtime
    * `code` – Compiler
    * `compiler` – Compiler
    * `gc` – GC
    * `include` – HotSpot
    * `interpreter` – Runtime
    * `jfr` – Runtime
    * `jvmci` – Compiler
    * `libadt` – Compiler
    * `logging` – Runtime
    * `memory` – Runtime, GC
    * `metaprogramming` – Runtime
    * `oops` – Runtime
    * `opto` – Compiler
    * `precompiled` – Runtime
    * `prims` – Runtime, Serviceability
    * `runtime` – Runtime
    * `services` – Runtime
    * `shark` – Compiler
    * `trace` – Runtime
    * `utilities` – Runtime
* `java.base`
  * Core Libs should almost always be included but LangTools, HotSpot, Security and/or I18n may also be involved.
  * `classes`
    * `crypto` – Security
    * `internal` – HotSpot, Core Libs
    * `invoke` – Core Libs
    * `io` – NIO
    * `lang` – Core Libs
    * `launcher` – LangTools
    * `math` – Core Libs
    * `net` – Net
    * `nio` – NIO
    * `reflect` – Core Libs
    * `security` – Security
    * `text` – I18n
    * `time` – Core Libs
    * `util` – I18n, Core Libs
  * `conf`
    * `sdp` – Net
    * `security` – Security
  * `legal` –
  * `lib/security` – Security
  * `man`
    * `java.1` - LangTools
    * `jfr.1` - Runtime
    * `keytool.1` - Security
  * `native`
    * `common` –
    * `include` – Runtime, Core Libs
    * `jspawnhelper` – LangTools
    * `launcher` – LangTools
    * `libfdlibm` – Core Libs
    * `libjava` – Core Libs
    * `libjimage` – LangTools, Core Libs
    * `libjli` – LangTools
    * `libjsig` – HotSpot
    * `libnet` – Net
    * `libnio` – NIO
    * `libosxsecurity` – Security
    * `libverify` – LangTools
    * `libzip` – Core Libs
* `java.compiler` – LangTools
* `java.datatransfer` – AWT
* `java.desktop` – Client
  * Many files in the awt directories are shared between 2D and AWT
    * See [https://openjdk.java.net/groups/2d/2dawtfiles.html](https://openjdk.java.net/groups/2d/2dawtfiles.html)
    * And see [https://openjdk.java.net/groups/2d](https://openjdk.java.net/groups/2d)
  * `color`, `font`, `freetype`, `geom`, `imageio`, `java2d`, `jpeg`, `lcms`, `mlib`, `print`, Graphics primitives – 2D
  * `splashscreen`, `dnd`, `eawt`, `lwawt` – AWT
  * `im`, InputMethods – I18n, AWT
  * `libjsound`, `sound` – Sound
  * `accessibility`, `laf` – Swing
* `java.instrument` – Serviceability
* `java.logging` – Core Libs
* `java.management` – Serviceability
* `java.management.rmi` – Serviceability
* `java.naming` – Core Libs
* `java.net.http` – Net
* `java.prefs` – Core Libs
* `java.rmi` – Core Libs
* `java.scripting` – LangTools
* `java.se` – Core Libs
* `java.security.jgss` – Security
* `java.security.sasl` – Security
* `java.smartcardio` – Security
* `java.sql` – Core Libs
* `java.sql.rowset` – Core Libs
* `java.transaction.xa` – Core Libs
* `java.xml` – Core Libs
* `java.xml.crypto` – Security
* `jdk.accessibility` – Swing
* `jdk.aot` – HotSpot Compiler
* `jdk.attach` – Serviceability
* `jdk.charsets` – I18n, Core Libs
* `jdk.compiler` – LangTools
* `jdk.crypto.cryptoki` – Security
* `jdk.crypto.ec` – Security
* `jdk.crypto.mscapi` – Security
* `jdk.crypto.ucrypto` – Security
* `jdk.dynalink` – LangTools
* `jdk.editpad` – LangTools
* `jdk.hotspot.agent` – Serviceability
* `jdk.httpserver` – Net
* `jdk.incubator.foreign` – LangTools
* `jdk.incubator.httpclient` – Net
* `jdk.incubator.jpackage` –
* `jdk.internal.ed` – LangTools
* `jdk.internal.jvmstat` – Serviceability
* `jdk.internal.le` – LangTools
* `jdk.internal.opt` – LangTools
* `jdk.internal.vm.ci` – HotSpot Compiler
* `jdk.internal.vm.compiler` – HotSpot Compiler
* `jdk.internal.vm.compiler.management` – HotSpot Compiler
* `jdk.jartool` – LangTools
* `jdk.javadoc` – LangTools
* `jdk.jcmd` – Serviceability
* `jdk.jconsole` – Serviceability
* `jdk.jdeps` – Core Libs
* `jdk.jdi` – Serviceability
* `jdk.jdwp.agent` – Serviceability
* `jdk.jfr` – Runtime
* `jdk.jlink` – LangTools
* `jdk.jshell` – LangTools
* `jdk.jsobject` – LangTools
* `jdk.jstatd` – Serviceability
* `jdk.localedata` – I18n
* `jdk.management` – Serviceability
* `jdk.management.agent` – Serviceability
* `jdk.management.jfr` – Runtime
* `jdk.naming.dns` – Core Libs
* `jdk.naming.rmi` – Core Libs
* `jdk.net` – Net
* `jdk.nio.mapmode` – NIO
* `jdk.pack` – LangTools
* `jdk.rmic` – Core Libs
* `jdk.scripting.nashorn` – LangTools
* `jdk.scripting.nashorn.shell` – LangTools
* `jdk.sctp` – Net
* `jdk.security.auth` – Security
* `jdk.security.jgss` – Security
* `jdk.unsupported` – Core Libs
* `jdk.unsupported.desktop` – Swing
* `jdk.xml.dom` – Core Libs
* `jdk.zipfs` – Core Libs
* `sample` –
* `utils` –

# JBS Label Dictionary

This table contains some frequently used JBS labels and their meaning. Please help keeping this dictionary up to date by adding your favorite labels. This table doesn’t dictate how to use labels, but rather document how they are used. That said, obviously it will help everyone if we try to follow a common standard and use similar labels in the same way across all entities that use JBS.

> ---
> ### Labels are case sensitive
> When using labels in Jira gadgets (like pie charts, heat maps, and statistics tables) Jira will be case sensitive and treat e.g. OpenJDK and openjdk as two different labels. Searching however is case insensitive. This means that if you group a set of issues in a gadget based on a label, and then click one of the groups to see the list of issues, that list will contain more results than the gadget if there are usages of the label with different casing. This can be very confusing and for this reason the recommendation is to stick with the commonly used case for all labels, regardless of your personal taste for upper or lower case letters. Most labels are lower case only, but there are examples where upper case letters are used in the most common version of a label.

> ---

<table class="dictionary">
  <tr style="text-align:left;"><th>Label</th><th>Description</th></tr>
  <tr>
    <td class="dictionary">
      [*(Area)***`-interest`**]{#area-interest}
    </td>
    <td class="dictionary">
      Used to indicate that an area (usually a team or project) is interested in the issue. This label doesn't indicate ownership of the issue. E.g., **`redhat-interest`**, **`azul-interest`**, **`coin-interest`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Area)***`-related`**]{#area-related}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to a specific area (usually a feature or project). This label doesn't indicate ownership of the issue. E.g., **`graal-related`**, **`testcolo-related`**, **`doc-related`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-bp`**]{#rel-bp}
    </td>
    <td class="dictionary">
      Used to indicate that a bug would be suitable for backport to a release *(Rel)*. This is not a decision to backport, just a suggestion / recommendation. E.g., **`11-bp`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-critical-request`**]{#rel-critical-request}<br>
      [*(Rel)***`-critical-approved`**]{#rel-critical-approved}<br>
      [*(Rel)***`-critical-watch`**]{#rel-critical-watch}
    </td>
    <td class="dictionary">
      Used in the rampdown phases of specific releases to request approval of changes that requires project lead approval (or similar) to be included. *(Rel)* is the release in question. E.g., **`jdk11-critical-request`**

      *(Rel)***`-critical-approved`** is used to signal that the change has been approved for inclusion. E.g., **`jdk11-critical-approved`**<br>
      *(Rel)***`-critical-watch`** is used for issues that must get into a specific release but risk running late. The label is used while the issue is still in progress and is replaced with *(Rel)***`-critical-request`** once the issue is resolved. E.g., **`jdk11-critical-watch`**

      These labels are always placed on the main JBS issue (the bug), never on backports or subtasks.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-defer-request`**]{#rel-defer-request}<br>
      [*(Rel)***`-defer-yes`**]{#rel-defer-yes}<br>
      [*(Rel)***`-defer-no`**]{#rel-defer-no}
    </td>
    <td class="dictionary">
      Used to request deferral of changes that requires project lead approval (or similar) to defer. *(Rel)* is the release in question. E.g., **`jdk12-defer-request`**

      *(Rel)***`-defer-yes`** and *(Rel)***`-defer-no`** are used to indicate wether the deferral has been approved or not. E.g., **`jdk12-defer-yes`**

      These labels are always placed on the main JBS issue (the bug), never on backports or subtasks.
      Further details are found in the [JDK Release Process](https://openjdk.java.net/jeps/3#Bug-Deferral-Process).
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-enhancement-request`**]{#rel-enhancement-request}<br>
      [*(Rel)***`-enhancement-yes`**]{#rel-enhancement-yes}<br>
      [*(Rel)***`-enhancement-no`**]{#rel-enhancement-no}
    </td>
    <td class="dictionary">
      Used in the rampdown phases to request the late inclusion of an enhancement. *(Rel)* is the release in question. E.g., **`jdk10-enhancement-request`**

      *(Rel)***`-enhancement-yes`** and *(Rel)***`-enhancement-no`** are used to indicate the response on the request. E.g., **`jdk10-enhancement-yes`**, **`jdk10-enhancement-no`**

      These labels are always placed on the main JBS issue (the bug), never on backports or subtasks.
      Further details are found in the [JDK Release Process](http://openjdk.java.net/jeps/3#Late-Enhancement-Request-Process).
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-fix-request`**]{#rel-fix-request}<br>
      [*(Rel)***`-fix-SQE-ok`**]{#rel-fix-SQE-ok}<br>
      [*(Rel)***`-fix-yes`**]{#rel-fix-yes}<br>
      [*(Rel)***`-fix-no`**]{#rel-fix-no}
    </td>
    <td class="dictionary">
      Used in rampdown phase 2 to indicate that an issue would be of interest to get integrated into release *(Rel)*. E.g., **`jdk12u-fix-request`**

      *(Rel)***`-fix-SQE-ok`** is used to indicate that the issue will be covered by the test plan for *(Rel)*. E.g., **`jdk12u-fix-SQE-ok`**<br>
      *(Rel)***`-fix-yes`** and *(Rel)***`-fix-no`** are used to indicate wether an issue has been approved for backport to *(Rel)*. E.g., **`jdk12u-fix-yes`**

      These labels are always placed on the main JBS issue (the bug), never on backports or subtasks.
      Further details are found in the [JDK Release Process](http://openjdk.java.net/jeps/3#Fix-Request-Process).
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Rel)***`-na`**]{#rel-na}
    </td>
    <td class="dictionary">
      Used to indicate that the issue does not affect release *(Rel)* or later. Could for instance be a bug in code that was removed in *(Rel)*.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [*(Team)***`-triage-`***(Rel)*]{#team-triage-rel}
    </td>
    <td class="dictionary">
      Used to indicate that *(Team)* has triaged this issue for release *(Rel)*. It's encouraged that all open bugs are triaged on a regular basis so that old bugs aren't forgotten. It's therefore common to see several triage labels on the same issue which helps keeping track of which bugs has been triaged for each release. E.g., **`oracle-triage-13`**

      There are many label variants that include the word triage in some form. The form described above is the only one recommended. Please refrain from using other forms.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`aot`**]{#aot}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to Ahead of Time Compilation.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
     [~~**`appcds`**~~]{#appcds}
    </td>
    <td class="dictionary">
      **Deprecated.** Was used to indicate that an issue was related to Application Class-Data Sharing. The **`cds`** label is now used instead.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`c1`**]{#c1}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to the C1 JIT compiler.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`c2`**]{#c2}<br>
      **`c2-`**`.*`
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to the C2 JIT compiler.

      **`c2-`**`.*` labels are used to identify different c2 features. E.g., **`c2-intrinsic`**, **`c2-loopopts`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`cds`**]{#cds}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to Class Data Sharing.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`cleanup`**]{#cleanup}
    </td>
    <td class="dictionary">
      The **`cleanup`** label is used to indicate enhancements which has no semantic changes, whose only purpose is to make the code more maintainable or better looking.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`docker`**]{#docker}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to docker support.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`gc-`**`.*`]{#gc}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to a specific garbage collector in the JVM. E.g., **`gc-g1`**, **`gc-shenandoah`**, **`gc-serial`**, **`gc-epsilon`**

      There are also labels in use to identify different GC features or areas rather than GC algorithms. E.g., **`gc-g1-fullgc`**, **`gc-largeheap`**, **`gc-performance`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`graal`**]{#graal}
    </td>
    <td class="dictionary">
      Used to indicate that this is a Graal issue. (Something that needs to be fixed in Graal rather than in OpenJDK.)
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`graal-integration`**]{#graal-integration}
    </td>
    <td class="dictionary">
      Reserved for Graal integration umbrella bugs. The automated integration script will break if this label is used for other bugs.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`hgupdate-sync`**]{#hgupdate-sync}
    </td>
    <td class="dictionary">
      Used to identify backport issues automatically created by HG Updater (a script that monitors the hg repositories for changes).
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [~~**`hs-nightly`**~~]{#hs-nightly}
    </td>
    <td class="dictionary">
      **Deprecated.** Was used to tag bugs found in the HotSpot nightly testing. Since we are now running tiered testing there is no more nightly HotSpot testing. See **`tier`**`[1-8]`.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`hs-sbr`**]{#hs-sbr}
    </td>
    <td class="dictionary">
      Used to tag bugs that are found in the "same binary runs", a stress testing method used to find intermittent failures.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [~~**`hs-tier`**~~~~`[1-8]`~~]{#hs-tier}
    </td>
    <td class="dictionary">
      **Deprecated.** Was used to identify which HotSpot tier a test failure was seen in. We don't separate HotSpot tiers from the JDK tiers anymore. See **`tier`**`[1-8]`.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`i18n`**]{#i18n}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to internationalization.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`integration-blocker`**]{#integration-blocker}
    </td>
    <td class="dictionary">
      Used to indicate that a bug is present in a downstream repository but not present in the upstream repository and is therefore blocking integration of downstream changes into upstream.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`intermittent`**]{#intermittent}<br>
      [**`intermittent-environment`**]{#intermittent-environment}<br>
      [**`intermittent-hardware`**]{#intermittent-hardware}
    </td>
    <td class="dictionary">
      An intermittent issue is one that fails sometimes but not always. The exact reason for the intermittent failure is per definition unknown. Once the reason has been identified the issue is no more considered intermittent. An issue isn't intermittent if some characteristics has been found that triggers the failure consistently, even if the actual cause for the failure has not been found. For instance if a test fails every time it is executed on a specific host but not on other hosts it wouldn't be considered intermittent as it fails consistently on that specific host. In other cases it may be that we know that a test sometimes is unlucky in some respect and fails due to this. This test could still be considered intermittent even though we know what the reason is if the reason itself appears intermittently.

      Some issues may seem intermittent when looking at test results, even though the reason for failing is actually known. One example is where a test fails consistently on a specific host, or due to specific conditions in the environment. These failures should not be considered intermittent but it may still be valuable to tag these in JBS with one of the labels **`intermittent-hardware`** or **`intermittent-environment`**. This will help to faster identify that the cause of the failure is known without having to read through the entire bug.

      A test that should be platform agnostic but is consistently failing on a specific OS would for instance be labeled with **`intermittent-environment`**, while a test that fails every time it is run on some specific hardware would be labeled with **`intermittent-hardware`**.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`maintainer-pain`**]{#}
    </td>
    <td class="dictionary">
      Bugs that for some reason is wasting engineering time just by existing, or in other ways are causing pain for the maintainers of the JDK. Examples are bugs that occur frequently in testing or test failures that are time consuming to investigate before determining that it is a pre-existing bug.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`noreg-`**`.*`]{#noreg}<br>
      [**`nounit-`**`.*`]{#nounit}
    </td>
    <td class="dictionary">
      The **`noreg-`**`.*` and **`nounit-`**`.*` labels are used to explain why a bugfix doesn't need/have a regression test or a unit test. The suffix of the label is described below.

[**`-sqe`**]{#noreg-sqe}
:    Change can be verified by running an existing SQE test suite; the bug should identify the suite and the specific test case(s).

[**`-jck`**]{#noreg-jck}
:    Change can be verified by running the JCK; the bug should identify the specific test case(s).

[**`-external`**]{#noreg-external}
:    Change can be verified by running an existing external test suite; the bug should identify the suite and the specific test case(s).

[**`-doc`**]{#noreg-doc}
:    Change only affects documentation.

[**`-demo`**]{#noreg-demo}
:    Change only affects demo code.

[**`-build`**]{#noreg-build}
:    Change only affects build infrastructure (makefiles, copyrights, scripts, etc.).

[**`-self`**]{#noreg-self}
:    Change is a fix to a regression or unit test itself.

[**`-perf`**]{#noreg-perf}
:    Change is for a performance bug for which writing a regression test is infeasible; the bug should describe how to verify the fix.

[**`-hard`**]{#noreg-hard}
:    It is too hard to write a regression or unit test for this bug (e.g., theoretical race condition, complex setup, reboot required, editing of installed files required, specific graphics card required); the bug should explain why.

[**`-long`**]{#noreg-long}
:    Testing requires a very long running time (e.g., more than a few minutes).

[**`-big`**]{#noreg-big}
:    Testing requires an unreasonable quantity of resources (e.g., tens of gigabytes of filesystem space).

[**`-trivial`**]{#noreg-trivial}
:    Change is so trivial that nothing could possibly go wrong with it.

[**`-cleanup`**]{#noreg-cleanup}
:    Change is a cleanup or refactoring of existing code that is covered by existing tests.

[**`-l10n`**]{#noreg-l10n}
:    Change only affects localized text.

[**`-undo`**]{#noreg-undo}
:    Change is a reversion of a previous faulty change.

[**`-other`**]{#noreg-other}
:    Regression or unit test is unnecessary or infeasible for some other reason; the bug report should explain why.

Examples:  If a bug fix only corrects a change in the build system, then add the **`noreg-build`** label to the corresponding bug. If the change improves loop optimizations in HotSpot, then add **`nounit-perf`** to the corresponding bug.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`performance`**]{#performance}
    </td>
    <td class="dictionary">
      Used to identify a bug with noticeable performance impact. Either positive or negative.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [~~**`pit`**~~]{#pit}
    </td>
    <td class="dictionary">
      **Deprecated.** Was used to indicate that a failure happened in product integration testing (PIT). Since we are now running tiered testing there is no more PIT. See **`tier`**`[1-8]`.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`problemlist`**]{#problemlist}
    </td>
    <td class="dictionary">
      One or more tests has been problemlisted due to this bug.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`regression`**]{#regression}
    </td>
    <td class="dictionary">
      A regression is a bug that did not exist in the previous release. Ideally all regressions must be fixed in order to release the next major version.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`release-note`**]{#release-note}
    </td>
    <td class="dictionary">
      Used to indicate that the issue is a release note. The release-note issue is a sub-task to the main JBS issue containing the text to be used in the release note. The release note must also have one of the following labels:

[**`RN-NewFeature`**]{#RN-NewFeature}
:   New Feature or enhancement.

[**`RN-IssueFixed`**]{#RN-IssueFixed}
:   A significant issue which has been fixed, would normally be a regression or an issue which unknowingly released in a new feature.

[**`RN-KnownIssue`**]{#RN-KnownIssue}
:   An issue that was not possible to fix by the time the release was GA'd.

[**`RN-Removed`**]{#RN-Removed}
:   Covers an API, feature, tool etc. which has been removed from the JDK.

[**`RN-Deprecated`**]{#RN-Deprecated}
:   Covers an API that has been marked as deprecated in the release.

[**`RN-Important`**]{#RN-Important}
:   Used to indicate that the release note should be highlighted in some fashion, such as listing it at the beginning of the release notes.

[**`RN-`(distro)**]{#RN-distro}
:   Used to indicate that the release note is only relevant for a specific JDK distribution. E.g. RN-Oracle

[~~**`RN-Change`**~~]{#RN-Change}
:   Deprecated.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`release-note=yes`**]{#release-note-yes}<br>
      [**`release-note=no`**]{#release-note-no}<br>
      [~~**`release-note=done`**~~]{#release-note-done}
    </td>
    <td class="dictionary">
      Used to indicate wether a change requires a release note or not. The labels are always placed on the main JBS issue, never on the actual release note issue.

      **`release-note=done`** is deprecated and should no longer be used.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`starter`**]{#starter}
    </td>
    <td class="dictionary">
      A starter bug is a well contained, small issue that is suitable for someone new to the codebase.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`startup`**]{#startup}
    </td>
    <td class="dictionary">
      Used to identify a bug as affecting Java SE startup performance.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`tck-red-`***(Rel)*]{#tck-red-rel}<br>
      [~~**`tck-red`**~~]{#tck-red}
    </td>
    <td class="dictionary">
      Used to identify TCK conformance stoppers (e.g. failure of a valid TCK test that exists in a shipped TCK). The release number indicates which release of the TCK that failed. E.g., **`tck-red-11`**

      There are **`tck-red`** labels without the release number out there as well. This usage is deprecated.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [~~**`test`**~~]{#test}<br>
      [~~**`test-only`**~~]{#test-only}<br>
      [~~**`testbug`**~~]{#testbug}
    </td>
    <td class="dictionary">
      The labels **`test`**, **`test-only`**, and **`testbug`** are deprecated and should no longer be used. Use [**`noreg-self`**](#noreg) to indicate that an issue is a bug in test code.
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`tier`**`[1-8]`]{#tier}
    </td>
    <td class="dictionary">
      Used to indicate which tier in the jdk/jdk CI pipeline a test failure has been seen in. Lower tiers would in general mean higher urgency to fix the issue. E.g., **`tier1`**, **`tier2`**
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`webbug`**]{#webbug}
    </td>
    <td class="dictionary">
      Used to identify a bug as submitted on [bugs.java.com](https://bugs.java.com/bugdatabase/).
    </td>
  </tr>
  <tr>
    <td class="dictionary">
      [**`zgc`**]{#zgc}
    </td>
    <td class="dictionary">
      Used to indicate that an issue is related to ZGC.
    </td>
  </tr>
</table>
