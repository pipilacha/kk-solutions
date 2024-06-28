### Task
A python app needed to be Dockerized, and then it needs to be deployed on App Server 1. We have already copied a requirements.txt file (having the app dependencies) under /python_app/src/ directory on App Server 1. Further complete this task as per details mentioned below:


Create a Dockerfile under /python_app directory:
    Use any python image as the base image.
    Install the dependencies using requirements.txt file.
    Expose the port 5000.
    Run the server.py script using CMD.

Build an image named nautilus/python-app using this Dockerfile.

Once image is built, create a container named pythonapp_nautilus:
    Map port 5000 of the container to the host port 8098.

Once deployed, you can test the app using curl command on App Server 1.

curl http://localhost:8098/
### Solution
```
[tony@stapp01 ~]$ cd /python_app/src/
[tony@stapp01 src]$ ls -la
total 16
drwxr-xr-x 2 root root 4096 Jun 10 02:22 .
drwxr-xr-x 3 root root 4096 Jun 10 02:22 ..
-rw-r--r-- 1 root root    5 Jun 10 02:22 requirements.txt
-rw-r--r-- 1 root root  278 Jun 10 02:22 server.py

[tony@stapp01 src]$ cat requirements.txt 
flask
[tony@stapp01 src]$ cat server.py 
from flask import Flask

# the all-important app variable:
app = Flask(__name__)

@app.route("/")
def hello():
    return "Welcome to xFusionCorp Industries!"

if __name__ == "__main__":
        app.config['TEMPLATES_AUTO_RELOAD'] = True
        app.run(host='0.0.0.0', debug=True, port=5000)

[tony@stapp01 src]$ cd ..
[tony@stapp01 python_app]$ sudo vi Dockerfile
# Dockerfile
FROM python:slim

WORKDIR /app

ADD ./src .

RUN pip install -r requirements.txt

EXPOSE 5000 

CMD flask --app server run --host=0.0.0.0

[tony@stapp01 python_app]$ docker build -t nautilus/python-app .
Sending build context to Docker daemon  4.608kB
Step 1/6 : FROM python:slim
slim: Pulling from library/python
09f376ebb190: Pull complete 
578d3daee00a: Pull complete 
2614b8b77544: Pull complete 
0d69f3ec269e: Pull complete 
c9e6b31e1b2e: Pull complete 
Digest: sha256:e3ae8cf03c4f0abbfef13a8147478a7cd92798a94fa729a36a185d9106cbae32
Status: Downloaded newer image for python:slim
 ---> faf6686d57dd
Step 2/6 : WORKDIR /app
 ---> Running in 74bfc9968204
Removing intermediate container 74bfc9968204
 ---> e8dbc025995f
Step 3/6 : ADD ./src .
 ---> 1c29810e83a4
Step 4/6 : RUN pip install -r requirements.txt
 ---> Running in 59271bebd1a9
Collecting flask (from -r requirements.txt (line 1))
  Downloading flask-3.0.3-py3-none-any.whl.metadata (3.2 kB)
Collecting Werkzeug>=3.0.0 (from flask->-r requirements.txt (line 1))
  Downloading werkzeug-3.0.3-py3-none-any.whl.metadata (3.7 kB)
Collecting Jinja2>=3.1.2 (from flask->-r requirements.txt (line 1))
  Downloading jinja2-3.1.4-py3-none-any.whl.metadata (2.6 kB)
Collecting itsdangerous>=2.1.2 (from flask->-r requirements.txt (line 1))
  Downloading itsdangerous-2.2.0-py3-none-any.whl.metadata (1.9 kB)
Collecting click>=8.1.3 (from flask->-r requirements.txt (line 1))
  Downloading click-8.1.7-py3-none-any.whl.metadata (3.0 kB)
Collecting blinker>=1.6.2 (from flask->-r requirements.txt (line 1))
  Downloading blinker-1.8.2-py3-none-any.whl.metadata (1.6 kB)
Collecting MarkupSafe>=2.0 (from Jinja2>=3.1.2->flask->-r requirements.txt (line 1))
  Downloading MarkupSafe-2.1.5-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (3.0 kB)
Downloading flask-3.0.3-py3-none-any.whl (101 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 101.7/101.7 kB 1.5 MB/s eta 0:00:00
Downloading blinker-1.8.2-py3-none-any.whl (9.5 kB)
Downloading click-8.1.7-py3-none-any.whl (97 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 97.9/97.9 kB 5.2 MB/s eta 0:00:00
Downloading itsdangerous-2.2.0-py3-none-any.whl (16 kB)
Downloading jinja2-3.1.4-py3-none-any.whl (133 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.3/133.3 kB 7.7 MB/s eta 0:00:00
Downloading werkzeug-3.0.3-py3-none-any.whl (227 kB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 227.3/227.3 kB 11.4 MB/s eta 0:00:00
Downloading MarkupSafe-2.1.5-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (28 kB)
Installing collected packages: MarkupSafe, itsdangerous, click, blinker, Werkzeug, Jinja2, flask
Successfully installed Jinja2-3.1.4 MarkupSafe-2.1.5 Werkzeug-3.0.3 blinker-1.8.2 click-8.1.7 flask-3.0.3 itsdangerous-2.2.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
Removing intermediate container 59271bebd1a9
 ---> ffec388a0326
Step 5/6 : EXPOSE 5000
 ---> Running in f69ad8c41c53
Removing intermediate container f69ad8c41c53
 ---> ed62dbddc6bf
Step 6/6 : CMD flask --app server run --host=0.0.0.0
 ---> Running in 6e84bfb58a40
Removing intermediate container 6e84bfb58a40
 ---> cc9d59f4978e
Successfully built cc9d59f4978e
Successfully tagged nautilus/python-app:latest

[tony@stapp01 python_app]$ docker run --name pythonapp_nautilus -d --rm -p 8098:5000 nautilus/python-app
b081bab2b71e05fc6eb4c75171ea4cbbe5eb70dcfd455167bc57e3320783cc5b

[tony@stapp01 python_app]$ docker ps
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
b081bab2b71e        nautilus/python-app   "/bin/sh -c 'flask -…"   6 seconds ago       Up 3 seconds        0.0.0.0:8098->5000/tcp   pythonapp_nautilus

[tony@stapp01 python_app]$ curl localhost:8098
Welcome to xFusionCorp Industries!

```
