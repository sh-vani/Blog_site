This repository contains a sample implementation of a blog application, designed to show off various features of OpenShift. The blog application is implemented using Python and Django.

In the default deployment configuration, the blog application uses a SQLite database within the container. When using the SQLite database, it will be pre-populated each time the application starts with a set of blog posts. An initial account will also be created for logging into the application to add more posts. The user name for this account is developer and the password is developer.

Because the SQLite database is stored in the container, new posts and any uploaded images, will be lost when the container restarts. A PostgreSQL database can be attached to the application to add persistence for posts and demonstrate the use of a database. A separate persistent volume can also be attached to the blog application to provide persistent storage for uploaded images. In the case of PostgreSQL being used, a manual step is required to setup the database the first time.

The appearance of the blog application can also be adjusted using a set of environment variables to make it easier to demonstrate blue/green or a/b deployments, split traffic etc.

Deploying from an image
An image is automatically built from this repository when code changes are made using the Docker Hub automated build mechanism.

To deploy the sample application from the command line, you can run:

oc new-app openshiftkatacoda/blog-django-py --name blog-from-image
oc expose svc/blog-from-image
Building from source code
A source build and deployment can be run direct from this repository.

To build and deploy the sample application from the command line, you can run:

oc new-app python:latest~https://github.com/openshift-katacoda/blog-django-py --name blog-from-source-py
oc expose svc/blog-from-source-py
Note that you need to provide the S2I builder name of python:latest if you are not explicitly telling oc new-app that the source build strategy should be used. This is because the repository also contains a Dockerfile and automatic detection performed by oc new-app would give precedence to the docker build strategy.

To build and deploy the sample application from the command line, but at least have automatic source language detection occur, you can run:

oc new-app --strategy=source https://github.com/openshift-katacoda/blog-django-py --name blog-from-source-auto
oc expose svc/blog-from-source-auto
Building from Dockerfile
A docker build and deployment can be run direct from this repository.

To build and deploy the sample application from the command line, you can run:

oc new-app https://github.com/openshift-katacoda/blog-django-py --name blog-from-docker
oc expose svc/blog-from-docker
This relies on oc new-app doing auto detection and finding that a Dockerfile exists. If want to be specific, you can use the --strategy=docker option to be sure.

Adding a PostgreSQL database
A PostgreSQL database can be used to add persistence for blog posts.

To deploy a PostgreSQL database from the command line, you can run:

oc new-app postgresql-persistent --name blog-database --param DATABASE_SERVICE_NAME=blog-database --param POSTGRESQL_USER=sampledb --param POSTGRESQL_PASSWORD=sampledb --param POSTGRESQL_DATABASE=sampledb
To re-configure the blog application to use the database, you need to set the DATABASE_URL environment variable for the blog application.

oc set env dc/blog-from-source-py DATABASE_URL=postgresql://sampledb:sampledb@blog-database:5432/sampledb
As a separate service is used for the database, it is necessary to manually setup the database the first time. This requires logging into the pod and running a setup script.

To run the setup script from the command line, you can run:

 -----> Running Django database table migrations.
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  No migrations to apply.
  Your models have changes that are not yet reflected in a migration, and so won't be applied.
  Run 'manage.py makemigrations' to make new migrations, and then re-run 'manage.py migrate' to apply them.
 -----> Running Django super user creation
Username: developer
Email address: mail@example.com
Password:
Password (again):
Superuser created successfully.
 -----> Pre-loading Django database with blog posts.
Installed 2 object(s) from 1 fixture(s)
 this blog app infomation about different blog
 
