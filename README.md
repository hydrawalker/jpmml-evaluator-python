JPMML-Evaluator-Python
======================

PMML evaluator library for Python.

# Features #

This package provides Python wrapper classes and functions for the [JPMML-Evaluator](https://github.com/jpmml/jpmml-evaluator) library.

# Prerequisites #

* Java Platform, Standard Edition 8 or newer.
* Python 2.7, 3.4 or newer.

# Installation #

Install the latest version from GitHub:
```
pip install --user --upgrade git+https://github.com/jpmml/jpmml-evaluator-python.git
```

# Usage #

### Java-to-Python API mapping ###

Guiding principles:

1. Java package prefix `org.jpmml.evaluator` becomes Python package prefix `jpmml_evaluator`.
2. Java classes and interfaces become Python classes with the same name.
3. Java methods become Python methods with the same name. In case of method overloading, the names of Python methods may have a disambiguating suffix (eg. `loadFile`, `loadInputStream`) appended to them.
4. Java parameter types become Python parameter types.

For example, the Java method `org.jpmml.evaluator.Evaluator#evaluate(Map<FieldName, ?> arguments)` has become a Python method `jpmml_evaluator.Evaluator.evaluate(arguments: dict)`.

### Workflow ###

Launching a Py4J gateway:

```python
from jpmml_evaluator.py4j import launch_gateway, Py4JBackend

gateway = launch_gateway()

backend = Py4JBackend(gateway)
```

Building a verified model evaluator from a PMML file:

```python
from jpmml_evaluator import Evaluator, LoadingModelEvaluatorBuilder

evaluatorBuilder = LoadingModelEvaluatorBuilder(backend) \
	.setLocatable(True) \
	.loadFile("DecisionTreeIris.pmml")

evaluator = evaluatorBuilder.build() \
	.verify()
```

Printing model schema:

```python
inputFields = evaluator.getInputFields()
print("Input fields: " + str([inputField.getName() for inputField in inputFields]))

targetFields = evaluator.getTargetFields()
print("Target field(s): " + str([targetField.getName() for targetField in targetFields]))

outputFields = evaluator.getOutputFields()
print("Output fields: " + str([outputField.getName() for outputField in outputFields]))
```

Evaluating a single data record:

```python
arguments = {
	"Sepal_Length" : 5.1,
	"Sepal_Width" : 3.5,
	"Petal_Length" : 1.4,
	"Petal_Width" : 0.2
}

results = evaluator.evaluate(arguments)
print(results)
```

Evaluating a collection of data records:

```python
import pandas

arguments_df = pandas.read_csv("Iris.csv", sep = ",")

results_df = evaluator.evaluateAll(arguments_df)
print(results_df)
```

Shutting down the Py4J gateway:

```python
gateway.shutdown()
```

# License #

JPMML-Evaluator-Python is dual-licensed under the [GNU Affero General Public License (AGPL) version 3.0](https://www.gnu.org/licenses/agpl-3.0.html), and a commercial license.

# Additional information #

JPMML-Evaluator-Python is developed and maintained by Openscoring Ltd, Estonia.

Interested in using JPMML software in your application? Please contact [info@openscoring.io](mailto:info@openscoring.io)
