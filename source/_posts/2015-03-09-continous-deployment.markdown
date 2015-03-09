---
layout: post
title: "A Brief Overview of Continous Deployment"
date: 2015-01-13 23:10:23 +0530
comments: true
categories: 
---

Continous deployment is a design practise in software development which
significantly decreases the time of software delivery.It includes
continous integration, integration testing and then automated
deployments.It gives the developers a platform where they can push
patches and rapidly fix bugs as compared to manual deploys.

Let us discuss  a typical deployment pipeline with unit tests,releases,
integration tests and then automatic deployments.

<h6>
We will be using Jenkins,Github,Chef,AWS Ec2 instances,Rails App
</h6>


<img src="{{ root_url }}/images/pipeline.png" />

The pipeline is divided into 5 steps.


<h4>1)Specs </h4>
We need to run unit tests everytime a developer commits some changes.
Jenkins continously pools github for new commit and whenever a
developer commits on the master branch, the build is triggered.
If the specs pass it triggers the package build

<h4>2)Package </h4>
There are many ways of installing the code which is to deployed the
server.We can use git clone, we can use tar files and then download and
use them on the servers.We use rpm or deb packages to install the
release on the server machines.
</br>Some of the steps in this build are
</br>
i) Package all the necessary gems which are required for app to
run
</br>
ii) Run asset precompile
</br>
iii) Package the code and create rpm or deb package
</br>
iv) Upload the package to yum or debian repo and update it
</br>
The advantage of packaging(tar,rpm) above using git is that we dont have
to run bundle install and asset precompile on every server we have.
The advantage or rpm or deb package is that we can update or downgrade
version using simple yum and apt-get commands

<h4>3)Test_deploy</h4>
In this step we create a test cluster for integration specs on which the source code is installed and
service are run to emulate the production conditions.We can either use lxc or docker and deploy our code on the container,run integration specs and shut them down or we can  use normal instance to create the cluster. We havent used containers here because the developers wanted to login to test cluster. We use chef as deployment and configuration tool.

<h4>4)Integration specs</h4>
We need to run integration specs if we have more than one service which
are dependent upon each other.
After deploying the source code on test cluster we need to get the details
of the test cluster i.e which service is running on which ip.
We use knife search api for this.Using this we configure the source code and
then run integration specs.If the integration specs pass , it triggers staging deploy

<h4>5)Staging deploy</h4>
Being very cautious with production,our philosophy is to create staging as a exact replica of production
and test changes on staging cluster before deployment to production.
The services in staging are run in production mode and we are configured
chef.In this step the new release is deployed on staging where the team
can test the feature in prod like environment.
