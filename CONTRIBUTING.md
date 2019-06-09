# Contributing to Clipper
:tada: Welcome! Clipper is under active development now, meaning we are looking for passionate contributors!
We know you are busy and your time is very precious. Thank you for considering to contribute to the project!:+1:

This documentation provides guidelines and resources that will help you to 
get onto the project as soon as possible. You don't need to read all of these or follow all the guidelines. 

Some of parts might not include any content yet since this doc is just started to be written. 
Feel free to submit a PR if you want to add new contents! 

### Table of Contents
* [Quick Start](#quick-start)
* [Help](#help)
* [Guideline](#guideline)
    * [Feature Request](#feature-request)
    * [Pull Request](#pull-request)
* [I am a beginner](#how-to-start-contribution)
* [Style Guides](#style-guide)
    * [C++](#cpp)
    * [Python](#python)
* [Development](#development)
    * [CI](#CI)
    * [Client Library Develpment (clipper-admin)](#client-library)
        * [Debug](#debug)
        * [Logging](#logging)
    * [Core Development](#core-development)
* [Want to learn Clipper](#want-to-learn-clipper)
    * [Concepts](#concepts)
    * [Components](#directories)
    * [Resources](#resources)

## Quick Start

### I want to learn Clipper. 
Since it is the contribution doc, we will assume you know some basics about Clipper. If you need more context, 
- Check out our website, http://clipper.ai/ 
- Check out basic examples, https://github.com/ucbrise/clipper/tree/develop/examples/basic_query.
- Various examples about how to use Clipper will be in this directory. https://github.com/ucbrise/clipper/tree/develop/examples.

### I want to start contributing right now.
Clipper consists of 
- Client library, clipper_admin (https://github.com/ucbrise/clipper/tree/develop/clipper_admin)
- Core stacks
    - Query Frontend (https://github.com/ucbrise/clipper/tree/develop/src)
    - Management Frontend (https://github.com/ucbrise/clipper/tree/develop/src)
    - Frontend Exporter (https://github.com/ucbrise/clipper/tree/develop/monitoring)
- Model Containers (https://github.com/ucbrise/clipper/tree/develop/containers)
- CI (https://github.com/ucbrise/clipper/tree/develop/bin)
- Benchmarking (https://github.com/ucbrise/clipper/tree/develop/bench)
- Dockerfiles for CI/core-stacks/model-containers (https://github.com/ucbrise/clipper/tree/develop/dockerfiles)
- Examples (https://github.com/ucbrise/clipper/tree/develop/examples)

If you'd like to learn more details about each component, check 
Any sort of contribution to any of them will be very helpful for Clipper. Please feel free to request a join to our slack through [link]

Also, here are our goals for future releases.
- Helps users to integrate our system to other ml pipeline tools
- Improves performances and scalability
- Provides better user experience with well-defined client library
- Provides robust selection policies 
- Stabilize new releases by fixing bugs and resolving issues

## Help
If you have any questions or need help for your contribution. Please send an email: clipper-dev@gmail.com

## Guideline
### Feature Request
If you have any feature request or you want to develop new features and discuss if it is a good idea, please create an issue first. 
Let us know what you want/what you will be working on. We recommend you to prefix `Suggestion` in your title like this link. https://github.com/ucbrise/clipper/issues/708

### Pull Request
We really appreciate all the pull requests! But sometimes, unorganized and less descriptive pull requests discourage us to review and merge them.
To avoid your hard work will be thrown away, I recommend you to do these 3 things.

1. Let us know you will be working on the issue and will create a PR! 
2. If you are planning to have a huge PR, please create an issue ahead of time with 
    - Details about the feature  
    - Design doc
    - PR plan
3. Plesae create a detailed PR description which includes lists below.
    - If it is a bug fix
        - Related issue
        - Problem statement
        - Summary
        - Note (things to note reviewers)
        - Ideally, how you verified the bug is fixed.

    - If it is a new feature
        - Related issue (Ideally, it should include design doc and details about new features)
        - Summary
        - How to QA

## I am a beginner
For those of you who have no prior experience to work on open source programs, no worries! 
Everyone once had their first contribution to their first open source program! We recommend you to start by
- Understanding the project. Read all the documentation that explains internal details. Try using Clipper and read examples. 
- Creating a PR that changes the contents in the examples or documentation.  
- Reading issues and see how it is resolved! You can also try to answer issues.
- Finding tasks from our backlog and tell us that you want to try working on them. In the main menu, there is a `project` tab and you can find all the backlogs.

## Style Guides
### C++
### Python
Not yet written.

## Development
Not yet written

## Want to learn Clipper
Details about Clipper will be found in this section. [Want to learn Clipper](#want-to-learn-clipper)
Here, we will briefly explain each directory/component. 

### Concepts
Clipper is a model serving system. Our primary goal is to provide a well-constructed system that helps 
data scientists, ml-engieers, and software-engineers to easily deploy and serve their models with low-latency and high throughput as possible. 

Here are list of features that Clipper provides.
- Simplifies deployment of models. Using our client library, you can deploy your model by a couple lines of code.
- Version control, rollback, and model selection policy.
- Provides a REST API that you can easily get the result of your model inference.
- Easy integration with Kubernetes.
- Caching and batching requests.

### Components
#### Client library, clipper_admin 
[clipper_admin_directory](https://github.com/ucbrise/clipper/tree/develop/clipper_admin)
Clipper_admin is a client library that communicates with `management frontend`. 
It provides `deployers` which help users to build a container that contain models and functions for ml inference.
Since Clipper is a distributed system that uses docker containers, it requires container coordination tools, such as Kubernetes. Currently, 
we support native Docker and Kubernetes. (Our native Docker is for the local environment and is not really coordinating anything. It is not fault-tolerant).

#### Core stacks
Clipper has microservices architecture. It has system components that communicate each other through RPC services.
RPC Messages are exchanged through ZeroMQ.

##### RPC service 
Clipper implements its own RPC to reduce network latencies. 
- code https://github.com/ucbrise/clipper/blob/develop/src/libclipper/src/rpc_service.cpp
- related issue: https://github.com/ucbrise/clipper/issues/238  

##### Query Frontend (https://github.com/ucbrise/clipper/tree/develop/src)
Query Frontend is the place that performs actual quries. It is connected to model servers (model containers) through long-lived TCP connections. 
To simplify development, it is designed to be stateless. All the states regarding Query Frontend will be stored in Redis. 

To achieve low latency and high througput, it batches requests. QueryFrontend uses adaptive batching scheme to dynamically decide the maximum batch size. 
Query Frontend can also cache the result of inference in its memory. Users can communicate with Query Frontend through REST APIs. 

##### Management Frontend (https://github.com/ucbrise/clipper/tree/develop/src)
Management Frontend is the component that `clipper_admin` communicates to. It is in charge of managing and deploying models. 

##### Redis
Not yet written.

##### Promehteus
Clipper collects metrics of Query Frontend for users using built-in prometheus.  
 
##### Frontend Exporter (https://github.com/ucbrise/clipper/tree/develop/monitoring)
It is tied with Query Frontend and exporting query frontend metrics to prometheus.

#### Model Containers (https://github.com/ucbrise/clipper/tree/develop/containers)
This directory contains model containers. Model containers are a lightweight server that processes the incoming RPC messages from Query Frontend. 
- RPC code https://github.com/ucbrise/clipper/blob/develop/containers/python/rpc.py

When models are deployed with `deployers`, functions and models are serialized and stored in docker's filesystem. 
When the RPC server boots up, it should know how to process requests and how to find and deserialize models and functions. 
To achieve this, each ml model should have its own container that extends `ModelContainerBase`. 
- Pyspark example: https://github.com/ucbrise/clipper/blob/develop/containers/python/pyspark_container.py

#### CI (https://github.com/ucbrise/clipper/tree/develop/bin)
Clipper started as a research project from Riselab at UC Berkeley. It uses Riselab's Jenkins for CI. 
The `bin` folder contains scripts for this CI process. Details will be in README.md of `bin` directory.
  
#### Benchmarking (https://github.com/ucbrise/clipper/tree/develop/bench)
Not yet written

#### Dockerfiles for CI/core-stacks/model-containers (https://github.com/ucbrise/clipper/tree/develop/dockerfiles)
Clipper heavily uses containers. All the necessary containers are included in this folder. It contains
- Dockerfile of core components such as query frontend, management frontend, and rpc service.
- Dockerfile for model containers.
- Dockerfile for CI such as `ClipperPy35TestsDockerfile` or `ClipperPy35DevDockerfile`. 
 
#### Examples (https://github.com/ucbrise/clipper/tree/develop/examples)
It contains all the example code. 

### Resources
- Clipper website: http://clipper.ai/
- Clipper task board: https://github.com/ucbrise/clipper/projects/4
- Clipper research paper: https://www.usenix.org/conference/nsdi17/technical-sessions/presentation/crankshaw
- Clipper container orchestration: http://clipper.ai/tutorials/container_managers/
- Clipper concepts: http://clipper.ai/tutorials/basic_concepts/
