---
title: "Flask Development Container"
date: 2021-09-29
weight: 20
---

For this project we will use flask as our web framework. Flask is native to python and is lightweight. It also allows us to use other native tools for integration with Python. Making Flask an ideal candidate. The initial setup is written for windows powershell, but the flask application will be run on Ubuntu.

## Prerequisites

 - Completion of all tasks under our [*Setting Up Docker*](/environments/docker/) article.

 ---

### Setting Up New Repo

In order to create an environment we need a repository to store our work under. create a new repo, in our case we have named it `leafriver`.

To create this new repo select the new repo button at the top left of the home screen.

![New-Repo](/images/environments/flask/new-repo.png)

You will then want to name the repo. We also want to initialise it with a README.md document as this will be a new project.

{{% notice info %}}
Create this as a private repo. This will be the repo that will transition into production.
{{% /notice %}}

![leafriver](/images/environments/flask/leafriver.png)


We will then create the repo by running the following commands

```sh
echo "# leafriver" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/mar0093/leafriver.git
git push -u origin main
```

Remove the readme file once the command is successful.

{{% notice info %}}
Authentication can be difficult when 'pushing' to the repo. I was able to authenticate once github prompted me for my **Username** & **Password** via Github Authentication window. Which only occurred on my second running of the command `git push -u origin main`
{{% /notice %}}

---

### Step 2. Connecting to Sourcetree

Once the new repository has been created we then need to add it to Sourcetree for version control.

To do this we click on a new tab. And navigate to the Remote window. We then search for then our newly named repo. We assume the Github account has already been added to Sourcetree.

{{% notice info %}}
Undertake the instructions below if the repository does not appear in sourcetree.
{{% /notice %}}
If the repository doesn't appear then this is likely due to Sourcetree not having permissions to view *Private* repositories. To get around this Execute the following.
1. Login into your Github account on any browser
2. From top right corner select SETTINGS
3. Now select DEVELOPER SETTINGS
4. From DEVELOPER SETTINGS select PERSONAL ACCESS TOKEN
5. Now from PERSONAL ACCESS TOKEN select GENERATE TOKEN
6. Fill Note as sourcetree and Check All Scopes from checkbox as show in below screenshots
7. After Click on Generate Token
8. Select *repo* as tp pass permission to pass under Scopes.
9. Edit your account.
10. Sign-in with your password as the Generated Token.
11. Click Save. (Private repositories should be appearing.)

Clone the Repository to a location. It should be near the same directory as the 'featherstream' directory from the previous article.

---

### Step 3. Preparing Repository

Now that the 'leafriver' repository has been created we want to generate a 'hello world!' container for flask.

Navigate to our new repo via Windows PowerShell.

#### Creating Directories

```sh
cd ~/PycharmProjects/leafriver
```

Create a parent directory for the application.

```sh
mkdir ./var/www/leafriverapp
```

Create the subdirectories `static` & `templates`. The static directory is where assets such as images & CSS. The templates directory is where you will put the HTML templates for your project.

```sh
cd ./var/www/leafriverapp
mkdir ./app/static
mkdir ./app/templates
```

#### Initial Scripts

We now want to write the `__init__.py` script. This will create a flask instance and import logic from the `views.py` file. (We will cover this in a second!)

```sh
cd app
bash -c 'vi __init__.py'
```

In the editor we add the following code.

```py
from flask import Flask
app = Flask(__name__)
from app import views
```

Now that we have written our `__init__.py` we need to write our `views.py` file. This will give us our 'Hello World!' page.

```sh
bash -c 'vi views.py'
```

In the editor we add the following code.

```py
from app import app

@app.route('/')
def home():
   return "hello world!"
```

#### Protocol & Server

We can now create the `uwsgi.ini` file. This file contains the configurations of our application. It is accepted by Nginx, it is both a protocol and an application server.

To create to file we run the following command.

```sh
cd ..
bash -c 'vi uwsgi.ini'
```

In the editor we add the following code.

```
[uwsgi]
module = main
callable = app
master = true
```

This module has 3 lines.

1. The first line defines the module that the Flask application will be served from. In this case, this is the main.py file, referenced here as main.
2. The second line instructs uWSGI to use the app instance exported by the main application.
3. The third line  allows your application to keep running, so there is little downtime even when reloading the entire application.

#### Main Script

Next, create the `main.py` file, which is the entry point to the application. The entry point instructs uWSGI on how to interact with the application.

```sh
bash -c 'vi main.py'
```

With this file we want to be able to the import the flask instance named `app`.  

```py
from app import app
```

#### Requirements file

A requirements file is setup in order to store the dependencies for the build. This will be run by the pip package manager when docker is deployed.

```sh
bash -c 'vi requirements.txt'
```

add the following line to the file as the initial dependency.

```
flask==1.0.2
Flask-SQLAlchemy==2.4.4
Flask-WTF==0.15.1
mysql-connector==2.2.9
sqlalchemy==1.3.23
protobuf==3.19.0
flask-login
```

---

### Step 4. Setting Up Container


#### Dockerfile

We now want to setup our container for our development work. To do this we will create what is known as a `Dockerfile`. It is a text document that contains commands that assemble the image.

It reads as below:

```txt
FROM tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7
RUN apk --update add bash nano
ENV STATIC_URL /static
ENV STATIC_PATH /var/www/app/static
COPY ./requirements.txt /var/www/requirements.txt
RUN pip install -r /var/www/requirements.txt
```

It will do the following:

1. Build an existing image; `tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7`
2. Add a nano text editor and also git.
3. Defines where to retrieve static material for images & CSS.
4. Pathing to the static location.
5. Copies requirements txt to the container.
6. Parses the dependencies outlined in the requirements.txt file.

the file should be placed in the location:

`/var/www/leafriverapp/Dockerfile`

#### Open Port

We need check whether there is an open port that we can use for our container to use to project our app. To do this on windows we run the following command.

```
 netstat -aon
```

We can see we are safe to use 8080. So that is what we will tell out `start.sh` to use in the next step.

#### Start Script

We will write a start script. The purpose of this script is to build our docker container with the parameters prescribed within our Dockerfile.

To write a new start file we write the command:

```txt
bash -c 'vi start.sh'
```

reminder, location is `/var/www/leafriverapp/start.sh`

We then put in the file the following:

```sh
#!/bin/bash
app="leafriverapp"
docker build -t ${app} .
docker run -d -p 8080:80 \
        --name=${app} \
        -v $PWD:/app ${app}
```

Run your new `start.sh` File

```sh
bash start.sh
```

Now when you run in your url `localhost:8080`  you should see a hello world page like the following.

![New-Repo](/images/environments/flask/hello-world.png)

I have also created a script that restarts the server.

```sh
#!/bin/bash
docker stop leafriverapp
docker start leafriverapp
```

Which can be run with the following command

```txt
bash rerun-leafriverapp.sh
```

Upon restrating the server with this command, all changes made to the code base will be applied. This script should be run before any commit is made to the repository.
