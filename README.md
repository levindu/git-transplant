git-transplant
==============

git-transplant is used to transplant internal development branch to message filtered public branch.

## Why we need it? ##
The development branch contains full developer information, which may not be appropriate for public.

So you rewrite all the commit messages. Blah. Due to the nature of Git, this public branch becomes a brand new independent branch which has no common commits with the development branch.

The development goes on. How to transplant those new commits to the public branch, instead of rewrite from scratch again and again?

git-transplant comes to rescue.

## Installation ##
Put it to any directory under your PATH, such as /usr/bin or /usr/local/bin:

    cp git-transplant /usr/bin/
    chmod 755 /usr/bin/git-transplant

## Usage ##

Suppose you have the "master" branch as the internal development branch.

Create "public" branch from development branch for the first time:

    git branch public master
    git filter-branch --env-filter '
    CORRECT_NAME="fae"
    CORRECT_EMAIL="fae@kernel.org"
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
    ' --tag-name-filter cat \
    --msg-filter '
    sed "s~: .\+<[^>]\+>~: $CORRECT_NAME <$CORRECT_EMAIL>~g"
    ' \
    -- public


Then use git-transplant for incremental transplanting:

    git-transplant --author "fae <fae@kernel.org>" master public

Get usage from command line:

    git-transplant --help
    usage: git transplant <original branch> <transplanted branch>
      -h, --help            show the help
      -q                    quiet
      -d                    show debug messages
      --author ...          in 'user <email>' form, override the commit/author
