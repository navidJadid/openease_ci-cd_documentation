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
**TODO**
- Test Coverage

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
    
    ```python
    # variable to store population size
    a = get_population_size() 
    ```
    
    or even worse
    
    ```python
    # variable to store population size
    population_size = get_population_size() 
    ```
    
    Therefore, the more you code with this and the principles of 1) in mind, the more you will see that many comments are just totally unnecessary and only clutter the code.
        
    Of course, there are cases where you will not get around writing comments, for example:
    - When it is necessary to explain a particularity or a choice for a block of code
    - Documentation for API's which are not project intern
3. Encapsulation and Side-Effects  
    Wrapping external modules

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