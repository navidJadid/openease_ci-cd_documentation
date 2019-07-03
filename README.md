# openease_ci-cd_documentation
The openEASE-project is a large open-source-software package which is developed as a tool for research on Robotics and Artificial Intelligence by the department for AI at the University of Bremen.
Like any bigger software, it is desirable to establish certain working and coding guidelines, as well as to automatize whatever is possible in the development procedure like testing and deployment. Thus with this document we would like to explain the current CI/CD structure of our project, our ambitions for the future, as well as the core principles that we would like encourage for the development of this project, both for internal researchers as well as open-source contributors.

## Table of Contents
**TODO: Table of Contents**

1. [chapter](#link_to_chapter)
2. [chapter](#link_to_chapter)

### CI/CD
Overall we will only give a short summary of the necessary here, but if you are more interested in CI/CD, then we recommend reading this extensive [series of articles by Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment) which we more or less indirectly cite here. 

1. **What are CI and CD?**  
    _CI_ is an abbreviation for _Continuous Integration_ which is the practice to merge into master/production/development (or however your release branch is called). Upon merging, the pipeline will run automated tests (therefore TDD is necessary, we touch on that in [Chapter **TODO**]()) and possibly other tasks. If tests fails, the developers now know there are problems and these should be resolved immediately, before other changes are merged into master. This is based on the premise, that master should whenever possible be in a state that is deployable.  
    In the very simplest case there exists only a master branch which every one is pushing to. This might work for small productions, but larger ones naturally require feature, patch, or version branches. These then regularly merge changes from master into themselves, to minimize merge conflicts when merging back into it.

    <sub>**Side note:** For branching and in general more in-depth explanations of these principles, please refer to mentioned [set of articles by Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment).</sub>

    _CD_ on the other hand can refer to either _Continuous Delivery_ or _Continuous Deployment_:
    - _Continuous Delivery_ aims to automate the delivery or release process, so any build can be released with the press of a button. Through CI we are already provided with high confidence that our software is stable, so this automation of release reduces yet another possible point of (human) failure.  
    - _Continuous Deployment_ basically combines the previous two practices. When committed changes successfully pass through all the stages of the _CI_-pipeline, that build will be automatically released. This means for one, that there is no release day anymore, and for the other, that developers can see their changes go live within a few minutes after pushing. 

2. **Why use CI and CD?**
Ever been through "Merging Hell"? Then you know why. If not, believe us the "Hell" in the name is well deserved. Basically this refers to the practice to have several development branches which needed to be merged into production before each deadline. This caused a bunch of merge conflicts, which needed to be fixed.
**TODO**

3. **Current CI/CD pipeline of the project: Travis-CI**  
**TODO**  
Travis-CI is our current CI-provider of choice. Travis-CI offers unlimited builds for Open-Source-Projects with 15 minutes of pipeline runtime for each build. 

4. **Future aspirations (alias the _dream_)**  
**TODO**

### Test-Driven-Development
As mentioned in the previous chapter, for the whole CI pipeline to work it is necessary to have (unit-)tests which cover majority of the code that is written. Therefore we encourage everyone to develop test-first for this project. The procedure should be as follows:

1. write unit-tests which test for the desired behaviour of the method
2. write actual code
3. run tests on code
4. repeat steps 2 & 3 until tests run successful

Tests are the tool which give us the confidence to merge into `master`, which is especially true when the code-base has a high test coverage. Later in [Chapter **TODO**]() we will present some tools and libraries which determine the test coverage. Of course writing tests will not catch all errors and problems which might come up, but they are a reliable, fast and automated way to ensure correct behaviour for (the majority of) our code. Tests also scale up elegantly from low-level modules to high-level ones.

The philosophies when writing tests should be:

- **test only one 'thing' per test**:  
    Each test should only test for one scenario or thing; for example, one test could cover what happens when `NULL` is passed as a parameter, while another one tests what happens when `INT_MAX` is passed. This will help during debugging as depending on the failing test it is easier to find the section in the code which does not work properly. E.g., If test `test_paramIsNull_returnZero()` fails you would know that the method is not returning 0 when `NULL` is passed as a parameter. This simply would not be possible when tests are multipurpose and cover all kind of scenarios.
- **cover the different scenarios aka have high test coverage**:  
    Testing for different kinds of scenarios ensures that your current implementation will run correctly, but it also ensures that future ones will. This alone should be enough motivation to write tests. Imagine switching out a dependency because it is deprecated, but then the program keeps crashing. With existing tests it will much easier to make out what is going wrong with the new library which you are using.
- **tests should execute quickly**:  
    This has two primary reason, the first one being more important than the second one:
    
    1)  To get timely feedback: If you made a change to a block of code, you do not want to wait 5 minutes each time to see whether your tests run successful. This will in the long run discourage developers to run (and maybe even write) tests, which obviously is not desirable.
    2)  Most CI-pipelines have limited run-time per build (usually around 10-20 minutes). If your tests take too much time to execute the CI pipeline will just time out, and you will not get any results.
    
    As a hint, it is important to understand that if a code block runs with outer dependencies, it is often enough to just mock or stub these. If you wanna read up on mocking and stubbing, refer to [this wonderful article by Martin Fowler](https://www.martinfowler.com/articles/mocksArentStubs.html) or the more compact answers in [this stackoverflow article](https://stackoverflow.com/questions/3459287/whats-the-difference-between-a-mock-stub?page=1&tab=votes#tab-top).

So far we have only talked about unit-test, but there is also something called _integrated tests_. Unit tests for the most part are trivial to write, so there literally are no excuses to not write them. Integrated tests are a different beast though as they intend to test parts of the system in conjunction which presents its own set of difficulties. 

_We are working to extend this part of the documentation, so please be patient for the update on integrated testing :)_  

<sub>**Side note for curious people:** There is a movement expanding on this idea called 'Documentation-Driven-Development' (DDD). The idea is to first write documentation which describes the desired behaviour, then to write tests according it, before finally writing the code. You can read up on it [here](https://gist.github.com/zsup/9434452) and a more practical and extensive article [here](https://medium.com/blacklane-engineering/documentation-driven-development-8b2ff119104f). DDD comes with its own merits and downsides, but maybe it is worth a look for some people :)</sub>

### Software Versioning and Commit Standards
Software Versioning is a delicate topic for many software engineers. It should be transparent as well as consistent. In the same way it should be handled for this project, as we intend to have a transparent versioning scheme for the end-users.  
Now depending on the kind of software, versioning should be handled differently. We will list a few types of software and our preferred versioning scheme. We aim to extend to this list, so if you have suggestions, let us now.

- **Public APIs and Packages**:  
    For public APIs and Packages we would like to encourage use of [SemVer](https://semver.org/), which is an abbreviation for _semantic versioning_. To quote the official [documentation](https://semver.org/):

    > Given a version number MAJOR.MINOR.PATCH, increment the:
    >
    > MAJOR version when you make incompatible API changes,  
    MINOR version when you add functionality in a backwards-compatible manner,    and  
    PATCH version when you make backwards-compatible bug fixes.

    All other changes, like on the documentation or tests would not cause a version bump, as they do not directly affect production code.
    
    Obviously use SemVer only after you release a stable build, aka `version 1.0.0`. As long as you linger in versions `0.x.y` you do not need to issue a `MAJOR`-version bump.
    
    This versioning convention will cause a lot of version bumps, but this is intended behaviour. We want to give the users the transparency of our development, as well as the choice of versions compatible with their systems.

    Now you might think that this all is great, but how do we guarantee that this standard is followed? Well, luckily there are tools which automate this, namely,  [semantic-release](https://semantic-release.gitbook.io/semantic-release/). `semantic-release` automatically bumps your version number based on your commit message (which we will explain in a minute), generates release notes and can even publish the package. To our knowledge, it is currently available as `npm`- and `pypi`-package (s. [Chapter **TODO**]() on language specific tools).
    
    <sub>**Side note:** For more explanation on the idea of SemVer and `semantic-release`, we recommend watching the talk [_Kill all humans_](https://www.youtube.com/watch?v=ZXyx_1kN1L8) by one of the engineers of `semantic-release` or reading through the official [documentation](https://semantic-release.gitbook.io/semantic-release/).</sub>
    
    For `semantic-release` to work properly, it requires commits to be formatted to a standard which by default is the [_Angular Commit Message Standard_](https://gist.github.com/stephenparish/9941e89d80e2bc58a153#format-of-the-commit-message), but it technically could be customized to any other standard. You can read through the specifications [here](https://gist.github.com/stephenparish/9941e89d80e2bc58a153#format-of-the-commit-message). `semantic-release` basically analyzes the commit message and then generates version number and changelog based on that. This drastically reduces the points of failure, and you as a developer just need to make sure to write correct commit messages as everything else is handled for you.  
    For adhering to the standard, we recommend using the [Commitizen CLI-tool](https://commitizen.github.io/cz-cli/) or the JetBrains-IDE plugin [Git-Commit-Template](https://plugins.jetbrains.com/plugin/9861-git-commit-template) if you are working with one of their IDEs. Even if you are not using `semantic-release`, we advise to use this standard, as it helps to keep the commits readable and standardized all over the project.  
    
    <sub>**Side note:** If your commits are not formatted correctly, `semantic-release` will not work which could cause problems for the whole CI/CD pipeline.</sub>
    
- **Larger Software Products**:  
    Obviously unlike public APIs and packages, too frequent version bumps might be undesirable. Imagine your operating system needing to update hundreds of times a day, because developers pushed bugfixes and little features.  
    Instead releasing new versions manually or in fixed interwalls (weekly, monthly, per quarter, etc.) sounds more reasonable.
    
    _We are working to extend this part of the documentation, so please be patient for the update on software versioning of larger software products :)_  

### Repository Setup
- setup
- setup tool
- Badges:
    - current release
    - travis build
    (- semantic-release)
- Travis-CI:  
    Travis-CI can be activated from the GitHub marketplace, then the repository needs to be manually checked on [travis-ci.org](https://travis-ci.org).  
You're almost ready to go, just add a `travis.yml` file to your repository. This file will tell Travis-CI, among other things, where to find the tests, which language the code base is written in, which test framework to use, etc.  
    **TODO**  
    For more details, please refer to the [Travis-CI documentation](https://docs.travis-ci.com/).

    Travis-CI can also be set up together with `semantic-release`. For that, please refer to the official [`semantic-release` documentation](https://semantic-release.gitbook.io/semantic-release/).

### Coding Principles
We would like to encourage certain coding principles for this project, in order to keep the code base consistent, maintainable, and easy to read. In large our coding guidelines are derived from the book _Clean Code: A Handbook of Agile Software Craftsmanship_ by Robert C. Spies, maybe better known by his alias Uncle Rob. We **strongly** recommend reading it, as it provides great ideas and principles to write readable and maintainable software. Now let us look at the coding principles which we regard as most important:

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
    - code styling: Eslint (link to [openease-eslint-config](https://www.npmjs.com/package/@code-iai/eslint-config-openease))
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: jest
    - external tools: Snyk, Greenkeeper
    - NodeJs stuff: version etc.
    - ECMA 2017 & Browserify
- Python:
    - code styling: PEP-8 (auto enabled in JetBrains-IDEs) (& Pylint)
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: pytest (monkeypatch)
    - Version: 2.7 due to ros -> No support from 2020 on...
    (- pipenv?)
- Docker