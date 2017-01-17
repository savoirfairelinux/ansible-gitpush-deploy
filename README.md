# ansible-gitpush-deploy

*Sets up a deployment scheme based on git-pushing from the control machine.*

This role sets up a system on target machines where we have a bare git repo and a target workdir
as well as a git folder on the *local control machine*. When this role is ran, we setup our remote
bare git repo with a `post-receive` trigger that checks out the pushed commit into our work dir.
Then, we push the `HEAD` of our local repo into that remote bare repo and let the `post-receive`
hook do its job.

This deployment system is described in [this blog post][gitpush-deployment-article].

## Requirements

* Ansible 2.2+
* One of the supported target systems:
  * Ubuntu 16.04
* Git installed on the control machine

## Usage

You call this role as with any other roles. See [vars file](defaults/main.yml) for customisation
options.

```yaml
- name: Deploy our project through git push
  hosts: all
  
  roles:
    - role: gitpush-deploy
      gitpush_local_repo_path: gitrepos/myproject/.git
      gitpush_repo_path: /opt/myproject/gitrepo
      gitpush_workdir_path: /opt/myproject/www
      gitpush_owner: myproject_user
      gitpush_group: www-data
```

Mandatory variables are:

* `gitpush_local_repo_path`
* `gitpush_repo_path`
* `gitpush_workdir_path`

[gitpush-deployment-article]: http://krisjordan.com/essays/setting-up-push-to-deploy-with-git
