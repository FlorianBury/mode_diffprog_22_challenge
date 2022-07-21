# Data challenge for the 2nd MODE workshop on differentiable programming

## [Crete, 12th-16th of September 2022](https://indico.cern.ch/event/1145124/)

This repo is meant to provide a start ing point for getting up and running with the challenge, and to provide information on the rules et cetera.

For a full introduction to the challenge, please read the challenge introduction slides. To summarise:

- The challenge focusses on imaging with muon tomography
- Your task is to take 3D predictions for the radiation-length of cosmic muons in voxelised volumes, and convert them to a 3D map of the locations of stone walls buried underground: a voxelwise classification of the voxel material, 0 = soil, 1 = wall.
- ~100,000 labelled examples are provided, and submissions must be made on ~30,000 unlabelled samples
- Submissions must be uploaded [here](https://cernbox.cern.ch/index.php/s/ylsOYg9q7hcRk4l) before 23:59:59 CEST on 22/09/04
    - Submissions must be in HDF5 format in a file name following the pattern `YOUR_NAME_IDXX.h5`
    - `IDXX` is an optional ID number created by you to allow for multiple submissions
    - At the end of the competition, final scores will be evaluated using each participants' predictions with the highest ID number
    - Additionally, every Friday before the end, the current scores on a subsample of predictions will be announced.

The dataset is available for download from [here](https://doi.org/10.5281/zenodo.6866890).
And can be downloaded remotely using, e.g.:

```bash
mkdir data
wget -O data/train.h5 https://zenodo.org/record/6866891/files/train.h5
wget -O data/test.h5 https://zenodo.org/record/6866891/files/test.h5    
```

## Data format

Data is in HDF5 format (with LZF compression). The training file contains two datasets:

- `'x0'`: a set of voxelwise X0 predictions (float32)
- `'targs'`: a set of voxelwise classes (int):
    - 0 = soil
    - 1 = wall

The format of the datasets is a rank-4 array, with dimensions corresponding to (samples, z position, x position, y position).
All passive volumes are of the same size: 10x10x10 m, with cubic voxels of size 1x1x1 m, i.e. every passive volume contains 1000 voxels.
The arrays are ordered such that zeroth z layer is the bottom layer of the passive volume, and the ninth layer is the top layer.

It can be read using e.g. the code below:

```python
with open('train.h5') as h5:
    inputs = h5['x0'][()]
    targets = h5['targs'][()]
```

The test file only contains the X0 inputs:

```python
with open('test.h5') as h5:
    inputs = h5['x0'][()]
```

Submission files can be created using e.g.:

```python
with h5py.File('MY_NAME_ID00_test_preds.h5', 'w') as h5:
    h5.create_dataset("preds", data=class_preds.astype("int"), dtype="int", compression='lzf')
```

where `class_preds` is a Numpy array of shape (30036, 10, 10, 10) filled with integer values: 0 for soil voxels, and 1 for wall voxels

For more information HDF5 in python, please refer to the [h5py documentation](https://docs.h5py.org/en/stable/)

## Starter notebook

The example notebook (threshold_demo.ipynb) demonstrates how to access and view the data, and how to apply a simple thresholding approach to convert the X0 values into class predictions.

It requires Jupyter to be installed in your Python environment (e.g. by `pip install jupyter`), and then running `jupyter notebook` will allow you to navigate to the notebook and run it.

## Licence and reuse

threshold_demo.ipynb is licenced under [AGPL 3.0](https://github.com/GilesStrong/mode_diffprog_22_challenge/blob/main/LICENSE), Copyright (C) 2022 Giles C Strong.

Except where indicated, challenge_introduction.pdf is licenced under [CC-BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/), Copyright (C) 2022 Giles C Strong.

For the dataset usage and licence, please see it's [Zenodo entry](https://doi.org/10.5281/zenodo.6866890). It was produced by Giles C. Strong, Tommaso Dorigo, Andrea Giammanco, Pietro Vischia, Jan Kieseler, Maxime Lagrange, Federico Nardi, Haitham Zaraket, Max Lamparth, Federica Fanzago, Oleg Savchenko, and Anna Bordignon.


## Citation

Please cite this software as:

Please cite the dataset as:

```bibtex
@dataset{strong_giles_chatham_2022_6866891,
  author       = {Strong, Giles Chatham and
                  Dorigo, Tommaso and
                  Giammanco, Andrea and
                  Vischia, Pietro and
                  Kieseler, Jan and
                  Lagrange, Maxime and
                  Nardi, Federico and
                  Zaraket, Haitham and
                  Lamparth, Max and
                  Fanzango, Federica and
                  Savchenko, Oleg and
                  Bordignon, Anna},
  title        = {{Dataset for the challenge at the 2nd MODE workshop 
                   on differentiable programming 2022}},
  month        = jul,
  year         = 2022,
  publisher    = {Zenodo},
  version      = {1.0.0},
  doi          = {10.5281/zenodo.6866891},
  url          = {https://doi.org/10.5281/zenodo.6866891}
}
```
