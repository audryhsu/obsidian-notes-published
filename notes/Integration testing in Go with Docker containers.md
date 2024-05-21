---
tags:
  - work
  - ookla
  - docker
  - testing
aliases: 
publish: true
---
# Background
- Growing popularity of microservices and usage of third party services has increased, which means a service can be made up of many components interacting with each other
	- Databases (mysql), messaging systems (kinesis), caches etc.
- Unit testing usually involves mocking out external services or side effects
	- 👍🏻  easier and faster to implement test
	- 👎🏼 mocks *simulate* expected behavior, not actual
	- 👎🏼 mocks are only as good as you make them and can be time consuming to write
- Integration testing can help increase developer confidence that interactions between services or external dependencies work as expected

# Problem 🙄
Integration tests require all components participating in the test to be configured and running in the test environment which can be cumbersome

Downsides to using a persistent, shared testing database:
- Shared instance can cause collisions🙄 if other tests are running at the same time.  
- Unexpected failed tests if test db is not "cleaned up" properly and has left over test artifacts
- Not scalable if we think beyond just mysql -- test dynamo? test kinesis?

## Why containers❓

> [!NOTE] Convenience and ease ✨
> Applications can be packaged with *all of its dependencies* into a *standardized unit* for software development.
> - Load applications without having to install and manage app + dependencies + configurations manually --> bundled in an image!
> - Docker Compose to start up containers as a group within own networked sandbox --> deploy group of apps a single unit

![team hour testing with containers 2023-04-06 08.16.35.excalidraw](../images/team%20hour%20testing%20with%20containers%202023-04-06%2008.16.35.svg)

## Potential Solutions 💡
###### Testcontainers
1. create and clean up container-based dependencies for automated integration/smoke tests

Pros:
- Spin up and down containers in your code gives you a lot of control, particularly around when individual containers are started
- Modules for MSQL, LocalStack

Cons:
- Originally written in Java, Go implementation is relatively newer (v0.19)
	- Chance that features available in Docker might not be available through the module 
- Ramp up time to learn a new library, no Ookla expertise available

Similar 👀: [dockertest](https://github.com/ory/dockertest)

###### Containerize app + dependent services to run together via **Docker Compose**
 Pro's:
 - Less complexity since dealing with less layers
 - Ookla seems more familiar with managing containers via Compose

Con's:
- Potentially harder to get other dev teams to adopt if they don't want to work directly with Docker and prefer code abstraction
- Still a personal learning curve

# Approach 🏃🏻‍♀️
Containerize app to be tests + mysql, run tests, and shut down containers

![Drawing 2023-04-06 08.26.12.excalidraw](../images/Drawing%202023-04-06%2008.26.12.svg)

Steps:
1. Create test-version Dockerfiles for DRAPI and databases
2. Configure and control containers using Docker Compose
	1. run on same virtual network to allow inter-container communication 
	2. define service dependency
3. Run make commands
4. Automatically clean up artifacts including ` test_db` after tests are completed

> [!Hint] Skip long running tests
>Mark integration tests with `t.Skip()` to [[Go Lang Testing#skip long-running tests|be skipped if `-short` flag used]]

# Challenges 😓
- running migrations *before* mysql container starts listening
- auth challenges fetching private github repositories
- coordinating timing of services
- docker, in general

# WIP/future work 🤔
1. Replace `test_db` build context to use *hosted* image instead of fetching from github and building each time
	1. Working with SRE to upload an image of the databases container to ECR
	2. Create a github action that is triggered by commits to `main` to rebuild image and push new image to ECR
2. Add to config ability to change db host
	1. flip between `localhost` and mysql container name 
3. Get feedback on how to improve DX for pattern adoption

![team hour testing with containers 2023-04-05 22.18.30.excalidraw](../images/team%20hour%20testing%20with%20containers%202023-04-05%2022.18.30.svg)