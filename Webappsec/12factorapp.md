https://blog.rheinwerk-computing.com/twelve-factor-app-principles-for-developers 

# Twelve-Factor App Principles for Developers Rheinwerk Computing 

As web applications became popular in the past two decades, many best practices and principles have emerged to develop and maintain web applications, including the twelve-factor app set of principles for building performant, scalable, and resilient web applications.
As the name suggests, twelve-factor app principles provide 12 principles for creating modern, microservice-based cloud-native web applications. It was originally introduced by Adam Wiggins in the 2011. He also happens to be the cofounder of Heroku, which was once a very popular platform-as-a-service (PaaS) for running enterprise applications.
In this post, we’ll explore the 12 principles.


## Twelve-Factor App Principles for Developers


 

### 1 Codebase
The codebase principle states that the application resources should have a version-managed, source-code repository such as Git or Subversion. Multiple apps sharing the same code is a violation of the twelve-factor app principles. The solution here is to factor shared code into libraries that can be included through the dependency manager.
Per the twelve-factor app methodology, a deploy is an instance of an app in the developer’s system, development system, quality system, and production system, though different versions can be active in each deployment (see figure below). The codebase can be accessible to Continuous Integration/Continuous Delivery (CI/CD) as part of the software development cycle. A system’s codebase can be in sync with some level of automation using CI/CD.

Twelve-Factor App Principle: Codebase 

2 Dependencies
When you build an application, it’s common to use other reusable repositories and tools within the application that you create. One easy way to handle dependencies is to include those dependent library codes into your application itself. However, that comes with the disadvantage of mixing the lifecycle of the application code and the dependent library code.

 

This principle specifies that the dependencies should be set in your app manifest or configuration file and managed externally instead of including them directly in the source code.

 

If you’re using Node.js, you specify the dependencies inside a package.json file (see below). The node package manager (NPM) takes care of downloading the specified versions and installing them to make them available for the application to use. Similarly, in a Java application, you set the dependencies in a build.gradle file, and the Gradle dependency manager takes care of those dependencies.

 

Twelve-Factor App Principle: Node.js Dependencies

 

3 Config
This principle states that configurations of an application need to be stored independently from the code itself, as environment variables, or in a configuration file.

 

Examples of configuration data are hostname, port number, and credentials. These configuration data are different for each of the deployment environments where the application is going to run. By separating such configuration data, we’re making it easy to run the application in different environments by just applying the independently maintained configuration data to the runtime.

 

The next figure shows that when running in different landscapes, the same codebase is run, but different sets of configuration data are applied in different environments.

 

Same Application Code Running in Different Environments

 

4 Backing services
The backing service principle suggests that services such as databases, messaging systems, Simple Mail Transfer Protocol (SMTP) services, and so on, should be architected as external resources. The application consumes these backing services over the network.

 

These services can be locally managed or provided by third parties such as Amazon Simple Storage Service (Amazon S3) or Google Maps. URLs, credentials, and so on should be maintained in a configuration file, and when required to replace an existing service with another one, you can easily change the details in the configuration file. Each of these backing services is referred to as a resource. In the figure below, you can see an example of an SAP Cloud Application Programming Model application accessing three different resources.

 

Twelve-Factor App Principle: SAP Backing Services

 

5 Build, Release, and Run
This principle states that there should be three independent steps in your deployment process (see figure below):

The build stage converts the code repo into an executable bundle. At this stage, all the dependencies will also be fetched and compiled into the executable bundle.
The release stage combines the configurations from configuration files and environment variables with the executable bundle. The resulting build will be an executable file ready to be run in the execution environment.
The run stage runs the app in the execution environment, which can be development, quality, or production.
Twelve-Factor App Principle: Build, Release, Run

 

6 Stateless Processes
This principle states that applications should have the provision to be served by multiple stateless, independent processes, as shown in this figure.

 

Stateless Processes Serving the Application

 

Consider that a user’s request is served by process 1. Subsequent requests should be able to be served by process 2 or any other processes as well. There will be no session data maintained in any of the processes, and each process independently serves the request without communicating with other processes. Any data that needs to persist must use a backing service such as a database.

 

Following this principle makes it easy to scale the infrastructure up and down, thus making it ideal for cloud deployments.

 

7 Port Binding
The twelve-factor app is a self-contained standalone app that doesn’t require a web server to create a web-facing service. Instead of having a web server to handle the requests and sending to the individual services, where dependency with the web server is created, a twelve-factor app directly binds to a port and responds to incoming requests (see below).

 

Port Binding

An individual service can act as a backing service to another app by providing the backing service URL in the configuration of the consumption app.

 

8 Concurrency
This principle states that the application needs to be broken down into multiple modules so that each of those modules can be scaled up and down independently. For instance, HTTP requests can be handled by a web process, and a worker process can take care of background jobs. Again, these individual processes can be scaled up or down to handle the increased workloads independently.

 

9 Disposability
A twelve-factor app should maximize robustness with fast startup and graceful shutdown. The processes should minimize the time to startup and ideally take a few seconds to start and receive the incoming requests; it helps while scaling up the processes. At the same time, the processes should shut down gracefully and cease to listen on the service port without allowing any incoming requests; in such cases, if there is a queuing system, the requests can be queued and processed once the processes are up.

 

10 DEV/PROD Parity
The twelve-factor app methodology suggests that an app’s development, staging, and production are kept as similar as possible. A twelve-factor app should be designed with the CI/CD approach by making the time gap small—where the developer writes some code and deploys it in hours or even minutes—and keeping DEV and PROD as similar as possible. This eliminates the risk of bugs in production when new changes are moved with different versions.

 

11 Logs
This rule suggests treating logs as event streams. Logs are typically time-ordered event information, or logs can be error or success messages recorded by an app. A twelve-factor app never concerns itself with storing the log information in the app, as it can die and, as a result, lose the information. Instead, the app should treat log entries as event streams and use a separate service to save them. These can be consumed by interested parties to perform analytics or for monitoring.

 

12 Admin Processes
The developer often needs to perform administrative or maintenance activities for apps that need data migration, running processes, or one-time scripts. These should also be identical across different landscapes (DEV, QA, and PROD). These processes should also be shipped along with the application code to avoid synchronization issues.

 

Editor’s note: This post has been adapted from a section of the book SAP Extension Suite Certification Guide: Development Associate Exam by Krishna Kishor Kammaje and Mahesh Kumar Palavalli.
