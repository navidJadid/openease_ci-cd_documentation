# openease_ci-cd_documentation
The openEASE-project is a large open-source-software package which is developed as a tool for research on Robotics and Artificial Intelligence by the department for AI at the University of Bremen.
Like any bigger software, it is desirable to establish certain working and coding guidelines, as well as to automatize whatever is possible in the development procedure like testing and deployment. Thus with this document we would like to explain the current CI/CD structure of our project, our ambitions for the future, as well as the core principles that we would like encourage for the development of this project, both for internal researchers as well as open-source contributors.

## Table of Contents
**TODO: Table of Contents**

1. [chapter](#link_to_chapter)
2. [chapter](#link_to_chapter)

### CI/CD
Overall we will only give a short summary of the necessary here, but if you are more interested in CI/CD, then we recommend reading this extensive [series of articles by Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment). 

**TODO**
1. What are CI and CD?
Continuous Integration
Continuous Delivery
Continuous Deployment

2. Why use CI and CD?

3. Current CI/CD pipeline of the project: Travis-CI

4. Future aspirations (alias the **dream**)

### Test-Driven-Development
As mentioned in the previous chapter, for the whole CI pipeline to work it is necessary to have (unit-)tests which cover majority of the code which is written. Therefore we encourage everyone to develop test-first for this project. The procedure should be as follows:

1. write unit-tests which test for the desired behaviour of the method
2. write actual code
3. run tests on code
4. repeat steps 2 & 3 until tests run successful

Tests are the tool which give us the confidence to merge into `master`, which is especially true when the code-base has a high test coverage. Later in [chapter **TODO**]() we will present some tools and libraries which determine the test coverage. Of course writing tests will not catch all errors and problems which might come up, but they are a reliable, fast and automated way to ensure correct behaviour for (the majority of) our code. Tests also scale up elegantly from low-level modules to high-level ones.

The philosophies when writing tests should be:

- **test only one 'thing' per test**:  
    Each test should only test for one scenario or thing; for example, one test could cover what happens when `NULL` is passed as a parameter, while another one tests what happens when `INT_MAX` is passed. This will help during debugging as depending on the failing test it is easier to find the section in the code which does not work properly. E.g., If test `test_paramIsNull_returnZero()` fails you would know that the method is not returning 0 when `NULL` is passed as a parameter. This simply would not be possible when tests are multipurpose and cover all kind of scenarios.
- **cover the different scenarios aka have high test coverage**:  
    Testing for different kinds of scenarios ensures that your current implementation will run correctly, but it also ensures that future ones will. This alone should be enough motivation to write tests. Imagine switching out a dependency because it is deprecated but then the program keeps crashing. With existing tests it will much easier to make out what is going wrong with the new library which you are using.
- **tests should execute quickly**:  
    This has two primary reason, the first one being more important than the second one:
    
    1)  To get timely feedback: If you made a change to a block of code, you do not want to wait 5 minutes each time to see whether your tests run successful. This will in the long run discourage developers to run (and maybe even write) tests, which obviously is not desirable.
    2)  Most CI-pipelines have limited run-time per build (usually around 10-20 minutes). If your tests take too much time to execute the CI pipeline will just time out and you will not get any results.
    
    As a hint, it is important to understand that if a code block runs with outer dependencies, it is often enough to just mock or stub these. If you wanna read up on mocking and stubbing, refer to [this wonderful article by Martin Fowler](https://www.martinfowler.com/articles/mocksArentStubs.html) or the more compact answers in [this stackoverflow article](https://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub?page=1&tab=votes#tab-top).

So far we have only talked about unit-test, but there is also something called _integrated tests_. Unit tests for the most part are trivial to write, so there literally are no excuses to not write them. Integrated tests are a different beast though as they intend to test parts of the system in conjunction which presents its own set of difficulties. 

_We are working to extend this part of the documentation, so please be patient for the update on integrated testing :)_  

**Side note for curious people:** There is a movement expanding on this idea called 'Documentation-Driven-Development' (DDD). The idea is to first write documentation which describes the desired behaviour, then to write tests according it, before finally writing the code. You can read up on it [here](https://gist.github.com/zsup/9434452) and a more practical and extensive article [here](https://medium.com/blacklane-engineering/documentation-driven-development-8b2ff119104f). DDD comes with it's own merits and downsides, but maybe it is worth a look for some people :)

### Software Versioning and Commit Standards
**TODO**
- SemVer (include semantic release & 'kill all humans')
- Angular Commit Standard
- Commitizen & JetBrains-IDE plugin Git-Commit-Template

### Travis-CI
- travis.yml

### Repository Setup
- setup
- setup tool
- Badges:
    - current release
    - travis build
    (- semantic-release)

### Coding Principles
We would like to encourage certain coding principles for this project in order to keep the code base consistent, maintainable and easy to read. In large our coding guidelines are derived from the book _Clean Code: A Handbook of Agile Software Craftsmanship_ by Robert C. Spies, maybe better known by his alias Uncle Rob. We **strongly** recommend reading it, as it provides great ideas and principles to write readable and maintainable software. Now let us look at the coding principles which we regard as most important:

**TODO**
1. Variable, Class, Module Naming
2. Comments  
    Go by this rule of thumb: The more you need to comment your code, the more you know something is probably wrong with it. Comments can (and probably will) clutter your code and make it less readable. Most of the time, if variables, classes and functions are named correctly and proper encapsulation is used, then there is (almost) no need for comments.
    
    Let's consider this very crude example in python:
    
    ```
    # variable to store population size
    a = get_population_size() 
    ```
    
    or even worse
    
    ```
    # variable to store population size
    population_size = get_population_size() 
    ```
    
    **TODO**
    
    Therefore, the more you code with this and the principles of 1) in mind, the more you will see that many comments are just totally unnecessary and only clutter the code.
        
    Of course, there are cases where you will not get around writing comments, for example:
    - When it is necessary to explain a particularity or a choice for a block of code
    - Documentation for API's which are not project intern
3. Encapsulation and Side-Effects  
    Wrapping external modules (library tests)

- Code-Styling in the next chapter
- JetBrains IDEs

### Language Specific Stuff
**TODO**
- JavaScript:
    - code styling: Eslint (link to openease-eslint-config)
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: jest
    - external tools: Snyk, Greenkeeper
    - NodeJs stuff: version etc.
    - ECMA 2017 & Browserify
- Python:
    - code styling: PEP-8 (auto enabled in JetBrains-IDEs) (& Pylint)
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: pytest (monkeypatch)
    - Version: 2.7 -> No support from 2020 on...
    (- pipenv?)
- Docker