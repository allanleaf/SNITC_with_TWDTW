# Algorithm SNITC to segmentation image

[//]: # (A primeira coisa a setar são as bibliotecas necessárias para o projeto. É necessário lembrar que esse projeto foi )

[//]: # (totalmente desenvolvido no Google Colab, desta forma os caminhos de arquivos e algumas bibliotecas estarão )

[//]: # (setadas para esse ambiente:)

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

[//]: # (Então as importações:)

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

[//]: # (Nesta adaptação do SNITC, foi feita uma alteração para que ambas as formas de cálculo de distância fossem funcionais,)

[//]: # (desta forma, foram introduzidos os parâmetros de entrada **distance_calculation** e **weight_twdtw** na chamada da )

[//]: # (função SNITC. Para melhor comprenssão do funcionamento geral dos parâmetros de entrada do SNITC é altamente recomendada)

[//]: # (a leitura do [artigo]&#40;https://ieeexplore.ieee.org/document/9258957&#41; de Soares et al. 2020.)

[//]: # ()
[//]: # (Onde **distance_calculation** admite "dtw" ou "twdtw" e **weight_twdtw** admite "linear" ou "logistic". Para melhor )

[//]: # (compreensão do funcionamento das opções do parâmetro **weight_twdtw**, é altamente recomendada a leitura do)

[//]: # ([artigo]&#40;https://www.jstatsoft.org/article/view/v088i05&#41; de Maus et al. 2019.)

In this adaptation of the SNITC, a change was made so that both forms of distance calculation were functional,
in this way, the input parameters **distance_calculation** and **weight_twdtw** were introduced in the call of the
SNITC function. For a better understanding of the general functioning of the SNITC input parameters it is highly recommended
reading [article](https://ieeexplore.ieee.org/document/9258957) by Soares et al. 2020.

Where **distance_calculation** admits "dtw" or "twdtw" and **weight_twdtw** admits "linear" or "logistic". For the best
understanding how the **weight_twdtw** parameter options work, it is highly recommended to read the
[article](https://www.jstatsoft.org/article/view/v088i05) by Maus et al. 2019