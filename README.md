## Amazon Braket Python Schemas

Amazon Braket Python Schemas is an open source library that contains the schemas for Braket, including:
* intermediate representations (IR) for Amazon Braket quantum tasks and offers serialization and deserialization of those IR payloads. Think of the IR as the contract between the Amazon Braket SDK and Amazon Braket API for quantum programs.
* schemas for the S3 results of each quantum task
* schemas for the device capabilities of each device

## Installation

### Prerequisites
- Python 3.7+

### Steps

The preferred way to get Amazon Braket Python Schemas is by installing the [Amazon Braket Python SDK](https://github.com/aws/amazon-braket-sdk-python), which will pull in the schemas.
Follow the instructions in the [README](https://github.com/aws/amazon-braket-sdk-python/blob/main/README.md) for setup.

However, if you only want to use the schemas, it can be installed on its own as follows:

```shell
pip install amazon-braket-schemas
```

You can install from source by cloning this repository and running a pip install command in the root directory of the repository:

```shell
git clone https://github.com/aws/amazon-braket-schemas-python.git
cd amazon-braket-schemas-python
pip install .
```

You can check your currently installed version of `amazon-braket-schemas` with `pip show`:

```shell
pip show amazon-braket-schemas
```

or alternatively from within Python:

```
>>> import braket._schemas as braket_schemas
>>> braket_schemas.__version__
```

## Usage
There are currently two types of IR, including jacqd (JsonAwsQuantumCircuitDescription) and annealing. See below for their usage.

**Serializing python structures**
```python
from braket.ir.jaqcd import CNot, H, Program, Expectation
from braket.ir.annealing import Problem, ProblemType

program = Program(instructions=[H(target=0), CNot(control=0, target=1)])
print(program.json(indent=2))

"""
{
  "braketSchemaHeader": {
    "name": "braket.ir.jaqcd.program",
    "version": "1"
  },
  "instructions": [
    {
      "target": 0,
      "type": "h"
    },
    {
      "control": 0,
      "target": 1,
      "type": "cnot"
    }
  ],
  "results": null,
  "basis_rotation_instructions": null,
}
"""

program = Program(
    instructions=[H(target=0), CNot(control=0, target=1)],
    results=[Expectation(targets=[0], observable=['x'])],
    basis_rotation_instructions=[H(target=0)]
)
print(program.json(indent=2))

"""
{
  "braketSchemaHeader": {
    "name": "braket.ir.jaqcd.program",
    "version": "1"
  },
  "instructions": [
    {
      "target": 0,
      "type": "h"
    },
    {
      "control": 0,
      "target": 1,
      "type": "cnot"
    }
  ],
  "results": [
    {
      "observable": [
        "x"
      ],
      "targets": [
        0
      ],
      "type": "expectation"
    }
  ],
  "basis_rotation_instructions": [
    {
      "target": 0,
      "type": "h"
    }
  ]
}
"""

problem = Problem(type=ProblemType.QUBO, linear={0: 0.3, 4: -0.3}, quadratic={"0,5": 0.667})
print(problem.json(indent=2))

"""
{
  "braketSchemaHeader": {
    "name": "braket.ir.annealing.problem",
    "version": "1"
  },
  "type": "QUBO",
  "linear": {0: 0.3, 4: -0.3},
  "quadratic": {"0,5": 0.667}
}
"""
```

**Deserializing into python structures**
```python
from braket.ir.jaqcd import Program
from braket.ir.annealing import Problem

jaqcd_string = """
{
  "instructions": [
    {
      "target": 0,
      "type": "h"
    },
    {
      "control": 0,
      "target": 1,
      "type": "cnot"
    }
  ],
  "results": [
    {
      "observable": [
        "x"
      ],
      "targets": [
        0
      ],
      "type": "expectation"
    }
  ],
  "basis_rotation_instructions": [
    {
      "target": 0,
      "type": "h"
    }
  ]
}
"""

program = Program.parse_raw(jaqcd_string)
print(program)

"""
braketSchemaHeader=BraketSchemaHeader(name='braket.ir.jaqcd.program', version='1') instructions=[H(target=0, type=<Type.h: 'h'>), CNot(control=0, target=1, type=<Type.cnot: 'cnot'>)] results=[Expectation(observable=['x'], targets=[0], type=<Type.expectation: 'expectation'>)] basis_rotation_instructions=[H(target=0, type=<Type.h: 'h'>)]
"""

annealing_string = """
{
  "type": "QUBO",
  "linear": {0: 0.3, 4: -0.3},
  "quadratic": {"0,5": 0.667}
}
"""

problem = Problem.parse_raw(annealing_string)
print(problem)

"""
braketSchemaHeader=BraketSchemaHeader(name='braket.ir.annealing.problem', version='1') type=<ProblemType.QUBO: 'QUBO'>, linear={0: 0.3, 4: -0.3}, quadratic={'0,5': 0.667}
"""

```

## Documentation

First, install tox:

```shell
pip install tox
```

To build the Sphinx docs, run the following command in the root repo directory:

```shell
tox -e docs
```

You can then find the generated HTML files in `build/documentation/html`.

## Testing

Make sure to install test dependencies first:
```shell
pip install -e "amazon-braket-schemas-python[test]"
```

To run the unit tests, linters, and doc generation:
```shell
tox
```

To select tests based on a keyword:
```shell
tox -e py37 -- -k 'your_test'
```

## License

This project is licensed under the Apache-2.0 License.
