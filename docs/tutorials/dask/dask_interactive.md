<!--
 Copyright 2021 dciangot
 
 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.
 You may obtain a copy of the License at
 
     http://www.apache.org/licenses/LICENSE-2.0
 
 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
-->
# :material-chart-scatter-plot: Use Dask interactively as a backend

Dask can be used as a backend for the distributed execution of ROOT's [RDataFrame](https://root.cern/doc/master/classROOT_1_1RDataFrame.html) computations. More specifically, ROOT RDataFrame supports distributed execution via the ```ROOT.RDF.Experimental.Distributed``` module.

## :gear: Set up
In a notebook, once a Dask client has been deployed and a Dask client instatiated as described [here](./dask_init), you can start with:

```python
from dask.distributed import Client

client = Client(<Dask scheduler address)
```

> In alternative, you can drag and drop the Cluster block on the left panel into the notebook. That will create the cell above automatically with the correct values already compiled.

## Using RDataframe

If you want to use DASK to distribute RDataFrame payloads, you need now to declare a distributed DataFrame, passing all the necessary information

```python
import ROOT

df = ROOT.RDF.Experimental.Distributed.Dask.RDataFrame(
    <name of the tree>, 
    chain, 
    npartitions=<maximum number of partitions (i.e. Dask tasks)>, 
    daskclient=client,
)
```

In most cases you would like to declare custom functions to the ROOT interpreter in order to perform specific calculations on data. To do this, you need to use the ```ROOT.RDF.Experimental.Distributed.initialize``` function to initialize each worker.

```python
text_file = open("postselection.h", "r")
data = text_file.read()

def my_initialization_function():
    ROOT.gInterpreter.Declare('{}'.format(data))

ROOT.RDF.Experimental.Distributed.initialize(my_initialization_function)
```

now you are ready to book all the computations you need to do on the dataframe using RDataFrame methods.
Once all the operations are booked, trigger the distributed execution by doing some actions on booked items, as you would do using RDataFrame locally. Results can be accessed in the same way as local RDataFrame, too.

## Using Coffea
TODO