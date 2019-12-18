# Lab 04 - Docker

# Authors : Jeremy Zerbib, Adrien Barth

## Link forked from [here](https://github.com/SoftEng-HEIGVD/Teaching-HEIGVD-AIT-2019-Labo-Docker) and the version is `28e97b713e2d3803acee5b447dd67596624e11b0`

## Introduction

As an introduction, we can say that the goals of this lab are to build our own `Docker` images, become familiar with process supervision for `Docker`, understand core concepts for dynamic scaling of an application in production and put into practice decentralised management of web server instances. This lab is built upon the previous lab we did on the load-balancing tool `HAProxy`.

In order to attain all the objectives of the lab, we will have to perform 7 tasks, including an introductory task to install and get familiar with the tools  

## Task 0 : Identify issues and install the tools

### Questions

#### **[M1]** Do you think we can use the current solution for a production environment? What are the main problems when deploying it in a production environment?

This solution is not usable in a production environment because it can cause some loss of session depending on the cookie used. 

Based on our configuration of the previous lab, we used `SERVERID` cookies so those unpredictable crashes could be a cause of loss of sessions. While the server is online, the user can navigate on the website and keep his session. But if the connection is lost and we reload the container, the `id` might not be the same and therefore the session is lost. 

Another issue is the latency between the human reaction and the server being down. The fact that the administrator has to perform a check on the servers to kill one and reload it has to create some latency. Therefore, a user could lose his connection 

#### **[M2]** Describe what you need to do to add new `webapp` container to the infrastructure. Give the exact steps of what you have to do without modifiying the way the things are done. Hint: You probably have to modify some configuration and script files in a Docker image.

#### **[M3]** Based on your previous answers, you have detected some issues in the current solution. Now propose a better approach at a high level.

#### **[M4]** You probably noticed that the list of web application nodes is hardcoded in the load balancer configuration. How can we manage the web app nodes in a more dynamic fashion?

#### **[M5]** In the physical or virtual machines of a typical infrastructure we tend to have not only one main process (like the web server or the load balancer) running, but a few additional processes on the side to perform management tasks.

#### For example to monitor the distributed system as a whole it is common to collect in one centralised place all the logs produced by the different machines. Therefore we need a process running on each machine that will forward the logs to the central place. (We could also imagine a central tool that reaches out to each machine to gather the logs. That's a push vs. pull problem.) It is quite common to see a push mechanism used for this kind of task. 

#### Do you think our current solution is able to run additional management processes beside the main web server / load balancer process in a container? If no, what is missing / required to reach the goal? If yes, how to proceed to run for example a log forwarding process?

#### **[M6]** In our current solution, although the load balancer configuration is changing dynamically, it doesn't follow dynamically the configuration of our distributed system when web servers are added or removed. If we take a closer look at the `run.sh` script, we see two calls to `sed` which will replace two lines in the `haproxy.cfg` configuration file just before we start `haproxy`. You clearly see that the configuration file has two lines and the script will replace these two lines.

#### What happens if we add more web server nodes? Do you think it is really dynamic? It's far away from being a dynamic configuration. Can you propose a solution to solve this?

### Deliverable

#### Take a screenshot of the stats page of HAProxy at http://192.168.42.42:1936. You should see your backend nodes.

![stats](./assets/Task0_stats.png)

#### Give the URL of your repository URL in the lab report.

The URL of our repository is this [one](https://github.com/jerozerbib/Teaching-HEIGVD-AIT-2019-Labo-Docker). 

## Task 1 :  Add a process supervisor to run several processes

