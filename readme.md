# Titanic package hackathon

## Use case

We have received an initial implementation of a machine learning model from one of our data scientists. They have asked us to convert their code into a well-documented and well-tested Python package, that can easily be distributed within the company.

Their full list of requests include:

* Converting this code base to a Semver versioned Python package.
* Adding (HTML) documentation that can be published online.
* Introducing tests to verify that the code works correctly.
* Creating a command line interface for training/evaluating/predicting.
* Implementing a Flask REST API that exposes a trained model and returns predictions.

Besides this, they would also like to have an easy way of running a linter (pylint) and code formatter (black) on their code. Ideally, this would also run as part of a CI/CD pipeline, as well as being easy to run locally.

## Exercises

### 1. Creating a Python package

As a first step, let's start by creating a Python package. Typically this includes creating a setup.py file, which contains metadata on your package, telling Python how it should be installed. For an idea on how to start, look at the Python packaging documentation: https://packaging.python.org/tutorials/packaging-projects/.

Steps:
* Create a setup.py file that allows you to install your package. Ensure the setup.py file contains a proper package name, version and other metadata such as author name, dependencies, etc. *Alternatively, try using a pyproject.toml file (see bonus below).*
* Verify that you can install your package using pip and that you can import and run code from your package using the Python REPL.
* Build a source/wheel distribution of your package (which you could use for re-distributing your package).

Bonus:
* Try building your package using a [pyproject.toml](https://martin-thoma.com/pyproject-toml/) file instead of using setup.py.
* Split your dependencies into main dependencies and dev dependencies, which you only need for development (if you have any).

*References*
* https://manikos.github.io/a-tour-on-python-packaging
* https://www.bernat.tech/pep-517-and-python-packaging
* Interesting discussion about using src directories:
    * https://hynek.me/articles/testing-packaging/
    * https://github.com/pypa/packaging.python.org/issues/320

### 2. Setting up linting and code formatting

Now we have a basic package, it's a good idea to start enforcing some practices for maintaining good code quality.

Steps:
* Install pylint and see if you can run it on your code. Does it flag any issues in the code? If so, see if you can fix these issues. Alternatively, if you don't agree with pylint, see if you can disable certain warnings using a pylintrc file.
* Install and run black on your code. Does black make any formatting changes to your code? Play with some examples and see what black does to make your code nicer (hopefully).
* Create a Makefile with basic commands such as *pylint* and *black* which run pylint/black on your code base. This should allow the DS to easily run these checks using a command such as `make pylint`.

Bonus:
* Install and setup pre-commit to run pylint/black as [pre-commit](https://pre-commit.com/) hooks that run whenever you try to create a new git commit.
* Add typehinting to your code base and check if you can verify whether your code is correct (at least as far as types are concerned) using a tool such as mypy.

### 3. Adding documentation using Sphinx

Next, we want to add some documentation to our package using Sphinx. At the very least, this documentation should include the following:

* A general introduction page, describing the intent of the package/model.
* An installation page, describing how to install our package.
* A usage page, describing showing an example of how to fit a model.
* An API page describing the different classes in our package.

Steps:
* Install Sphinx using pip.
* Create an initial skeleton for your documentation using `sphinx-quickstart`.
* Generate an HTML version of the documentation by building it.
* Add the different pages listed above to your documentation skeleton. For adding descriptions of our classes/functions, have a look at the autodoc extension: https://www.sphinx-doc.org/en/master/usage/extensions/autodoc.html. Check the results of your changes by rebuilding the docs.
* Enable the [napoleon](https://www.sphinx-doc.org/en/master/usage/extensions/napoleon.html) extension to make sure your docstrings are parsed correctly.

Bonus:
* Generate the API page automatically using sphinx-apidoc.
* Add a *docs* command to your Makefile for building your docs.
* Create prettier docs by switching to the [ReadTheDocs](https://github.com/readthedocs/sphinx_rtd_theme) template.

### 4. Introducing tests using pytest

Now we have some documentation, we should probably also start implementing some tests for our code.

Steps:
* Install pytest and create a *tests/titanic* directory, in which we will start writing our tests.
* Create a *test_features.py* file and implement some tests for our *ColumnSelector* class. See if you can use fixtures for sharing test data across tests.

Bonus:
* Design your test data fixtures to load data from external test files, rather than defining your test data inline.
* Add a *test* command to your Makefile for running your tests.
* Create tests for the model class.

### 5. Creating a command line interface using click

To increase the usability of our package, we would like to implement the following command line scripts for our package:

* Train - trains our model on a given dataset, producing a model pkl file.
* Evaluate - loads a trained model from a pkl file and evaluates the model on a validation dataset.
* Predict - loads a trained model and produces predictions for a given prediction dataset.

Steps:
* Create a *cli* module in the *titanic* package, which will contain entrypoints for our command line interface.
* Define *train*, *evaluate* and *predict* functions in this module and use [click](https://click.palletsprojects.com/en/7.x/) to convert these functions into a command line interface. Tip: use command groups to group the three commands.
* Use entrypoints in setup.py (https://chriswarrick.com/blog/2014/09/15/python-apps-the-right-way-entry_points-and-scripts/) to automatically create a command line app when your package is installed.
* Reinstall your package after adding the entrypoints and see if you can run your command line application.

### 6. Implementing a REST API using Flask

As a final step, we would like to add a small REST-based API for serving our model. The idea is that this REST API includes a `/predict` endpoint, which should accept a JSON dataset payload and return a JSON payload containing predictions.

Steps:
* Add an app module in your Python package that implements a [Flask](https://flask.palletsprojects.com/en/1.1.x/) API with a `ping` endpoint, which simply returns 'pong' when called. You can find a small example of how to get started here: https://realpython.com/flask-by-example-part-1-project-setup/.
* Test your dummy API by calling the `ping` endpoint using your browser (or the `requests` Python library).
* Implement the prediction endpoint, which takes a JSON set and returns predictions. Note that the prediction endpoint needs to be able to load a trained model (from a pkl file) to be able to do predictions.

Bonus:
* Add a *serve* CLI command to start your Flask application from the command line.

### Bonus: Set up CI/CD

Ideally, we would want to run our CI/CD steps (black, pylint, pytest, building a wheel, etc.) as part of an automated build/release pipeline. Try to automate this process by building a CI/CD pipeline using your favorite platform (e.g. Github using Github actions or Azure DevOps using Azure Build pipelines).

### Bonus: Containerize using docker

To make it even easier to run our model, it would be nice to containerize the entire package/application in docker. Try to create a Docker image for our application and see if you can use this image to train and serve a model (or produce predictions using the CLI).

Bonus: think about what you should include in your docker container. For example, should the container only contain your model/application code, or should the container represent a trained model? (Which means it should also include the pickle of the trained model.) Both approaches are possible and have their own advantages/drawbacks. Which do you prefer?
