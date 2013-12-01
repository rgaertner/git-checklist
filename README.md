# Git Checklist

I was juggling lots of private git branches at work and making some use
of the `BRANCH_DESCRIPTION` file (see `git branch --edit-description`)
to store my notes for each branch. I soon realise what I wanted was to
just store my TODOs in each branch and mark them off as I went.

And so was born `git-checklist`, though in actual fact I've aliased it
to `git todo`. Assuming it's in your path:

    $ git config alias.todo=checklist

Otherwise, like this:

    $ git config alias.todo=!/path/to/git-checklist

## Using It Is Easy

To view your TODOs, just enter the bare command from a git repository:

    $ git todo

Well, if you've not got anything to do yet, it won't have anything to
show you. Find out where to go next:

    $ git todo --help
    Usage: git-checklist ([ARG] | [COMMAND])
      Per-branch TODO list for Git repositories

    Available options:
      -h,--help                Show this help text

    Available commands:
      show                     Show current TODOs
      add                      Add a TODO
      done                     Mark a TODO as done.
      undo                     Item needs redone!
      remove                   Remove a TODO (can't be undone)

It will give you a summary of the commands you can enter at this point.
There are no entries yet so all you can do is add them.

    $ git todo add Add an informative README file
    1: [ ] Add an informative README file

Each entry has a number in the left column and a box. Empty boxes mean
the item is still to be done. When you've finished a task, mark it done
using the number as a reference:

    $ git todo done 1
    1: [x] Add an informative README file

You can also "undo" items if you realise that the fix you made wasn't so
hot after all, or you didn't understand the issue fully, or whatever
other reason.

Finally you can remove items entirely from the list. This is permanent
and can't be undone.

## Advanced Usage

If you're knee deep in work on one branch and you'd like to change the
checklist for another branch, all the commands will recognise the
`--branch` (or `-b`) option.

    $ git todo add --branch newparser Make nicer error messages

If you want to start a description of your note with a hyphen you can
separate it from the rest of the command options with a double-hyphen on
its own.

    $ git todo add -- -b stopped working but --branch still okay

## Background Details

The checklists are stored in `.git/checklist/<branchname>` in a
serialised form of the internal data structure called `ToDo`. If you
open up one of these files it's easy to edit though you might find
breakage occurs!

The checklist for the working directory is whatever branch name is
pointed to by `HEAD`. I have not tested it with a detached head, though I
guess you'd just end up with a SHA as a branch name.

## Installing It Is Fairly Easy

Assuming you've got a Haskell installation (if not, [grab the Haskell
platform] [hp]) you can grab the source and build with Cabal.

    $ git clone http://github.com/dougalstanton/git-checklist
    $ cd git-checklist
    $ cabal build
    $ cabal install

[hp]: <http://www.haskell.org/platform>
    "Haskell for Windows, Mac OS X and Linux"

## Shortcomings

The data is stored in a separate directory inside `.git` which isn't
versioned --- this is a local list only. I am open to suggestions to
make this versioned though it is not a priority for me. My workflow is
based around a single computer.

The read/write step bashes into the awkwardness of lazy IO.

The serialisation is quick-and-dirty using Haskell's `deriving
(Read,Show)` functionality which means not only is it brittle but the
error messages on failure are poor and the storage format could be
better too. In future I'd like to use a line-oriented format which is
easier to edit manually if necessary.

If you have any more than 9 items in a list the pretty printing will no
longer look as pretty because only 1 character is allowed for the
numbers. Any more than that and the checkboxes no longer line up --- not
pretty!

It doesn't cope very well with not being somewhere inside a repository.
It shouldn't wreck anything but the error message isn't very refined.
