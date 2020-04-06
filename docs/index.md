# Software Engineering for Experimental Economics

These are working notes for learning and applying software engineering
practices for the efficient development of software to do
economic decision-making experiments.


## Version control

An essential prerequisite is learning the basics of a modern
distributed version control system.  We highly recommend
[`git`](https://git-scm.com).

A good introduction to distribution version control, and
`git` in particular, targeted at scientific computing users is
the one offered by
[Software Carpentry](http://swcarpentry.github.io/git-novice/).
You should work through their exercises before starting to
program, to become familiar with how `git` works.


## Setting up a Python development environment

(At present this assumes a Mac.  Versions for Windows and Linux
to be written!)

Python comes pre-installed on a Mac, or you can download it from
the official Python website.  However, we *strongly recommend*
that you don't develop your software using the system Python
or one you download and install directly.  The reason is that a
Python installation consists of both the Python interpreter, as well
as a large number of supporting libraries and files.  A benefit
of using Python to develop your software is this enormous ecosystem
of libraries.

However, installing libraries directly into a Python installation
that's shared across the system has two important drawbacks:

1.  Any software that relies on that Python installation also relies
on certain additional packages being installed, and those packages
being of specific versions.  If you upgrade or change the packages
that are installed in a shared Python, you might unintentionally
break other programs.  Especially on Mac and Linux, there are many
programs and utilities that rely on the system installation of Python,
so you might unwittingly destablise your whole system.
2.  When doing an experiment, you want complete control of the
environment your experiment runs in.  When you move from testing
on your own computer to deploying your experiment in your lab or
on a server, you want to know you are running your program in
the same version of Python, with the same versions of any libraries
installed.

The steps we will outline here will keep you from accidentally
messing up a Python installation relied on by other programs on your
computer, and make it easy for you (and others) to develop and use
your software on other computers by tracking the versions of Python
and other libraries you rely on.

Some of these steps you only need to do once on a given computer.
Some you should do for each project you are working on.  They require
a small amount of extra work up front.  However, they are fairly
routine and take just a few minutes; just follow the cookbook here.
Please trust us when we tell you this investment in good "hygiene"
will save you untold hours of confusion and frustration down
the line.


### Steps to take once on a computer

These are steps you will need to do only when setting up your computer
to develop or deploy your software.

#### Install homebrew

`Homebrew` is a package manager for MacOS.  It is a convenient
way to manage the installation and maintenance of many open-source
programs and libraries.

Go to the homebrew website [https://brew.sh](https://brew.sh)
and follow the instructions
there for installation - it involves simply copying and pasting a
single line into the Terminal.

#### Installing Python via pyenv

Rather than installing Python using the installers provided on the
official site, we will use [`pyenv`](https://github.com/pyenv/pyenv)
to download and install Python.  `pyenv` will allow us to install
as many different versions of Python as we like, without interfering
with each other (or the system installation of Python).[^1]

[^1]: This section benefitted greatly from the advice given at
[https://realpython.com/intro-to-pyenv/](https://realpython.com/intro-to-pyenv/).

In the Terminal, run

	$ brew install pyenv

To see the list of Python versions you have installed:

	$ pyenv versions
	* system (set by /Users/YOURNAME/.pyenv/version)

You can get a list of all the Python versions that `pyenv` can install

	$ pyenv install --list

You'll get a long list of options (which we don't include here).
As of the writing of this section, 3.7.7 is the most recent
version in the 3.7 series of Python.  We'll install that:

	$ pyenv install 3.7.7

It will take a few minutes for the build to complete.  If you are
uncomfortable with the terminal sitting there with no output
for a while, and want to watch the sausage being made, you can turn
on verbose mode:

	$ pyenv install -v 3.7.7

After the build completes, you'll see 3.7.7 listed in the list
of available versions:

	$ pyenv versions
	* system (set by /Users/YOURNAME/.pyenv/version)
	  3.7.7

At the moment, if you run `python` at the prompt, you would be using
the system python (that's what the `*` means in the output).  You
can change this by using `pyenv shell`:

	$ pyenv which python
	/usr/local/bin/python
	$ pyenv shell 3.7.7
	$ python -V
	Python 3.7.7
	$ pyenv which python
	/Users/YOURNAME/.pyenv/versions/3.7.7/bin/python

This changes the version of Python being used for the current shell
you're working in only.  If you open a new shell, it will still
default to the system Python.

This gets us part of the way there; we can install whichever version
of Python we'd like to use, and we can keep it separate from the
Python the system uses (and other users use, as well).  But if we
start installing packages in the 3.7.7 installation, we still
have the problem of keeping track of which packages we need for
each program we are writing.  We'll turn to solving that next.


### Virtual environments

A Python _virtual environment_ allows us to isolate further the
set of packages and configuration of Python, so we can control
the environment our program runs in and keep it separate from
the environment used by other programs we might having installed
or be developing.  They are similar in spirit to what we have
done above, with installing different versions of Python via
`pyenv` - each of those versions has its own environment with its
own set of installed package.  But, for example, what if we wanted
to develop two different programs using Python 3.7.7, but wanted
to manage the packages the two programs depended on separately?
This is what a virtual environment allows us to do.[^2]

[^2]: For a more general introduction to Python virtual environments,
see
[https://realpython.com/python-virtual-environments-a-primer/](https://realpython.com/python-virtual-environments-a-primer/)

There is a plugin for `pyenv` which makes creation and management
of virtual environments easy.  We'll install it via Homebrew:

	$ brew install pyenv-virtualenv

You'll get a message like this:

	==> Caveats
	To enable auto-activation add to your profile:
	  if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi

We won't use auto-activation in this document; we'll prefer to
activate virtual environments manually to encourage awareness of
when we are or aren't running in a virtual environment.

The syntax for creating a virtual environment is:

	$ pyenv virtualenv <python_version> <environment_name>

In our example, if we want to create a virtual environment for our
first project, we might do

	$ pyenv virtualenv 3.7.7 myproject

If you then look at the versions installed, you would see

	$ pyenv versions
      system
    * 3.7.7 (set by PYENV_VERSION environment variable)
      3.7.7/envs/myproject
      myproject

This is quite helpful as it shows the similarities and differences
between a `pyenv` installation of a version of Python, and a virtual
environment based on one of those installed versions.  They're
similar in that both represent a separate environment, with separate
sets of installed packages.  Virtual environments are organised
by the base version of Python.  So, our new environment `myproject`
appears twice.  One listing, `3.7.7/envs/myproject`, is more explicit;
it tells us that this environment is based on Python 3.7.7.
The other, `myproject`, is a convenient shortcut name, which we'll
typically use.

The virtual environment has been created, but we aren't yet using it.
To do that, we activate it:

	$ pyenv activate myproject
	(myproject) $ 

Helpfully, the Terminal prompt changes to include the name of the
virtual environment.  We can confirm which instance of the Python
interpreter executable we are using:

	(myproject) $ pyenv which python
	/Users/arbiter/.pyenv/versions/myproject/bin/python
	(myproject) $ python -V
	Python 3.7.7

Now we are all set!  When we install packages while using this
virtual environment, they appear only in this virtual environment;
they do not affect the system Python, the main Python 3.7.7
installation we did earlier via `pyenv`, or any other virtual
environment you might have.

If you want to switch virtual environments, you can just
`pyenv activate` it as above.  If you want to deactivate the
virtual environment, and drop back to the Python installation for
the current shell, do

	(myproject) $ pyenv deactivate
    $ pyenv which python
	/usr/local/bin/python

The name of the virtual environment disappears, and you're back,
in this case, to the system Python.


### A typical development workflow

We recommend having one virtual environment for each project you
are working on.  While it might be the case that at any given moment
of time you're using the same versions of packages across several
projects, there might be reasons you might change this.  For example,
suppose you are working on two projects, `foo` and `bar`, both of
which rely on `tegwar` version 1.0.0.  You finish developing and
testing `foo` and start running experiments.  At this point, you
should *always* use `tegwar` version 1.0.0 for `foo`; changing to
a different version might lead to subtle bugs or other changes in
behaviour.  However, if `bar` is still under development, and
`tegwar` version 1.1.0 comes out with helpful new features, you might
want to take advantage of those in developing `bar`.  If you have
been using different virtual environments for `foo` and `bar`,
you can accomplish this easily.

For example, suppose that you're going to use
[oTree](https://otree.org) as your framework for your experiment.
You would do the following steps:

	$ pyenv activate myproject
	$ pip3 install otree

This will install the latest version of oTree, and all its
dependencies.

!!! warning "A warning note"
    The oTree docs give very bad advice for installing oTree.
	They suggest installing it in the system or a downloaded Python,
	and not a virtual environment.
	Further, they suggest using the -U flag, which means "upgrade".
    You certainly can upgrade packages within a virtual environment,
	while you are developing your software.
	However it is safer to create a new one and install the latest
	versions there - because if for some reason the upgraded packages
	break your program, you have a fallback.

!!! warning "Another warning note"
    The oTree documentation also suggests running upgrade
	every several weeks.  We strongly advise against this.
	If you do want to migrate to a newer version of oTree,
	create a new virtual environment and test your software in
	that new virtual environment before migrating.
	Once you have fully tested and start running your experiment,
	you should *never* switch versions of oTree or another package.
	This is not only a matter of good software engineering but
	good science.  You should be able to reproduce your environment
	for running your experiment at any point in the future
	(e.g. when Referee 2 asks you to do something).

oTree depends on a lot of packages.  This is a great strength of
using Python for developing your program; there are libraries and
packages for doing many things, so you don't have to invent the wheel.
However, if you develop your program on your own computer, and then
go to test it in a lab, you want to be sure to be using the same
versions of not only oTree, but also all its dependent packages.
Fortunately, there's a very easy way to do this:

	(myproject) $ pip3 freeze

Will give you a (long!) list of all of the packages installed.
We will show you later how to use this effectively when preparing to
run your experiment on a computer that's different than the one you've
been programming it on.


!!! note "Using an IDE instead of the command line"
    The examples we've given here for activating a virtual environment
    and installing packages use the Terminal command line.
	If you are using an IDE (e.g. PyCharm) for developing and testing
	your software, consult the documentation for how to work with
	virtual environments.  All good IDEs will have strong support for
	this.


## A basic workflow

We'll now have a look at the basics of integrating virtual
environments and version control with the
[oTree tutorial](https://otree.readthedocs.io/en/latest/tutorial/part1_texteditor.html).

First, we create a virtual environment for our project, and install
oTree in it.  We'll use Python 3.7.7, as built in the previous section.

    $ pyenv shell 3.7.7
    $ pyenv virtualenv tutorial1
	$ pyenv activate tutorial1
	(tutorial) $ pip3 install otree

Now, go to the directory where you're going to put the source code
for your project.  In this example, I'm using a directory `src` off
the root directory for my account:

    $ cd ~/src
	(tutorial1) src $ otree startproject my_simple_survey
	(tutorial1) src $ cd my_simple_survey

!!! note "Virtual environments and project directories"
    Don't confuse _virtual environments_ with _project directories_.
    The project directory is where you'll keep all the source code
	for the experiment you're developing.  The virtual environment
	contains the Python interpreter and libraries you'll use to
	run the server.

The `otree startproject` command sets up a minimal working set of
files.  What you should do straightaway is set up a git repository,
which contains a snapshot of these files:

    (tutorial1) my_simple_survey $ git init
    Initialized empty Git repository in /Users/YOURNAME/src/my_simple_survey/.git/
	(tutorial1) my_simple_survey $ git add * .gitignore

This command stages everything in the project directory.  It also
includes the `.gitignore` file, which `otree startproject` also
creates and says which files should *not* be put under version
control.

    (tutorial1) my_simple_survey $ git status
    On branch master

    No commits yet

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)

	        new file:   .gitignore
   	        new file:   Procfile
	        new file:   _static/global/empty.css
	        new file:   _templates/global/Page.html
	        new file:   manage.py
	        new file:   requirements.txt
	        new file:   requirements_base.txt
	        new file:   settings.py

We then commit these files.

    (tutorial1) my_simple_survey $ git commit -m "Initial commit of default oTree project."
    [master (root-commit) 16d0c59] Initial commit of default oTree project.
     8 files changed, 78 insertions(+)
     create mode 100644 .gitignore
     create mode 100644 Procfile
     create mode 100644 _static/global/empty.css
     create mode 100644 _templates/global/Page.html
     create mode 100644 manage.py
     create mode 100644 requirements.txt
     create mode 100644 requirements_base.txt
     create mode 100644 settings.py

The idea of `git` is that it makes commits fairly easy to create and
work with.  You should get in the habit of committing often, by
breaking down the development of your program into individual steps,
and committing after each one.  If you do this, you can save yourself
a lot of grief, because you can always fall back on the last working
version if something goes awry!

Next you setup the application:

    (tutorial1) my_simple_survey $ otree startapp my_simple_survey
    (tutorial1) my_simple_survey $ git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

	        my_simple_survey/

    nothing added to commit but untracked files present (use "git add" to track)

Add the application files to version control and commit:

    (tutorial1) my_simple_survey $ git add my_simple_survey
    (tutorial1) my_simple_survey $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

	        new file:   my_simple_survey/__init__.py
	        new file:   my_simple_survey/_builtin/__init__.py
	        new file:   my_simple_survey/models.py
	        new file:   my_simple_survey/pages.py
	        new file:   my_simple_survey/templates/my_simple_survey/MyPage.html
	        new file:   my_simple_survey/templates/my_simple_survey/Results.html
	        new file:   my_simple_survey/tests.py

    (tutorial1) my_simple_survey $ git commit -m "Set up my_simple_survey app."
    [master 84ac43e] Set up my_simple_survey app.
     7 files changed, 108 insertions(+)
     create mode 100644 my_simple_survey/__init__.py
     create mode 100644 my_simple_survey/_builtin/__init__.py
     create mode 100644 my_simple_survey/models.py
     create mode 100644 my_simple_survey/pages.py
     create mode 100644 my_simple_survey/templates/my_simple_survey/MyPage.html
     create mode 100644 my_simple_survey/templates/my_simple_survey/Results.html
     create mode 100644 my_simple_survey/tests.py

Now follow the steps in the tutorial to edit `models.py` and `pages.py` and
define the templates as per the instructions there, and test your
program.  Once you've done all that, your working directory will look
something like this:

    (tutorial1) my_simple_survey $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

	        modified:   my_simple_survey/models.py
	        modified:   my_simple_survey/pages.py
	        modified:   my_simple_survey/templates/my_simple_survey/MyPage.html
	        modified:   my_simple_survey/templates/my_simple_survey/Results.html
	        modified:   settings.py

Save your work with another commit:

	(tutorial1) my_simple_survey $ git add my_simple_survey settings.py
	(tutorial1) my_simple_survey $ git commit -m "Finished initial implementation of survey."
	[master 2ff0631] Finished initial implementation of survey.
	 5 files changed, 20 insertions(+), 21 deletions(-)

## Good habits and practices

Here are some suggestions for working habits and practices using
`git` and virtual environments.

1.  Use one virtual environment for every project.  So, for example,
    if you were to move on to the next tutorial from the oTree
    documentation, create a different virtual environment
	(perhaps `tutorial2`), install oTree there, and use that
	virtual environment to run the program.  The rationale for this
	is that you will develop experiments at different rates, and
	you may want, for example, to use different versions of oTree
	or different versions of libraries.  It also means that when
	you create the list of required libraries when you go to deploy
	your software, you'll only have the ones you need for each
	experiment.

2.  Don't upgrade packages in a virtual environment.  The oTree
    documentation suggests upgrading oTree every few weeks with
	`pip install -U otree`.  This is *terrible* advice.  Generally
	speaking, you should stick with the same versions of any
	packages while developing, and use the same versions of packages
	when you test and when you deploy.  It's not per se important
	to have the latest version of packages; and, you may find that
	moving to a newer version of a package may actually break your
	code which was previously running just fine.

	If you *do* want to upgrade packages while you are developing
	your software, what you should do is create a new virtual
	environment, install the desired version of the packages there,
	and then test (extensively!) your program in the new virtual
	environment.  If you are satisfied everything is OK, then you
	can delete the old virtual environment and proceed with using the
	new one.  You should do this step separately and not while also
	making changes to your code, except for any changes to your code
	that are required by the new versions of the packages you are
	using.

3.  Break down your programming into small steps, and commit (with
    an informative commit message!) after each step.  As you become
	more comfortable with `git`, you can also start making use of
	branches to organise different parts of your development.

4.  Make heavy use of the "bots" feature of oTree to test your
    program.  Ideally run tests before each commit.  Don't forget to
	make bots do unexpected and non-obvious things.
 












	
