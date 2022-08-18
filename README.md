# Algorithm SNITC to segmentation image

The first thing to set is the libraries needed for the project. It is necessary to remember that this project was
fully developed in Google Colab, this way the file paths and some libraries will be
set for this environment:

```bash
!pip install rasterio
!pip install geopandas
!pip install stmetrics
!pip install -vvv --upgrade --force-reinstall --no-deps --no-binary dtaidistance dtaidistance
!pip install connected-components-3d
!pip install fastremap
!pip install --upgrade tbb
```

Then imports:

```bash
import numpy
import xarray
import rasterio
from numba import njit, prange
import stmetrics
from dtaidistance import dtw
import pandas as pd
from geopandas import GeoDataFrame
from google.colab import drive
drive.mount('/content/drive')
import cc3d
import fastremap
from pyproj import CRS
from sklearn.metrics.pairwise import euclidean_distances
from math import exp
```

In this adaptation of the SNITC, a change was made so that both forms of distance calculation were functional,
in this way, the input parameters **distance_calculation** and **weight_twdtw** were introduced in the call of the
SNITC function. For a better understanding of the general functioning of the SNITC input parameters it is highly recommended
reading [article](https://ieeexplore.ieee.org/document/9258957) by Soares et al. 2020.

Where **distance_calculation** admits "dtw" or "twdtw" and **weight_twdtw** admits "linear" or "logistic". For the best
understanding how the **weight_twdtw** parameter options work, it is highly recommended to read the
[article](https://www.jstatsoft.org/article/view/v088i05) by Maus et al. 2019

In the SNITC algorithm, preference is originally given to the distance calculation performed by the function distance_fast, 
this happens to greatly optimize the processing of the algorithm. In this sense, it will be possible to notice that
the TWDTW adaptation is located only in the distance_fast function, because if there is no error in setting the input variables, 
it will always be executed.
