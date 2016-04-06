# restapi-ddt-accel
An open source data driven accelerator for testing RESTful APIs.

## High Level Requirement
I would like to regression test a RESTful API with the click of a button.

## Low Level Requirements
I would like...

* **Integration Testing.**  To verify that APIs are returning certain results.
* **Load Testing Statistics.** To calculate API response time.
* **A Data Driven Approach.** To add test executions without touching code.
* **Complex Assertions.** To verify http status codes and response data.
* **Automation.**  To execute tests within a build server.
* **Adhoc Execution.**  To run tests at any time.
* **Multi Environment Support.**  To run tests against any environment.
* **API Chaining.**  To use response data from some APIs as request data for other APIs.

## Technologies
* [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer) - A precondition, it's the actual API implementation we're looking to test.
* [SOAPUI](https://www.soapui.org/) - An open source web service testing application.  It's our primary design-time environment.
* [Maven](https://maven.apache.org/) - A command line build automation tool.  Our automated runtime environment.
* [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) - Comma Separated Value File Format.  The format of our input file.
* [RAML](http://raml.org/) - RESTful API Modeling Language.  A specification for describing RESTful APIs.  Our mechanism for "importing" the API into SOAPUI.
* [Swagger](http://swagger.io/) - A specification for describing RESTful APIs.  Another mechanism for importing API into SOAPUI.  Not used in this accelerator.
* [Groovy](http://www.groovy-lang.org/) - An object-oriented scripting language.  Used to manipulate data and perform assertions within SOAPUI.

## Versions
This accelerator was built and verified with the following technologies.

| Technology          | Version   | 
| --------            | --------  | 
| SOAPUI              | 5.2.1     | 
| SOAPUI RAML Plugin  | 0.4       | 
| Maven               | 3.2.1     |
| RAML                | 0.8       |
  
## Design
The following steps were taken to develop this accelerator.

1. **Install SOAPUI and Maven.**

2. **Import API specification into SOAPUI via RAML.**  
For this example we used GitHub API v3.

3. **Design Tests in SOAPUI.**  

  Generally speaking, the following design decisions were made:  

   * **Project Level** - Define the enviroment via variable "ENV".  Used to drive service endpoint and loading of appropriate data file.  
   
   * **Test Suite Level** - Nothing of note.  
   
   * **Test Case Level** - Define API request/response related properties.  For example, "USERID", "USERNAME" and "REPOID".  
   
   * **Test Step Level** - Model API requests based on requirements.  All API calls are orchestrated via a groovy script.  

  Specifically for this accelerator, the following API calls logic were modeled.  For each row in the datafile...  
  
   * **GET /users/{userId} (200)** - Get the user based on "USERID" (column #1 in datafile).
      * Verify HTTP Status Code = 200
      * Assert name in JSON response = "USERNAME" (column #2 in datafile).
       
   * **GET /users/{userId}/repos (200)** - Get list of repos owned by the user.
      * Verify HTTP Status Code = 200
       
   * **GET /repos/{ownerId}/{repoId} (200)** - Get each individual repo owned by the user.
      * Verify HTTP Status Code = 200

4. **Configure Maven commandline interface.**  
See the pom.xml for additional details.

## Running
From the commandline, run **"mvn test"**.  This will run the default "prod" maven profile. 

You should see the following output:

>    
> SoapUI 5.2.1 TestCaseRunner Summary  
> .---------------------------------  
> Time Taken: 2912ms  
> Total TestSuites: 1  
> Total TestCases: 1 (0 failed)  
> Total TestSteps: 9  
> Total Request Assertions: 0  
> Total Failed Assertions: 0  
> Total Exported Results: 0  
> [INFO] ------------------------------------------------------------------------  
> [INFO] BUILD SUCCESS  
> [INFO] ------------------------------------------------------------------------  
> [INFO] Total time: 9.798 s  
> [INFO] Finished at: 2016-04-05T19:26:10-05:00  
> [INFO] Final Memory: 28M/258M  
> [INFO] ------------------------------------------------------------------------  

## FAQ

**Q:** The github api is failing and returning 403.  What's the problem?  
**A:** The github api is [rate limited](https://developer.github.com/v3/#rate-limiting) and this might be your problem.  Wait some time or change your IP address.

**Q:** How do I run the "dev" maven profile?  
**A:**  Run "mvn test -P dev".

**Q:** When I run the "dev" maven profile I get 404 errors.  What's the problem?  
**A:** This profile hits a bogus service endpoint (https://dev-api.github.com/).  This profile exists only to demo multi environment functionality.

## Next Steps
The intent of this accelerator is to be a starting point.  It can be demod as-is, extended or parted out for pieces.  

Ideally it's included within the [build lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) of your project for integration testing.  

The world is your oyster.  
