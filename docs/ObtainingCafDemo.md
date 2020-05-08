---
id: ObtainingCafDemo
title: Obtaining Cafienne Demo
sidebar_label: Obtaining Cafienne Demo
---

Here we explain how you can obtain the binaries for Cafienne to get started. There is currently one way: clone the github repository from https://github.com/cafienne/getting-started. For that you have to ask us authorization. Contact us using the form on [cafienne.io](https://cafienne.io). Note that Cafienne runs in dockers, so Docker has to be installed too. You need at least version Docker 17.03-ce / Docker for Mac/Win 17.03-ce +.

## Clone the repository and get started
When authorized, you can clone the repository from Github by running the following command in a terminal: `git clone https://github.com/cafienne/getting-started.git`. Note that the clone is in an folder cafienne-demo on your Home directory. 

Now, you can pull the latest Cafienne docker images by running `docker-compose pull` and bringing it up by running  `docker-compose up`. You can now start working with Cafienne.

There are  two methods to stop and/or remove the environment.  

* `docker-compose down`   will stop the running environment and remove all created containers. Only use this method if you want to rebuild all containers and start with a clean environment, because everything is completely removed.

* `docker-compose stop`   only stops the running containers. All data is preserved. Use this method if you want to preserve all your running cases.Once stopped, you can start the containers by running docker-compose -f cafienne-demo.yml start 

## Exposed URLs of the Cafienne Demo environment
After starting up the Cafienne Demo environment, the following URL allow you to access the various parts of the environment:

* Cafienne IDE: http://localhost:2081
* Cafienne task user interface: http://localhost:8081
* Cafienne API (exposed through Swagger): http://localhost:2027
* MailCatcher web UI: http://localhost:1080

You can logon to these parts by using `admin` for the username and `cafienne` for the password. Next to the admin there are four predefined users: Lara `lara@example.com/lara`, Hank `hank@example.com/hank`, Gerald `gerald@example.com/gerald` and Suzy `suzy@example.com/suzy`.
