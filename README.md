# App Interface release scripts

### Requirements
- gh (GitHub command line client for api access)
- jq (json cli processor)
- awk (black magic witchcraft language that you only see in hacky stuff like this)

1) `get_all_$(app)_shas.sh`

Run this to get the sha's to stick in your deploy.yml. 
Output is similar to this: 
```
Branch: stable
ansible_tower: 484abf0
api: 9c565b2
azure: 24ff4fe
amazon: 327d395
openshift: 7fc8af1
persister: bf5fc1c
satellite: a275f27
orchestrator: 0471eda
ingress_api: 0e9f5b0
sync: e4db79a
```

2) `commit_and_pr_all_repos.sh`

Tiny little script to pr all the repos, useful for updating the UBI image tag of everything.  
*Be warned, the paths are probably not indicative of what is in your env. I have symlinks.*

First step is to update all the dockerfiles, I usually do this with `vim $(fzf)` and select all the dockrefiles one wants to update. 

Run it like so:  
` commit_and_pr_all_repos.sh "Update ubi image to ubi8:13.37" "This PR Updates the UBI Image to the latest one to fix CVE #8675309"`  

It basically creates PRs with arg 1 as the commit message, and arg 2 as the PR message.

3) `patch_app_interface.rb`

This is a **very heavy** work in progress that will automatically update all of the SHA tags for $ENV in the $APPLICATION deploy.yml files.     

**Don't use it it doesn't work yet.**

4) `bundle_all.sh`

Simple one, this one goes through and finds all the Gemfiles in subdirectories and runs:   

`bundle update && bundle clean`   

in each. Easy but useful!

5) `inspect.sh`

This one allows you to look at a specific file running on OCP so you can see what changes are there. 
The arguments are:
1) regex to match the running deployment, e.g. to match topological-inventory-ansible-tower-operations `tower-op` would work
2) the file to inspect, starting at the web root (which is usually just the rails root or the beginning of the project)

Example:  

`inspect.sh tower-op lib/topological_inventory/ansible_tower/operations/applied_inventories/parser.rb`

Will print out that file pulling the running image from whichever namespace you are in, useful for checking if stage/prod have changes!
