---
title: 'flask webserver development'
date: 2018-12-28
permalink: /posts/2018/12/2018-12-28-flask-webserver/
tags:
  - python
  - flask
---

This post is based on the 
- [Python Flask Tutorial](#1)
- [Corey Schafer's Github Repo](#2)

Hints for setting up Flask web server:

1. Setup local develop environment. Set DEBUG mode of flask to be on to enable realtime update.
   - Ubuntu
   - Python venv
   - Flask 
   - MySql
2. CSS, JS code (Check Bootstrap)
3. upload it to the server
   1. create virtual environment for python
   3. download the requirement packages for python (pip freeze > requirements.txt; pip install -r requirement.txt)
   4. start the local test environment, make sure every functionality is fine.
   5. download nginx to enable the web service and gunicorn to enable python code for job processing, config them correctly
   6. setup the ufw (firewall) to disable other ports connection (like test port)

(Server operation: login with admin and create a user with normal permission. Disable the ssh with rootlogin then login with the user account.)

[1]: https://www.youtube.com/watch?v=goToXTC96Co&list=PL-osiE80TeTs4UjLw5MM6OjgkjFeUxCYH&index=13
[2]: https://github.com/CoreyMSchafer/code_snippets/tree/master/Python/Flask_Blog