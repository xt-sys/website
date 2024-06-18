+++
title = 'Working with GIT'
date = 2024-06-17T17:31:15+02:00
+++
The preferred method of contributing patches to ExectOS is via our self-hosted [Gitea](https://git.codingworkshop.eu.org)
instance.

### Initial Setup
First of all you will have to create an account on our [Gitea](https://git.codingworkshop.eu.org) instance. Do note that
you can also use your existing GitHub or GitLab account via OAuth. Creating new account on our Gitea is pretty straight
forward, anyway:
 1. Go to our Gitea instance and click on "[Register](https://git.codingworkshop.eu.org/user/sign_up)" link in top,
    right corner.
 2. Fill in your information.
 3. Add your SSH key(s) for easier pushing via SSH instead of HTTPS.
 4. Consider also enabling 2FA for improved security access and signing your commits with GPG.

### Cloning Repositories
 1. Go to Gitea's frontage after you’ve signed in and search for the repository you want to clone. Click on its name to
    open it.
 2. Click on 'Fork' button, to create your own copy of the repository.
 3. Either clone your fork of it, with
    ```
    git clone ssh://git@git.codingworkshop.eu.org:8822/<username>/<repository>.git .
    ```
    or if you already cloned the repo you can add your fork as remote, with
    ```
    git remote add <my_fork> ssh://git@git.codingworkshop.eu.org:8822/<username>/<repository>.git
    ```

### Proper GIT Setup
ExectOS uses GIT for Source Code Management (SCM) and GIT uses a username to associate commits with an identity. The GIT
username is not the same as your Gitea username. You can change the name that is associated with your GIT commits. The new
name you set will be visible in any future commits you push to Gitea from the command line. In order to accept your
patches, we require you to provide your real name and valid e-mail address. This can be done with the following commands:
```
git config --global user.name "<your real name>"
git config --global user.email "<your email>"
```
Above command will update username and email globally, for every repository on your computer. Alternatively, these changes
can be introduced for a single repository, by changing the current working directory to the local repository where you want
to configure the name that is associated with your GIT commits and issueing the following commands:
```
git config user.name "<your real name>"
git config user.email "<your email>"
```

### Keeping Forks Updated
You should always ensure that you have the latest version of the repository you want to work on before starting your work to
avoid duplicating work and merge conflicts. You can do so with:
```
git pull <remote> master --rebase
```
