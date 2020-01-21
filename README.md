# BP Code Assessment
Having fun deploying serverless web apps using Azure

### Basic Python App
My app accepts a query string in the url, specifically a name. The app then returns a message including the name and a status code of 200. If no query string is included, the app returns a message as well as a 400 status code. 

*You can hit the QA Serverless func here:* https://bpapp-qa.azurewebsites.net/api/HttpExample

*You can hit the Prod Serverless func here:* https://bpapp.azurewebsites.net/api/HttpExample

### My Path to Muli-stage
I first created a Azure function through VScode. I had to install extensions in VScode for Azure functions, update python to 3.7 and install pylint. I had to register the func in the Azure services, under function apps. I then had to create a project in the Azure devops platform. Once I had a project, I could create a connection between my github repository that housed my source code and my project. My Azure devops project would now know everytime a new commit had been merged to master.

Now that my repository was configured with the project, I could create service connections, that would allow a commit to master to trigger the build pipeline to begin. I had to create a service connection for both my production and QA Azure functions. 

My functions are registered, my devops project is watching my github repo and my service connections are in place. I can start to build my yaml file. I start with one stage, that installs dependencies, builds the project and deploys the build artifact to my production function. I then add the build testing, specifically the trufflehog scanning that was a requirment on this exercise. I added a false aws token to my .yaml file and built the project again. The following image shows that the test found a string displaying high entropy over 20 characters and terminated the build.

![image of truffleHog test]
(https://imgur.com/0T0r3Ib)

I then seperated my build and release into stages. I started with just the production function and then added the QA function stage. My biggest struggle here was how to pass the build artifact from the build stage to the deploy stages, I found the pre-defined tasks publishPipelineArtifacts and DownloadPipelineArtifacts very useful. I added a testing stage between my QA and production, to make sure that the endpoint was behaving properly before releasing the build to production. 

### The Prompt vs Reality
I want do a quick runthrough of the prompt I was given and how my project has addressed each item:

* Create a simple 'Hello World' Python app (can be Dockerised) that will be run as an Azure Function
  - I was able to create a simple python app and run it as a Azure func. In fact two Azure functions

* During the build phase run truffleHog on the remote repo
  - I was able to scan my remote repo with truffleHog and show that when a secret was found, the build was terminated.
  
* Demonstrate both pass and fail scenarios in the build history if possible
  - I left my Azure DevOps pipeline as public, although I am not sure if you will actually be able to go and check it out. Here is the linke the the project: https://dev.azure.com/davidgilmoreinnis/BP_ASSESSMENT_PIPELINE
  - On the chance that you cannot look at the project yourself, I am providing screen shots of a pipeline successfully built and one that fails because the Test stage failed.
  
  ![image of successful build]
  (https://imgur.com/kivTvPW)
  ###### Successful build
  
  ![image of failed build]
  (https://imgur.com/YGJdFZz)
  ###### Failed Build
  
 * Create a release pipeline that deploys a multi-stage (multi-environment) pipeline with stages that reflect a realistic route-to-live.
   - At this stage, I began to have a lot of questions that were getting harder to find the answers. I seperated my pipeline into stages, I create seperate enviroments for testing and production, and I did my best to include helpful tests. I really found a wide variety of answers and opinions on the how a pipeline should be configured and I have to believe that there are a ton of other factors that help mold a solution. 
 
* Create a pipeline that deploys your Python app as an Azure Function
  - I have!
* The pipeline should aim to demonstrate  relevant examples in SOME, or all, of the following areas:
  1. Use of Pre- and post-deployment conditons
     - I was unclear on exactly what was being asked here. As far as I can tell, Azure supports pre and post conditions in the form of gates, which can only be configured through the devops UI. Since the prompt was asking for .yaml code, I did not implement this item.
   2. Use of application testing steps
      - I created a Test Stage to hit the QA endpoints before deployment to production. I ran truffleHog during the build and I linted my python code from VScode before commits. There was a lot of testing tools and approaches that I read about and wanted to get a chance to implement. I ran out of time, but I would have continued by writing specific unit tests for my application, run code coverage tests and publishing the results with the build artifacts.

* Your deployed Azure function should result in a response from a testable endpoint
   - It does!

* Your build and release pipelines should be saved as YAML code in your repo for ease of review
   - The build pipeline is saved in the file azure-pipelines.yml in this repo. 

## Finally, I want to thank you for such an intersting challenge. I really learned a ton and enjoyed working with Azure functions and pipelines!
