---
title: "User Table"
date: 2021-11-09
weight: 20
---

This article outlines how we setup our first table, our User table.

---

## Prerequisites

- Completion of all tasks under our [*Initialise Database*](/databases/initialise-db/) article.

---

### Step 1. Install Dependencies

Our dependencies include

```txt
flask-login
werkzeug
email_validator==1.1.3
pymysql
```

### Step 2. Add Register to Navbar

Add *Register* to the Navbar. This is where we will allow new users to register to use Featherstream.
If you haven't already established the navbar the html file can be found undertaken

`var/www/leafriverapp/app/templates/includes/_navbar.html`

```HTML
//list object for register page link.
<li class="nav-item">
  <a class="nav-link" href="{{url_for('register')}}">Register</a>
</li>
```
The `{{url_for('function')}}` looks for a route, in this case register. And will initiate the route to run from the views.py file.
We will come back to the

### Step 3. Create a register HTML file

The Register HTML file appears as follows.

```HTML
{% extends 'layout.html' %}

{% block body %}


<h1>Register</h1>
<br/>
<form method="POST">
    {{ form.hidden_tag() }}

    {{ form.first_name.label(class="form-label") }}
    {{ form.first_name(class="form-control") }}
    <br/>
    {{ form.last_name.label(class="form-label") }}
    {{ form.last_name(class="form-control") }}
    <br/>
    {{ form.email.label(class="form-label") }}
    {{ form.email(class="form-control") }}
    <br/>
    {{ form.username.label(class="form-label") }}
    {{ form.username(class="form-control") }}
    <br/>
    {{ form.password_hash.label(class="form-label") }}
    {{ form.password_hash(class="form-control") }}
    <br/>
    {{ form.password_hash2.label(class="form-label") }}
    {{ form.password_hash2(class="form-control") }}
    <br/>
    {{ form.submit(class="btn btn-primary") }}
</form>
<br/><br/><br/>

{% endblock %}
```

The file imports the navbar with the 'layout.html' file. But the typical body of the file is just the form to
register a new user. The form.hidden_tag () template argument generates a hidden field that includes a token
that is used to protect the form against CSRF attacks. All you need to do to have the form protected is
include this hidden field and have the SECRET_KEY variable defined in the Flask configuration.
The rest of the objects are the UserForm() attributes defined under `/var/www/leafriverapp/app/models.py`.
As well as a submit button. to add the user, granted all parameters passed are valid.

### Step 4. Create User Model

In step 3 we can see that we have a form that has 6 fields expecting information. Yet at this point we haven't created the form,
nor have created the model that will populate the form. We will define both these required elements here.

##### User Model

The User model is all the basic information we wish to store of our users.

```py
# Create model

class Users(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(20), nullable=False, unique=True)
    first_name = db.Column(db.String(100), nullable=False)
    last_name = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(120), nullable=False, unique = True)
    date_added = db.Column(db.DateTime, default=datetime.utcnow)
    password_hash = db.Column(db.String(128))

    def __repr__(self):
        return '<Name %r>' % self.first_name
```

We can see that for our Users class we are initialising it with a db.Model. This means we are
using the parent class `db.Model` for our subclass. The `db` is a defined object from the SQLAlchemy wrapper.
that we define as follows.

```py
app.config['SQLALCHEMY_DATABASE_URI'] = ConfigDb.config_uri
app.config['SECRET_KEY'] = ConfigDb.secret_key

db = SQLAlchemy(app)
```

{{% notice info %}}
Since our development environment is within docker we replace `localhost` with `host.docker.internal`.
This will be replace with the correct IP once in production.
{{% /notice %}}

We can also observe from our *Users* class is how it allows us to define our objects. Each object is a database column for *MySQL*.
We define the parameters by first defining that it is indeed, al column; db.Column(), we then pass the first parameter
as the column type (and length if applicable. This is the only required parameter.) And following this we pass any other
attributes we want the column to have. (E.g. primary_key, nullable, unique, default)

##### User Form

The User form is a class that applies the FlaskForm wrapper. Within our Userform we are able to define our form items.
Mainly, the form defines fields and buttons for the form in this case.

This object can be found under the file:
`var/www/leafriverapp/app/models.py`

```py
#Create user register form

from wtforms import StringField, SubmitField, PasswordField, BooleanField, ValidationError
from wtforms.validators import DataRequired, Email, EqualTo, Length
from flask_wtf import FlaskForm

class UserForm(FlaskForm):
    first_name = StringField("First Name", validators=[DataRequired()])
    last_name = StringField("Last Name", validators=[DataRequired()])
    username = StringField("Username", validators=[DataRequired()])
    email = StringField("Email", validators=[DataRequired(), Email()])
    password_hash = PasswordField('Password', validators=[DataRequired(), EqualTo('password_hash2', message='Passwords Must Match!')])
    password_hash2 = PasswordField('Confirm Password', validators=[DataRequired()])
    submit = SubmitField("Submit")
```

The required parameter is the string that will be the name of the form object. We can also pass validators so the field on accepts valid input
for its particular field.

##### Creating The Users table In DB

To create the table we have to run a couple of commands in the loccal python interpreter.

First, we head into the folderstructure of our container dev environment.

```sh
docker exec -it leafriverapp /bin/bash
```

We then run python.

```sh
python
```

We then import our create db object that we defined under models.py

```py
from app.models import db
```

The output should appear like the following

```txt
/usr/local/lib/python3.6/site-packages/flask_sqlalchemy/__init__.py:834: FSADeprecationWarning: SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and will be disabled by default in the future.  Set it to True or False to suppress this warning.
  'SQLALCHEMY_TRACK_MODIFICATIONS adds significant overhead and '
```

If our output looks as expected we can then run the following commands

```py
db.create_all()
```
This command will create all tables within the db that do not exist. If the table already exists, the command will do nothing.

### Step 5. User View

Once the User Model, the register  and the User Form are created we can then bundle them together to create the view.
The View script is what flask runs when passed the relevant address. In this case, the address is `/register`.

```py
@app.route('/register', methods=['GET', 'POST'])
def register():
    first_name = None
    form = UserForm()
    if form.validate_on_submit():
        user = Users.query.filter_by(email=form.email.data).first()
        if user is None:
        	# Hash the password!!!
            hashed_pw = generate_password_hash(form.password_hash.data, "sha256")
            user = Users(username=form.username.data, first_name=form.first_name.data, last_name=form.last_name.data, email=form.email.data, password_hash=hashed_pw)
            db.session.add(user)
            db.session.commit()
        first_name = form.first_name.data
        form.first_name.data = ''
        form.username.data = ''
        form.email.data = ''
        form.last_name.data = ''
        form.password_hash.data = ''
        flash("User successfully registered.")
    our_users = Users.query.order_by(Users.date_added)
    return render_template("register.html",
        name = first_name,
        form = form,
        our_users = our_users)
```

The script will run the register function. The crux of what will happen, is the `return` function will run the register.html page,
it will also pass the other parameters that we have set. Upon accepting a successful user request we will return a flash message.
These flash messages are stored under `var/www/leafriverapp/app/templates/includes/_includes.html`
