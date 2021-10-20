---
title: "Python Anywhere"
date: 2021-10-20
weight: 40
---

For this Project the production release of featherstream will be hosted on `Pythonanywhere`. Pythonanywhere is designed with hosting python scripts in mind. It has a GUI that allows for setting up the python environment, it also has integrated hosting for `mySQL` for databases and an automated scripting system called that runs `Cron Jobs`. Here we will outline the setup of the site for featherstrem using `Git` to push our master branch.  

## Perquisites


- Flask
    - Setup the development environment for the 'hello world' application.

### Step 1. Setup Featherstream on Pythonanywhere

We first need to make a account on Pythonanywhere. Without an acocunt, we have nowhere to push our code! Go the [pythonanywhere.com](https://www.pythonanywhere.com/) and create a hacker account.

{{% notice info %}}
This is $5 US, but we can disable this once this complete.
{{% /notice %}}

#### Bare repo
This is where we are going to host our cloned site.
Open up a new bash window, and create a 'bare repo.'

```
# From a Bash console on PythonAnywhere:
mkdir -p ~/bare-repos/leafriver.git
cd !$
git init --bare
ls # should show HEAD  branches  config...
```

#### Post-Receive Hook

We need to setup a post receive hook on the server. Change directory to

```sh
~/bare-repos/leafriver.git/hooks/post-receive
vi post-receive
```

in the file post the following text.

```sh
#!/bin/bash
GIT_WORK_TREE=/ git checkout -f
```
 We now allow the hook to be executable with the following command:

 ```
# In a Bash console on PythonAnywhere:
chmod +x ~/bare-repos/leafriver.git/hooks/post-receive
 ```
#### Pythonanywhere as Remote
On our local pc inside of `Powershell` we want to add pythonanywhere as a remote. We first change to the location of our repo, add the remote and do an initial push.

```
cd .\PycharmProjects\leafriver\
git remote add pythonanywhere featherstream@ssh.pythonanywhere.com:/home/featherstream/bare-repos/leafriver.git
git push -u pythonanywhere master
```

You will then be prompted for a password. This is the password we have setup for the account to access featherstream. PythonAnywhere provide a warning that reads:

{{% notice info %}}
You may get asked for your password at this point. If so, I strongly recommending setting up private key authentication instead, with a passphrase-encrypted private key on your machine, and adding your public key to ~/.ssh/authorized_keys on the server. There’s a good guide [here.](https://www.linode.com/docs/guides/use-public-key-authentication-with-ssh/)
{{% /notice %}}

We will address this at a later point.

#### Validation

We now should check that pythonanywhere is reading our repo as expected. In the bash terminal we want to run the following command.

```
ls /var/www/leafriverapp/app
```

we should see our own repo underneath there. (I am meaning to fix this).

we then go the wgsi.py file on the home page and update the code to read as the following.

```py
# +++++++++++ FLASK +++++++++++
# Flask works like any other WSGI-compatible framework, we just need
# to import the application.  Often Flask apps are called "app" so we
# may need to rename it during the import:
#
#
import sys

# The "/home/featherstream" below specifies your home
# directory -- the rest should be the directory you uploaded your Flask
# code to underneath the home directory.  So if you just ran
# "git clone git@github.com/myusername/myproject.git"
# ...or uploaded files to the directory "myproject", then you should
# specify "/home/featherstream/myproject"
path = '/var/www/leafriverapp/app/var/www/leafriverapp'
if path not in sys.path:
    sys.path.append(path)

from main import app as application  # noqa
```

We now see that PythonAnywhere is looking at our application! Okay so now we want to automate to the post-receive hook to automatically reload the site.

In our `post-receive` file we add the touch command to the file so that when the web worker notices a change to the site it is automatically reloaded.

```
#!/bin/bash
GIT_WORK_TREE=/var/www/leafriverapp/app git checkout -f
touch /var/www/featherstream_pythonanywhere_com_wsgi.py
```

So now when make a change and push we should see our changes in production!

```
git commit -am"testing hook to pythonanywhere production
git push pythonanywhere"    
```

### Helpful references for the article were



https://medium.com/@aadibajpai/deploying-to-pythonanywhere-via-github-6f967956e664

https://blog.pythonanywhere.com/87/
