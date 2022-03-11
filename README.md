How to Deploy Flask  Applications on Heroku
===========================================

![heroku-django](https://i.ytimg.com/vi/sqJSdJbOOU0/maxresdefault.jpg)
# Install heroku CLI
[Sign up](https://signup.heroku.com/) to Heroku.

Then install the [Heroku Toolbelt](https://toolbelt.heroku.com/). It is a command line tool to manage your Heroku apps

After installing the Heroku Toolbelt, open a terminal and login to your account:
```bash
$ heroku login
Enter your Heroku credentials.
Email: albertbyrone@herokudeploying.com
Password (typing will be hidden):
Authentication successful.
```

# Preparing the Application
## Assumptions
* Your familiar with the basics of flask e.g concept of apps, settings, basics of databases 
* You have flask application that you want to deploy to heroku
* You are familiar with virtual environments - not a must but the knowledge would be a plus
* Your deployment db is postgres

We need to add the following to our project, we will cover each of them in detail in the below section

* Add `requirements.txt` file with all the requirements in the project root;
* Add `Gunicorn` to `requirements.txt`;
* A `.env` to store all the variable configurations needed in the  project root;


## Procfile
A `Procfile`  is a mechanism for declaring what commands are run by the Heroku environment We will create a file in our root folder and name it Procfile

In our case we want to run our Flask application using gunicorn.

Create a file named `Procfile` in the project root with the following content:
```
web: gunicorn manage:app
```
## Set up Postgres on Heroku

This database's URI will be stored in Heroku in the configuration variable DATABASE_URL. We then need to define this new Database URI inside our config.py

`config.py`

```bash
class ProdConfig(Config):
    SQLALCHEMY_DATABASE_URI = os.environ.ge("DATABASE_URL")
```


`app/__init__.py`  

```bash
app.config['SQLALCHEMY_DATABASE_URI'] = os.environ.ge("DATABASE_URL")
app.config['SQLALCHEMY_TRACK_MODIFICATIONS']=False

```



`manage.py`

```bash
app = create_app('production')
```


# Lets deploy now
First make sure you are in the root directory of the repository you want to deploy

Next create the heroku app
```bash
heroku create <your-app>
```

Create a postgres addon to your heroku app
```bash
heroku addons:create heroku-postgresql:hobby-dev
```
Next we log in to [Heroku dashboard](https://dashboard.heroku.com) to access our app and configure it

![Imgur](https://i.imgur.com/dbDQxlJ.png)

Click on the Settings menu and then on the button Reveal Config Vars:
Next add all the environment vaiables, by default you should have `DATABASE_URI` configuration created after installing postgres to heroku.

Alternatively you can add all your configurations in `.env` file directly to heroku by running the this command.

```bash
heroku config:set $(cat .env | sed '/^$/d; /#[[:print:]]*$/d')
```
Remember to first set `DEBUG` to false and confirm that you have added all the confuguration variables needed.

![Imgur](https://images2.imgbox.com/20/33/6ApMpPQu_o.png)

### Pushing to heroku

confirm that your application is running as expected before pushing, runtime errors will cause deployment to fail so make sure you have no bugs, you have all the following `Procfile` and  `requirements.txt` .

```bash
$ git push heroku master
```

if you find an error where the heroku git initialization has turned out unsuccessful; run the following command
```
bash
$ heroku git:remote -a <application-name>
```
If you did everything correctly then the deployment should be done after a while with an output like this

```bash
Enumerating objects: 94, done.
Counting objects: 100% (94/94), done.
Delta compression using up to 8 threads.
Compressing objects: 100% (84/84), done.
Writing objects: 100% (94/94), 3.35 MiB | 630.00 KiB/s, done.
Total 94 (delta 24), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote: 
remote: -----> Python app detected
remote: -----> Installing python-3.6.6
remote: -----> Installing pip
remote: -----> Installing requirements with pip
remote:        Collecting config==0.3.9 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 1))
remote:          Downloading https://files.pythonhosted.org/packages/0a/46/186ac016f3175211ec9bb4208579bc6dc9dd7dc882790d9f281533b83b0f/config-0.3.9.tar.gz
remote:        Collecting dj-database-url==0.5.0 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 2))
remote:          Downloading https://files.pythonhosted.org/packages/d4/a6/4b8578c1848690d0c307c7c0596af2077536c9ef2a04d42b00fabaa7e49d/dj_database_url-0.5.0-py2.py3-none-any.whl
remote:        Collecting Django==1.11 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 3))
remote:          Downloading https://files.pythonhosted.org/packages/47/a6/078ebcbd49b19e22fd560a2348cfc5cec9e5dcfe3c4fad8e64c9865135bb/Django-1.11-py2.py3-none-any.whl (6.9MB)
remote:        Collecting django-bootstrap3==10.0.1 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 4))
remote:          Downloading https://files.pythonhosted.org/packages/18/a8/f12d8491155c7f237084b883b8600faf722e3a46e54f17a25103b0fb9641/django-bootstrap3-10.0.1.tar.gz (40kB)
remote:        Collecting django-heroku==0.3.1 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 5))
remote:          Downloading https://files.pythonhosted.org/packages/59/af/5475a876c5addd5a3494db47d9f7be93cc14d3a7603542b194572791b6c6/django_heroku-0.3.1-py2.py3-none-any.whl
remote:        Collecting gunicorn==19.9.0 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 6))
remote:          Downloading https://files.pythonhosted.org/packages/8c/da/b8dd8deb741bff556db53902d4706774c8e1e67265f69528c14c003644e6/gunicorn-19.9.0-py2.py3-none-any.whl (112kB)
remote:        Collecting Pillow==5.2.0 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 7))
remote:          Downloading https://files.pythonhosted.org/packages/d1/24/f53ff6b61b3d728b90934bddb4f03f8ab584a7f49299bf3bde56e2952612/Pillow-5.2.0-cp36-cp36m-manylinux1_x86_64.whl (2.0MB)
remote:        Collecting psycopg2==2.7.5 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 8))
remote:          Downloading https://files.pythonhosted.org/packages/5e/d0/9e2b3ed43001ebed45caf56d5bb9d44ed3ebd68e12b87845bfa7bcd46250/psycopg2-2.7.5-cp36-cp36m-manylinux1_x86_64.whl (2.7MB)
remote:        Collecting python-decouple==3.1 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 9))
remote:          Downloading https://files.pythonhosted.org/packages/9b/99/ddfbb6362af4ee239a012716b1371aa6d316ff1b9db705bfb182fbc4780f/python-decouple-3.1.tar.gz
remote:        Collecting pytz==2018.5 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 10))
remote:          Downloading https://files.pythonhosted.org/packages/30/4e/27c34b62430286c6d59177a0842ed90dc789ce5d1ed740887653b898779a/pytz-2018.5-py2.py3-none-any.whl (510kB)
remote:        Collecting whitenoise==3.3.1 (from -r /tmp/build_19aebf8f25d534a39e73b13219af9927/requirements.txt (line 11))
remote:          Downloading https://files.pythonhosted.org/packages/0c/58/0f309a821b9161d0e3a73336a187d1541c2127aff7fdf3bf7293f9979d1d/whitenoise-3.3.1-py2.py3-none-any.whl
remote:        Installing collected packages: config, dj-database-url, pytz, Django, django-bootstrap3, whitenoise, psycopg2, django-heroku, gunicorn, Pillow, python-decouple
remote:          Running setup.py install for config: started
remote:            Running setup.py install for config: finished with status 'done'
remote:          Running setup.py install for django-bootstrap3: started
remote:            Running setup.py install for django-bootstrap3: finished with status 'done'
remote:          Running setup.py install for python-decouple: started
remote:            Running setup.py install for python-decouple: finished with status 'done'
remote:        Successfully installed Django-1.11 Pillow-5.2.0 config-0.3.9 dj-database-url-0.5.0 django-bootstrap3-10.0.1 django-heroku-0.3.1 gunicorn-19.9.0 psycopg2-2.7.5 python-decouple-3.1 pytz-2018.5 whitenoise-3.3.1
remote: 
remote: -----> Discovering process types
remote: 
remote: -----> Compressing...
remote:        Done: 56.3M
remote: -----> Launching...
remote:        Released v6
remote:        https://mtr1bune.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy... done.
To https://git.heroku.com/mtr1bune.git
 * [new branch]      master -> master

 ```


We now need to define our database schema on our Heroku database.

```bash
$ heroku run python3.6 manage.py db upgrade
```
You can the open the app in your browse.
