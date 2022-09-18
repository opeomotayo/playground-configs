Branching Protection Strategy

Purpose prevent merging directly to the master/main branch
The idea is any branch can merge to develop branch and only develop branch can merge to master/main branch

Step1: Protect merging manually on the UI
Solution:
create a github workflow 
create .github/workflows/merge-master.yaml file
add the the below content:
on:
  pull_request:
    branches:
      - master
jobs:
  merge_check:
    runs-on: ubuntu-latest
    steps:
      - name: Check if the pull request is mergeable to master
        run: |
             if [[ "$GITHUB_HEAD_REF" == 'develop' ]]; then exit 0; else exit 1; fi;

In addition to the above go to the repo settings, branches, add rule.
set Branch name pattern to master/main
select the following checkboxes only:
Require a pull request before merging
Require status checks to pass before merging
Require branches to be up to date before merging
search for merge_check and select it
Include administrators 

Save changes.



Step2: Protect merging using command line interface on the terminal
====================================================================
create .git/hooks/prepare-commit-msg file
#!/usr/bin/env ruby

# This git hook will prevent merging specific branches into master
# Put this file in your local repo, in the .git/hooks folder
# and make sure it is executable.
# The name of the file *must* be "prepare-commit-msg" for Git to pick it up.

FORBIDDEN_BRANCHES = ["staging"]

def merge?
  ARGV[1] == "merge"
end

def into_master?
  current_branch = `git branch | grep '*' | sed 's/* //'`.chop
  current_branch == "master"
end

def merge_msg
  @msg ||= `cat .git/MERGE_MSG`
end

def from_branch
  @from_branch = merge_msg.match(/Merge branch '(.*?)'/)[1]
end

def from_forbidden_branch?
  FORBIDDEN_BRANCHES.include?(from_branch)
end

if merge? && into_master? && from_forbidden_branch?
  out = `git reset --merge`
  puts
  puts " STOP THE PRESSES!"
  puts " You are trying to merge #{from_branch} into the *master* branch."
  puts " Surely you don't mean that?"
  puts
  puts " run the following command now to discard your working tree changes:"
  puts
  puts " git reset --merge"
  puts
  exit 1
end
====================================================================
#!/usr/bin/env ruby

# This git hook will prevent merging specific branches into master
# Put this file in your local repo, in the .git/hooks folder
# and make sure it is executable.
# The name of the file *must* be "prepare-commit-msg" for Git to pick it up.

ALLOWED_BRANCHES = ["develop"]

def merge?
  ARGV[1] == "merge"
end

def into_master?
  current_branch = `git branch | grep '*' | sed 's/* //'`.chop
  current_branch == "master"
end

def merge_msg
  @msg ||= `cat .git/MERGE_MSG`
end

def from_branch
  @from_branch = merge_msg.match(/Merge branch '(.*?)'/)[1]
end

def from_allowed_branch?
  ALLOWED_BRANCHES.include?(from_branch)
end

if merge? && into_master? && !from_allowed_branch?
  out = `git reset --merge`
  puts
  puts " STOP THE PRESSES!"
  puts " You are trying to merge #{from_branch} into the *master* branch."
  puts " Surely you don't mean that?"
  puts
  puts " run the following command now to discard your working tree changes:"
  puts
  puts " git reset --merge"
  puts
  exit 1
end
====================================================================




References: 
https://github.community/t/improvement-suggestion-settings-branch-pattern-that-can-only-merge-to-target-branch-pattern/171870
https://bl.ocks.org/slattery/5eea0d6ca64687ecba6b
https://stackoverflow.com/questions/46146491/prevent-pushing-to-master-on-github
https://github.com/talenavi/husky-precommit-prepush-githooks


