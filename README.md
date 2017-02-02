# ansible-gitpush-deploy

*Sets up a deployment scheme based on git-pushing from the control machine.*

This role sets up a system on target machines where we have a bare git repo and a target workdir
as well as a git folder on the *local control machine*. When this role is ran, we setup our remote
bare git repo with a `post-receive` trigger that checks out the pushed commit into our work dir.
Then, we push the `HEAD` of our local repo into that remote bare repo and let the `post-receive`
hook do its job.

This deployment system is described in [this blog post][gitpush-deployment-article].

**Important:** this role is designed to run as an unprivileged user. See below.

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
      become: yes
      become_user: myproject_user
```

Mandatory variables are:

* `gitpush_local_repo_path`
* `gitpush_repo_path`
* `gitpush_workdir_path`

### Designed to run as unprivileged

To avoid permission errors, you should properly `become` when calling this role.

This role is designed so it can run directly with an unprivileged `ansible_user` (without `become`),
but it still tries to install git. So if your target isn't sure to already have `git` installed,
you should initially run this role from a superuser (with proper `become`-ing to your unprivileged
user. `gitpush-deploy` will override `become` to `no` before trying to install git.

Alternatively, you can simply make sure that git is already installed.

[gitpush-deployment-article]: http://krisjordan.com/essays/setting-up-push-to-deploy-with-git
