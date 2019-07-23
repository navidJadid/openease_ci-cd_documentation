# openease_ci-cd_documentation
The openEASE-project is a large open-source-software package which is developed as a tool for research on Robotics and Artificial Intelligence by the department for AI at the University of Bremen.  
Like any bigger software, it is desirable to establish certain working and coding guidelines, as well as to automatize whatever is possible in the development procedure like testing and deployment. Thus with this document we would like to explain the current CI/CD structure of our project, our ambitions for the future, as well as the core principles that we would like encourage for the development of this project, both for internal researchers as well as open-source contributors.

## Table of Contents

1. [CI/CD](#1-cicd)
2. [Test-Driven-Development](#2-test-driven-development)
3. [Software Versioning & Commit Standards](#3-software-versioning--commit-standards)
4. [Repository Setup](#4-repository-setup)
5. [Coding Principles](#5-coding-principles)
6. [Language Specific Stuff](#6-language-specific-stuff)

### 1. CI/CD
Overall we will only give a short summary of the necessary here, but if you are more interested in CI/CD, then we recommend reading this extensive [series of articles by Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment) which we more or less indirectly cite here. 

1. **What are _CI_ and _CD_?**  
    _CI_ is an abbreviation for _Continuous Integration_ which is the practice to merge into the `master`-branch (or however your release branch is called) as often as possible. Upon merging, the pipeline will run (among other tasks) automated tests in augmented production environments (therefore TDD is necessary, we touch on that in [Chapter 2: Test-Driven-Development](#2-test-driven-development)). If tests fails, the developer now knows there are problems and that these should be resolved immediately, before other changes are merged into master. This is based on the premise, that master should whenever possible be in a state that is releasable.  
    In the very simplest case there exists only a master branch which everyone is pushing to. This might work for small productions, but larger ones naturally require feature, patch, or version branches. These then regularly merge changes from master into themselves, to minimize merge conflicts when merging back into it.

    <sub>**Side note:** For branching and in general more in-depth explanations of these principles, please refer to mentioned [set of articles by Atlassian](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment).</sub>

    _CD_ on the other hand can refer to either _Continuous Delivery_ or _Continuous Deployment_:
    - _Continuous Delivery_ aims to automate the delivery or release process, so any build can be released with the press of a button. Through _CI_ we are already provided with high confidence that our software is stable, so this automation of release reduces yet another possible point of (human) failure.  
    - _Continuous Deployment_ basically combines the previous two practices. When committed changes successfully pass through all the stages of the _CI_-pipeline, that build will be automatically released. This means for one, that there is no release day anymore, and for the other, that developers can see their changes go live within a few minutes after pushing. 

2. **Why use _CI_ and _CD_?**
    Ever been through "Merging Hell"? Then you know why. If not, believe us the "Hell" in the name is well deserved. Basically this refers to the practice to have several development branches which need to be merged into production before each deadline. This naturally causes a bunch of merge conflicts, which of course need to be fixed before release. You might be able to imagine this is quite stressful and we do not want to put anyone through this.

    With _CI_ we already resolve conflicts as soon as they appear, tests give us confidence our build is actually stable, running them in production environment (in addition to only on our own machines) reassures us of that even more, and with _CD_ we possibly do not even have release dates anymore, as we just release any changes that are stable according to the pipeline.

    **To conclude:** _CI/CD_ increases quality of software, automates redundant tasks, and reduces human error as much as possible. When a pipeline is set up correctly, you as a developer can focus more on writing code than wasting time manually deploying or stressing yourself out about needing to merge 10 branches into master before friday.

3. **Current CI/CD pipeline of the project: Travis-CI**  
    Now after all this talk, you might think we have these systems implemented all over the project already, right?
    
    Not right.
    
    Not right?!  
    <sub>(Kudos, if you get the reference...)</sub>

    The whole point of this document is to encourage the implementation of these systems in this project. For clarification: By no means do you _have_ to use them for non-public projects, as it can be straight up excessive (of course you can, but your mileage may wary). However, when it comes to our open-source software, we think offering transparency, consistency, and software of high quality should be priorities. Yet, so far, only few of our open-source projects implement _CI_, code quality tools, if anything at all... (don't even mention _CD_...)

    Concerning our _CI_-provider of choice: The parts of our project that have a _CI_-pipeline use [_Travis-CI_](https://travis-ci.org). _Travis-CI_ offers unlimited builds for Open-Source-Projects with 15 minutes of pipeline runtime for each build. We will talk more about _Travis-CI_ in [Chapter 4: Repository Setup](#4-repository-setup).

4. **Future aspirations (alias the _dream_)**  
    The dream is to implement _CI/CD_ for all of openEASE: When changes are committed they run through the pipeline, are versioned properly and automatically shipped. Then, when dependencies get updated, the modules get notified, run the build with the updated dependency, and automatically deploy or issue pull requests upon successfully passing the pipeline. Alongside that, feature or patching branches automatically merge in changes from master regularly to avoid drifting off.

    Naturally, setting up these systems should be easy and quick (otherwise nobody will use them...), therefore, we plan to provide tools for exactly that. For example, we would like to have a tool which automates setting up repositories together with all the necessary _CI/CD_ stuff, coding quality tools, etc.

    <sub>**Side note:** If you are interested in developing such software, please get into contact with us.</sub>

    Once more, the point is, the more of these tasks can be automated, the more time is left for you to spend on researching and developing, while having good faith in the stability of the software. Hence, seeds we plant now will reap enormous benefits later...

### 2. Test-Driven-Development
As mentioned in the previous chapter, for the whole CI pipeline to work it is necessary to have (unit-)tests which cover majority of the code that is written. Therefore, we encourage everyone to develop test-first for this project. The procedure should be as follows:

1. write unit-tests which test for the desired behaviour of the method
2. write actual code
3. run tests on code
4. repeat steps 2 & 3 until tests run successful

Tests are the tool which give us the confidence to merge into `master`, which is especially true when the code-base has a high test coverage. Later in [Chapter 6: Language Specific Stuff](#6-language-specific-stuff) we will present some tools and libraries which determine the test coverage. Of course writing tests will not catch all errors and problems which might come up, but they are a reliable, fast and automated way to ensure correct behaviour for (the majority of) our code. Tests also scale up elegantly from low-level modules to high-level ones.

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

### 3. Software Versioning & Commit Standards
Humans can be arbitrary with their versioning schemes, hence software versioning is a delicate topic for many software engineers. Most would agree, it should be transparent as well as consistent. Now depending on the kind of software, versioning should be handled differently. We will list a few types of software and our preferred versioning scheme. We aim to extend to this list, so if you have suggestions, let us now.

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

    Now you might think that this all is great, but how do we guarantee that this standard is followed? Well, luckily there are tools which automate this, namely,  [semantic-release](https://semantic-release.gitbook.io/semantic-release/). `semantic-release` automatically bumps your version number based on your commit message (which we will explain in a minute), generates release notes and can even publish the package. To our knowledge, it is currently available as `npm`- and `pypi`-package (s. [Chapter 6: Language Specific Stuff](#6-language-specific-stuff)).
    
    <sub>**Side note:** For more explanation on the idea of SemVer and `semantic-release`, we recommend watching the talk [_Kill all humans_](https://www.youtube.com/watch?v=ZXyx_1kN1L8) by one of the engineers of `semantic-release` or reading through the official [documentation](https://semantic-release.gitbook.io/semantic-release/).</sub>
    
    For `semantic-release` to work properly, it requires commits to be formatted to a standard which by default is the [_Angular Commit Message Standard_](https://gist.github.com/stephenparish/9941e89d80e2bc58a153#format-of-the-commit-message), but it technically could be customized to any other standard. You can read through the specifications [here](https://gist.github.com/stephenparish/9941e89d80e2bc58a153#format-of-the-commit-message). `semantic-release` basically analyzes the commit message and then generates version number (according to SemVer) and changelog based on that. This drastically reduces the points of failure, and you as a developer just need to make sure to write correct commit messages as everything else is handled for you.  
    For adhering to the standard, we recommend using the [Commitizen CLI-tool](https://commitizen.github.io/cz-cli/) or the JetBrains-IDE plugin [Git-Commit-Template](https://plugins.jetbrains.com/plugin/9861-git-commit-template) if you are working with one of their IDEs. Even if you are not using `semantic-release`, we advise to use this standard, as it helps to keep the commits readable and standardized all over the project.  
    
    <sub>**Side note:** If your commits are not formatted correctly, `semantic-release` will not work which could cause problems for the whole CI/CD pipeline.</sub>
    
- **Larger Software Products**:  
    Obviously unlike public APIs and packages, too frequent version bumps might be undesirable. Imagine your operating system needing to update hundreds of times a day, because developers pushed bug-fixes and little features.  
    Instead, releasing new versions manually or in fixed intervals (weekly, monthly, per quarter, etc.) sounds more reasonable.
    
    _We are working to extend this part of the documentation, so please be patient for the update on software versioning of larger software products :)_  

### 4. Repository Setup
When setting up a new repository please go through the following steps (if possible):

1. Add a `.gitignore`-file to your repository
2. Add a `README.md` which at the very least explains the purpose of the repository. You are free to add more information, but make sure to maintain it properly during development :)
3. Add a testing suite with code coverage tools to your repository
4. Enable Travis-CI for your repository and add a `travis.yml` to your repository:  
    Travis-CI can be activated from the GitHub marketplace, then the repository needs to be manually checked on [travis-ci.org](https://travis-ci.org).  
You're almost ready to go, just add a `travis.yml` file to your repository. This file will tell Travis-CI, among other things, where to find the tests, which language the code base is written in, which test framework to use, etc.  
      
    For more details, please refer to the [Travis-CI documentation](https://docs.travis-ci.com/).

    Travis-CI can also be set up together with `semantic-release`. For that, please refer to the official [`semantic-release` documentation](https://semantic-release.gitbook.io/semantic-release/).
5. Add code-quality tools, like linters or tools like [_Codacy_](https://www.codacy.com/)
6. Consider adding tools for software versioning, like [`semantic-release`](https://semantic-release.gitbook.io/semantic-release/)
7. Add badges to your `README.md`, so readers cab get a quick overview of the state of the project. A few ideas for possible badges:
    - current release
    - current stable release
    - travis build
    - license
    - dependencies
    - docker build or alike
    - semantic-release
    - codacy
    - registry downloads

As mentioned in [Chapter 1: CI/CD](#1-cicd), we would like to develop a tool which automates all of this. Until then these steps will have to be done by hand. Please be patient for the tool to ship. If you are interested in the development of such a tool, consider getting in touch with us :)

_We are working to extend this part of the documentation, so please be patient for the update on repository setup :)_

### 5. Coding Principles
A code base should, first and foremost, be easily read- and maintainable. It is developers, humans, that in the end have to work on the code base. For that reason, these goals should have the highest priority (obviously except for performance-critical applications or code segments). If the project is not maintainable, it is doomed to fail sooner or later. Spending a bit more time writing tests or refactoring goes a long way, thus we would like to encourage a few easy-to-maintain coding principles.

In large our coding guidelines are derived from the book _Clean Code: A Handbook of Agile Software Craftsmanship_ by Robert C. Spies, maybe better known by his alias Uncle Rob. We **strongly** recommend reading it, as it provides great ideas and principles to write readable and maintainable software. Now let us look at the coding principles which we regard as most important (aside from a proper project architecture):

1. **Variable, Class, Module Naming**  
    First and foremost, it is important to find precise and meaningful names for the components of your software. Meaningless or outright cryptic names will not do you and the other developers any favors. Let us look at a few examples:
    
    ```python
    # bad
    a = employee.get_age()
    e_age = employee.get_age()
    emage =  employee.get_age()
    int_e_age = employee.get_age()
    int_employee_age = employee.get_age()
    
    # good
    employee_age = employee.get_age()
    ```
        
    If there is anything you wanna take from this, it would be: Good code should almost be readable like a book, because as far as we are concerned, developers usually speak English not Cryptic.
    
    Reading `a` in your code does not give you any valuable information on the variable, you will certainly find yourself in the need to jump to the declaration. Next, the name `e_age` is also quite inconclusive, because you might know it represents some age, but you cannot be sure what `e` stands for. `emage` is even worse, because it does not separate the words in any way, and also one could think this is a typo for `image`. Lastly, you do not (I emphasize: **DO NOT!!!**) need to include the type into any method or variable names, as your IDE should be able resolve them in real-time.
    
    <sub>**Side note:** In this example we used underscore name separation (which is standard practice in python), but of course feel free to use the accepted naming practice in your language, like, for example, [camelCase](https://sanaulla.info/2008/06/25/camelcase-notation-naming-convention-for-programming-languages/) in Java.</sub>
    
    Choosing precise and meaningful names allows us, among other things, to omit writing comments and documentation (at least project-intern), because the name indicates the function. For the same reason, developers will be forced less often to jump to the declaration.  
    Also do not worry about names being too long. With auto-completion in IDEs nowadays, you do not actually have to type them out. A precise name gives us readable code with all the aforementioned benefits. Of course, whenever you find a better and shorter name, consider refactoring it.

2. **Comments**  
    Go by this rule of thumb: The more you need to comment your code, the more you know something is probably wrong with it. Comments can (and probably will) clutter your code and make it less readable. Let us go over a few examples:
     
     - **Comments instead of good naming**:  
        Most of the time, if variables, classes and functions are named correctly and properly encapsulated, there is (almost) no need for comments. Let's consider this very crude example in python:
    
        ```python
        # variable to store population size
        a = get_population_size() 
        ```
        
        or even worse
        
        ```python
        # variable to store population size
        population_size = get_population_size() 
        ```
        
        In the first example, the variable naming is just bad, as the name `a` just does not tell us, what it is representing. A comment does not fix that issue, because to understand what is stored in `a`, we always have to jump back to this line. We hope you understand by now, that you should not name global use variables in that manner. Of course naming the iterator variable of your small for-loop like that is totally fine.
         
         The second example has a great name for the variable, therefore, the comment is totally unnecessary and it can be removed, like such:
        
        ```python
        population_size = get_population_size()
        ```
        
        Now everyone seeing the variable will know what it stored inside, and we can see, how proper naming makes the comment straight up unnecessary.
    
    - **Leaving dead code in comments**:  
        Imagine going through some code, and coming across this tragedy:
        
        ```python
        # FIXME
        # do_something_uncertain()
        # other_stuff_that_does_not_belong_here()
        do_something_certain()
        ```
        
        How are you supposed to know, what to do with those comments and whether they are of importance. Such comments only cause confusion and clutter the code. Instead you should delete them and consider creating an issue or a separate branch. With version history there should be no problem finding these lines of code again, even after you have deleted them.
    
    Therefore, the more you code with these principles and the ones from 1) in mind, the more you will see that many comments are just totally unnecessary and only clutter the code.
        
    Of course, there are cases where you will not get around writing comments, for example:
    - When it is necessary to explain a particularity or a choice for a block of code:
    
        ```python
        '''
        Explain particularities about the method ...
        '''
        complicated_method(some_parameters ...){
          ...
        }
        ```
        
        or
        
        ```python
        some_method(some_parameters ...){
          ...
    
          '''
          Explain particularities about block of code ...
          '''
          var = get_some_complicated_stuff()
          more_complicated_stuff(var)
    
          ...
        }
        ```
    
    - Documentation for API's which are not project intern:
    
        ```python
            '''
            Describe what the method does...
            
            Parameters:
            -----------
            param_1: type
                     description of param_1 ...
            param_2: type
                     description of param_2 ...
            
            Returns:
            --------
            type
                 description of that the class returns
            '''
            public_api_method(param_1, param_2 ...){
              ...
            }
        ```
     
      <sub>**Side note:** This example uses the [numpy-docstring standard](https://numpydoc.readthedocs.io/en/latest/format.html#method-docstrings).</sub>
     
    - Short in-line comments, which are preferred, because they do not clutter the code as much:
        
        ```python
        do_something()           # explain something briefly
        ```
    
3. **Encapsulation, Levels of Abstraction, and Side-Effects**  
    Functions, modules, classes, etc., should all follow the single responsibility principle and be properly encapsulated. This will lead the code to be more 'robust', because it has 'less reasons to change', due to the minimal amount of responsibilities. This also kind of implies, that whenever possible, we keep components small and on the same level of abstraction. Let us look at an example, to see how these ideas help to keep code readable and properly encapsulated:
    
    ```python
    print_country_stats(country){
      [...]
      
      population = country.get_population()
      
      income_source_a = country.get_total_income_from_source_a()
      income_source_b = country.get_total_income_from_source_b()
      [...]
      
      total_personal_income = income_source_a + income_source b + [...]    
       
      income_per_capita = total_personal_income / population
      
      [...]
      
      print(country.name)
      print('Income per capita: {}', income_per_capita)
      [...]
    }
    ```
    
    This code looks messy and unreadable. We have mixed all kinds of levels of abstraction, the method is too long and handles way too many responsibilities, like calculating the values which it wants to print. Ideally it would just get these values from the `country`-object and print them. Therefore, we would like to extract a method which calculates the income-per-capita of the `country`-object. Then our code could look something like this:
    
    ```python
    print_country_stats(country){
      name = country.name
   
      [...]
      
      income_per_capita = country.calculate_income_per_capita()
      
      [...]
      
      print(name)
      print('Income per capita: {}', income_per_capita)
      [...]
    }
 
    Class Country{
      [...]
   
      calculate_income_per_capita() {
        population = get_population()
      
        income_source_a = get_total_income_from_source_a()
        income_source_b = get_total_income_from_source_b()
        [...]
      
        total_personal_income = income_source_a + income_source b + [...]    
       
        return total_personal_income / population
      }
 
      [...]
    }
    ```
    
    If we now take a look at the extracted method, we can directly see calculating the total income inside the method really clutters the code and also does not maintain the same level of abstraction. Instead it should just be a separate method of the `Country`-class:
    
    ```python
    Class Country{
      [...]
      
      calculate_total_personal_income() {
        total_personal_income = 0
        
        for income_source in personal_income_sources:
          total_personal_income += income_source
     
        return total_personal_income
      }
   
      calculate_income_per_capita() {
        population = get_population()
        total_personal_income = calculate_total_personal_income()    
       
        return total_personal_income / population
      }
      
      [...]
    }
    ```
    
    With that our previous method just becomes a three-liner. Much cleaner, more maintainable, and more readable :)
    
    Next, we would like to encourage the wrapping of external modules or libraries; even standard libraries, as these are not exempt to change either. The consequence is that all our business code will consist of code written internally by us, and external sources just become kind of plugins. This drastically reduces possible points of failure and eases maintainability. Imagine coming in need to switch some library. If it was not wrapped before, you have to refactor all the occurrences in the code. On the other hand, if you used a wrapper, you just change the occurrences in the wrapper class and make them work with the existing unit-tests. The code will work the same before and afterwards, and you did not need to change any other code.
    
    Often people associate these measures with object-oriented programming, but these can honestly be applied to any kind of programming, be it functional, data oriented, etc.
    
    <sub>**Side note:** If you wanna read more on encapsulation and the reasoning behind single responsibility components, refer to _Clean Code: A Handbook of Agile Software Craftsmanship_ by Uncle Rob, or [this blog article by him](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html).</sub>
    
    One last thing you should keep in mind, is to avoid so called _side effects_. When your method is called `get_total_number_of_employees()`, I expect it to only do as it says and not to also do something else. Let us say, the method would also send the retrieved number to some database. In that case it should be called something in the likes of `get_total_number_of_employees_and_send_to_db()`.

Of course, your priorities when developing should be to get it working first, then you can refactor and further apply the principles.

Another point to consider is code style (like linting etc.) which should be uniform over the whole project. Since this is different for each language, we will touch on in [Chapter 6: Language Specific Stuff](#6-language-specific-stuff). Some things, like _PEP-8_-linting are automatically enabled in JetBrains IDEs.

### 6. Language Specific Stuff
- JavaScript:
    - code styling: Eslint (link to [openease-eslint-config](https://www.npmjs.com/package/@code-iai/eslint-config-openease))
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: jest
    - external tools: Snyk, Greenkeeper
    - NodeJs stuff: version etc.
    - registry: npm, link documentation
    - ECMA 2017 & Browserify
- Python:
    - code styling: PEP-8 (auto enabled in JetBrains-IDEs) (& Pylint)
    - semanticRelease & commitizen (provide links)
    - recommended testing framework: pytest (monkeypatch)
    - Version: 2.7 due to ros -> No support from 2020 on...
    (- pipenv?)
- Docker
- Codacy

    _We are working to extend this part of the documentation, so please be patient for the update on language specific recommendations :)_  
