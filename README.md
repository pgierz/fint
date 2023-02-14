# fint
Easy interpolation of [fesom2](https://github.com/FESOM/fesom2) output data. Your data should be created with `refactoring` brunch of the model.

## Motivation

Make something that is more light weight to install compared to [pyfesom2](https://github.com/FESOM/pyfesom2) that has similar capabilities, but sometimes could be pain to install.

## Installation

On most HPC machines you should be able to select python module, that have `xarray` and preferably `cartopy` installed, clone repository, and just use it simply as a script (e.g. `python /path/to/fint/fint/fint.py [some options]`). But if you want to install it in a bit more cleaner way, read instructions below.

### Dependencies

- xarray
- netcdf4
- numpy
- scipy
- matplotlib
- pandas
#### Optional
- cartopy (should work without it as well, but ony interpolate to lan lat boxes)
- shapely (also should work without it)

## Miniconda installation

Please follow [those instructions](https://github.com/koldunovn/python_for_geosciences#getting-started-for-linuxmac) to install Miniconda.

Change to `fint` directory and create separate `fint` environment with:

```shell
conda env create -f environment.yml
```

activate it after installation with

```shell
conda activate fint
```

and you should be good to go :)


## Basic functionality

The easiest way to test `fint` is to execute `tests.sh` script, that will download sample data and perform some tests. Let's assume you executed the script once, and everything worked. We now can repeat some of the commands from there with explanations.

We set some variables, that will be frequently used as command line arguments:
```
export FILE="./test/data/temp.fesom.1948.nc"
export MESH="./test/mesh/pi/"
export INFL="--influence 500000"
```

As bare minimum you should provide path to FESOM2 output file and path to the mesh. We also use `--influence`, so that output result looks a bit nicer:
```shell
python fint.py ${FILE} ${MESH} ${INFL}
```

## Usage

Currently it's just a list of help from `fint`. Later will expand each of the options with explination.

```shell
usage: pfinterp [-h] [--depths DEPTHS] [--timesteps TIMESTEPS] [--box BOX]
                [--res N_POINTS_LON N_POINTS_LAT] [--influence INFLUENCE]
                [--map_projection MAP_PROJECTION] [--interp {nn,mtri_linear,linear_scipy}]
                [--mask MASK] [--ofile OFILE] [--odir ODIR] [--target TARGET] [--no_shape_mask]
                [--rotate] [--no_mask_zero] [--timedelta TIMEDELTA]
                data meshpath

Interpolates FESOM2 data to regular grid.

positional arguments:
  data                  Path to the FESOM2 output data file.
  meshpath              Path to the FESOM2 mesh folder.

optional arguments:
  -h, --help            show this help message and exit
  --depths DEPTHS, -d DEPTHS
                        Depths in meters. Closest values from model levels will be taken. Several
                        options available: number - e.g. '100', coma separated list - e.g.
                        '0,10,100,200', slice 0:100 -1 - all levels will be selected.
  --timesteps TIMESTEPS, -t TIMESTEPS
                        Explicitly define timesteps of the input fields. There are several options:
                        '-1' - all time steps, number - one time step (e.g. '5'), numbers - coma
                        separated (e.g. '0, 3, 8, 10'), slice - e.g. '5:10', slice with steps - e.g.
                        '8:120:12'. slice untill the end of the time series - e.g. '8:end:12'.
  --box BOX, -b BOX     Several options are available: - Map boundaries in -180 180 -90 90 format that
                        will be used for interpolation. - Use one of the predefined regions. Available:
                        gs (Golf Stream), trop (Atlantic Tropics), arctic, gulf (also Golf Stream, but
                        based on Mercator projection.)\))
  --res N_POINTS_LON N_POINTS_LAT, -r N_POINTS_LON N_POINTS_LAT
                        Number of points along each axis that will be used for interpolation (for lon
                        and lat).
  --influence INFLUENCE, -i INFLUENCE
                        Radius of influence for interpolation, in meters. Used for nearest neighbor
                        interpolation.
  --map_projection MAP_PROJECTION, -m MAP_PROJECTION
                        Map projection. Available: mer, np
  --interp {nn,mtri_linear,linear_scipy}
                        Interpolation method. Options are nn - nearest neighbor (KDTree implementation,
                        fast), mtri_linear - linear, based on triangulation information (slow, but more
                        precise)
  --mask MASK           File with mask for interpolation. Mask should have the same coordinates as
                        interpolated data. Usuall usecase is to use mtri_linear slow interpolation to
                        create the mask, and then use this mask for faster (nn) interpolation.
  --ofile OFILE, -o OFILE
                        Path to the output file. Default is out.nc.
  --odir ODIR           Path to the output directory. Default is ./
  --target TARGET       Path to the target file, that contains coordinates of the target grid (as lon
                        lat variables)
  --no_shape_mask       Do not apply shapely mask for coastlines. Useful for paleo applications.
  --rotate              Rotate vector variables to geographic coordinates. Use standard FESOM2 angles
                        (this should be fine in 99.99 percent of cases:)).
  --no_mask_zero        FESOM2 use 0 as mask value, which is terrible for some variables. Solution is
                        to create a mask using temperature or salinity, and then use this mask for
                        interpolation, applying this option.
  --timedelta TIMEDELTA
                        Add timedelta to the time axis. The format is number followed by unit. E.g.
                        '1D' or '10h'. Valid units are 'D' (days), 'h' (hours), 'm' (minutes), 's'
                        (seconds). To substract timedelta, put argument in quotes, and prepend ' -', so
                        SPACE and then -, e.g. ' -10D'.
```
