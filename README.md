Packtpub Django2
Oct 15, 2018, MOn
pg229

http://localhost:8000/account/login/
http://c19e1fb6.ngrok.io/account/login/

#Django
cd /home/hal/Documents/softwares/pycharm/workspace/myshop_docker

#database
psql -d myshop

#Super User
username: hal
email: halvong@yahoo.com
password: wesleyave

#ngrok
./ngrok http 8000

#User


#commands
docker-compose exec web python manage.py createsuperuser


#myshop docker steps, Oct 15, 2018 
1. docker run -i -t --rm -v $PWD:/usr/src/app python:3.6.3 bash
2. pip install --upgrade pip
3. pip install Django==2.1.2
   python -m django --version
4. django-admin startproject mysite
6. exit; sudo chown -R $USER:$USER mysite
7. source ~/venv3/bin/activate
8. cd mysite; python manage.py startapp [app name]
9. python manage.py migrate 
10. move/create Dockerfile, docker-compose.yml, README.md, requirements.txt to root directory, keep SQL lite temporarily
11. (optional) 
    docker build -t <image_name> . 
            or
    docker build .
    docker run -p 8000:8000 5a95621bd372 \
    python manage.py runserver 0.0.0.0:8000
    docker tag [image id] [image name:1.0]
12. (optional) 
i   docker run -p 8000:8000 [image name]
13. (creates database container to create database)
    docker-compose up -d database
        because database "shop" needs to exits first 
14. settings.py 
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.postgresql_psycopg2',
                'NAME': 'shop',
                'USER': 'postgres',
                'PASSWORD': 'postgres',
                'HOST': 'database',
                'PORT': 5432,
            }
        }
15. docker-compose run --rm database psql -U postgres -h database
    password: postgres   
16. create database shop;
17. docker-compose stop
18. deletes SQL lite (if exists)        
19. docker-compose up web or docker-compose up --build
20. docker-compose exec web python manage.py makemigrations 
        Migrations for 'shop':
          shop/migrations/0001_initial.py
            - Create model Category
            - Create model Product
            - Alter index_together for product (1 constraint(s))
21. docker-compose exec web python manage.py migrate
        Running migrations:
          Applying contenttypes.0001_initial... OK
          Applying auth.0001_initial... OK
          Applying admin.0001_initial... OK
          Applying admin.0002_logentry_remove_auto_add... OK
          Applying admin.0003_logentry_add_action_flag_choices... OK
          Applying contenttypes.0002_remove_content_type_name... OK
          Applying auth.0002_alter_permission_name_max_length... OK
          Applying auth.0003_alter_user_email_max_length... OK
          Applying auth.0004_alter_user_username_opts... OK
          Applying auth.0005_alter_user_last_login_null... OK
          Applying auth.0006_require_contenttypes_0002... OK
          Applying auth.0007_alter_validators_add_error_messages... OK
          Applying auth.0008_alter_user_username_max_length... OK
          Applying auth.0009_alter_user_last_name_max_length... OK
          Applying sessions.0001_initial... OK
          Applying shop.0001_initial... OK
22. docker-compose exec database psql -U postgres -h database
                            or
    docker-compose run --rm database psql -U postgres -h database
    
#notes docker steps 
1. docker-compose up -d database 
   docker-compose run --rm database psql -U postgres -h database
   make sure the database exists
   
2. docker-compose logs database 
3. docker-compose build web 
4. docker-compose up -d web 
2. docker-compose logs -f web 
5. docker-compose exec web python manage.py makemigrations account
6. docker-compose exec web python manage.py sqlmigrate blog 0001 
7. docker-compose exec web python manage.py migrate
8. docker-compose exec web python manage.py createsuperuser
9. docker-compose exec web python manage.py startapp [app name]
10. docker-compose exec web python manage.py shell_plus

#django_site does not exists error
./manage.py migrate sites
./manage.py migrate

#check containers running
docker-compose ps

#starts all containers
docker-compose up -d 

#----
#gunicorn manual
gunicorn --bind 0.0.0.0:8000 wisdompets.wsgi:application

#----
#migrate
python manage.py makemigrations books
python manage.py sqlmigrate blog 0001
#assume django in container 
docker-compose exec [web] python manage.py sqlmigrate blog 0001
docker-compose exec [service] python manage.py migrate
docker-compose exec web python manage.py collectstatic

#force stop
docker stop $(docker ps -a -q)
#remove
docker rm $(docker ps -a -q)

#--------------------------------
#uses Dockerfile
#remove all containers
docker rm $(docker ps -a -q)

#remove all images
docker rmi $(docker images -q)

#--------------------------------
#image
#run an image
docker run -p 8000:8000 8c3ff7015517 \
> python3 manage.py runserver 0.0.0.0:8000

#rebuild existing image using tag
docker build -t [app_name] .

#run an image
docker run -p 8000:8000 [app_name]
#--------------------------------
#containers, login to container
docker exec -t -i [66175bfd6ae6] bash

#--------------------------------
#Docker-compose

#build image and container
docker-compose up --build

#run container and detached
docker-compose up -d

#service
docker-compose [start,stop,restart] [service]

#--------------------------------
#database
#connecting to database container
docker-compose up -d database
docker-compose logs database
docker-compose run --rm database psql -U postgres -h database

#app.settings database config
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'django_core',
        'USER': 'postgres',
        'PASSWORD': 'postgres',
        'HOST': 'database',
        'PORT': 5432,
    }
}
#--------------------------------
#database log
docker-compose logs database

#--------------------------------
#web
#database web
docker-compose logs web

#--------------------------------
#virtualenv
source ~/venv3/bin/activate

#django version
python -m django --version
python manage.py startapp books
python manage.py runserver

#collectstatic
python manage.py collectstatic
#--------------------------------
#git
git push -u origin <name> --tags

Remove Tag
git tag -d <tag>

Remove Remote Tag
git push origin :refs/tags/<tag>

#--------------------------------
**console**
from django.conf import settings
>>> dir(settings)
>>> settings.name

#--------------------------------
**sample**
-- Create model Post
CREATE TABLE "blog_post" ("id" serial NOT NULL PRIMARY KEY, "title" varchar(250) NOT NULL, "slug" varchar(250) NOT NULL, "body" text NOT NULL, "publish" timestamp with time zone NOT NULL, "created" timestamp with time zone NOT NULL, "updated" timestamp with time zone NOTNULL, "status" varchar(10) NOT NULL, "author_id" integer NOT NULL);
ALTER TABLE "blog_post" ADD CONSTRAINT "blog_post_author_id_dd7a8485_fk_auth_user_id" FOREIGN KEY ("author_id") REFERENCES "auth_user" ("id") DEFERRABLE INITIALLY DEFERRED;
CREATE INDEX "blog_post_slug_b95473f2" ON "blog_post" ("slug");
CREATE INDEX "blog_post_slug_b95473f2_like" ON "blog_post" ("slug" varchar_pattern_ops);
CREATE INDEX "blog_post_author_id_dd7a8485" ON "blog_post" ("author_id");
COMMIT;

#--------------------------------
**shell console**
docker-compose exec python manage.py shell_plus --notebook