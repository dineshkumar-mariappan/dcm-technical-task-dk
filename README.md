## DCM Technical Task: Test runner web application
This is a web application that provides a central place to run Python-based tests.
These tests run in a shared environment.

There are some sample tests (some failing, some not,
and some that have delays for more realism) and the tests for this application can also be run in the runner.
The tests are executed on the machine that hosts the test executor application.

###################################

### Feature to be implemented:

* Users of the test runner have asked to have the ability to upload a new test into the application and run it.  For 
  example, they would like to be able to upload a simple test that calls a public API and then validates the response.
    * ####Acceptance Criteria: 
      * The user can select a file from their computer and upload it into one of the 
      directories or a new directory for the uploaded tests and then they should be able to run that test.
      
      * The frontend and backend components of this new feature should also be tested 
      (They don't have to be extensively tested just show us how you write tests for
      the backend API and for testing the DOM changes on a click event for the frontend)


###################################

### Bug to fix
When running 2 tests in the same environment, there is the possibility that when multiple workers are being used, that
a race condition could occur where both the workers think the environment is available and as a result both try to use
it.

Please find a way to fix this issue.

Hint: The issue is in the api/tasks.py file

###################################

### After the bug is fixed and feature is implemented, make sure that the following works properly:
* Creating a new test run with the following:
    * Username
    * Test environment ID (choose an ID between 1 and 100)
    * Choosing one or more files to test (the available tests are read from file system)
    
**NOTE:** It should not be possible to run a test in an environment where there
    is already a test running, the next test for that environment should wait until the previous is complete!
    
* List all previous test runs in a table including their outcome (failed, success,
running)

* List details for one test run
    * Show username, test env ID and what was tested
    * Show the full log that the test created



## How to Run the current project locally
```
cd ionos
cp .env.dist .env
cd ..
docker-compose build
docker-compose up
docker-compose run web pytest -vv api
```
You should now be able to go to http://127.0.0.1:8081/ to see the FE.

Since the Frontend is served through Django when running in the container, if you want to have hot reloading
while developing the frontend aspect, run yarn start in the frontend folder to run that locally.


## How it works
When creating a new test run request, it triggers a celery task to execute it on the selected env. If the env is busy, we 
wait for some time (or give up after some retries) and when it's done, we change the status of the request and save the logs.
in the frontend, we call the listing api every one second to get a live updates (also the details api).

In the project we have sample-tests directory to save all the sample tests that can be run. Also, you can choose the actual test
files from api.tests dir. The test path is a multi-select, you can choose one or more file to test at a time and these paths
are created automatically in a migration file api/migrations/0002_auto_20200706_1208.py
