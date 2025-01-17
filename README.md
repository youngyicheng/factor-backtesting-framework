git remote rm origin


<img src="https://github.com/nymath/torchquantum/blob/main/src/fig/logo.png" align="right" width="196" />

# torchquantum

TorchQuantum is a backtesting framework that integrates
the structure of PyTorch and WorldQuant's Operator for
efficient quantitative financial analysis.

## Contents

- [Installation](#installation)
- [Features](#features)
- [Contribution](#contribution)

## Installation

Before running examples, you should compile the cython code.

```shell
python setup.py build_ext --inplace
```

Now you can run examples

```shell
python ./examples/main.py
```

## Example

You can easily create an alpha through torchquantum!

```python
import torchqtm.op as op
import torchqtm.op.functional as F
class NeutralizePE(op.Fundamental):
    def __init__(self, env):
        super().__init__(env)
        self.lag = op.Parameter(5, required_optim=False, feasible_region=None)

    def forward(self):
        self.data = F.divide(1, self.env.PE)
        self.data = F.winsorize(self.data, 'std', 4)
        self.data = F.normalize(self.data)
        self.data = F.group_neutralize(self.data, self.env.Sector)
        self.data = F.regression_neut(self.data, self.env.MktVal)
        self.data = F.ts_mean(self.data, self.lag)
        return self.data
```

- `F` is library that contains the operators defined by WorldQuant.
- `op.Fundamental` implies the NeutralizePE belongs to fundamental alpha.
- `self.lag` is the parameter of rolling mean, which can be optimized through grid search.

## Features

- High-speed backtesting framework (most of the operators are implemented through cython)
- A revised gplearn library that is compatible with Alpha mining.
- CNN and other state of the art models for mining alphas.
- Event Driven backtesting framework is available.

## References

[quantopian/alphalens](https://github1s.com/quantopian/alphalens/blob/HEAD/alphalens/performance.py)
[quantopian/zipline](https://github1s.com/quantopian/zipline/blob/HEAD/zipline/performance.py)
