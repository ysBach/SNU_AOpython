(00:Prepare_Python)=

# (IMPORTANT) Python Environment

For these lecture notes, you need some packages to run the codes.

```{warning}
The intention of this note is for completeness and reference for students when they become researchers. **I have not intended** to let students go through all these installation steps during class time. Please let TA setup/install everything beforehand.

Depending on your OS and environment, some parts will result in errors. **You should take full responsibility for that**, unless you use the computers' set up by the TA.
```


```{admonition} For Windows users
If you want to use Windows, you have to sacrifice ``fitsio`` (unfortunately, their installation assumes ``gcc``). Erase the installation of ``fitsio`` from the codes below.
```

If you are familiar with conda and virtual environments, please feel free to create a new environment for this lecture note. If you don't understand what this means, please ignore it (you will naturally know what it is after a few years of using conda and having numerous headaches and googling experiences).

```{note}
If you are sick of the slow speed of ``conda`` and adventurous, give [``mamba``](https://github.com/mamba-org/mamba) a try.
```

## Installation Script

Copy and paste the following lines to the terminal (I am assuming you have Anaconda installed, following [00-1_Softwares.md](00-1_Softwares.md)):

```
conda create -n snuao python=3.10 numpy=1.24 scipy=1.10 astropy=5.2 pandas=1 jupyter=1 sep=1.2 ccdproc=2.4 photutils=1.6 specutils=1.10 -c conda-forge -y
conda activate snuao
conda install -c conda-forge fitsio -y  # Windows will raise error here; ignore this line if it does.
```

While it is going on, clone the AOclass repo Download ZIP or

```
git clone --recursive https://github.com/ysBach/SNU_AOclass.git
# Or gh repo clone ysBach/SNU_AOclass

cd SNU_AOclass # Go to the SNU_AOclass directory.
git submodule update --init --recursive
```

**This may take several minutes.** Be patient, grab a cup of coffee, and read the "Reading Materials" section below.

***AFTER the above is finished***, install the submodules:

```
# Go to the SNU_AOclass directory.
cd submodules/
conda activate snuao
cd version_information && pip install -e . && cd ..
cd astroquery && pip install -e . && cd ..
cd ginga && pip install -e . && cd ..
cd astroalign && pip install -e . && cd ..
cd astroscrappy && pip install -e . && cd ..
```

(basically, any version of these may not break any part of the notebooks, hopefully...)

Next,
```
pip install astro-ndslice==0.2 ysfitsutilpy==0.2 ysphotutilpy==0.1.1 --no-deps
```

Finally, download [_tool_visualization.py](https://github.com/ysBach/SNU_AOpython/blob/main/chaps/_tool_visualization.py) and put it in **the directory where you will run your python files/notebooks**.


### Reading Materials

Some things to read during the installation:

**Astropy**
: The name of a project that devotes its human power to developing a *single* package containing tools useful for astronomers in Python language ([GitHub](https://github.com/astropy/astropy/wiki), [Official website](http://www.astropy.org/), [The most recent stable distribution documentation](http://docs.astropy.org/en/stable/)).

**Affiliated Packages**
: Since astropy is a "single core" package, it doesn't have many convenience functionalities for small, specific fields of astronomy. So there are some affiliated packages that help astronomers fulfill their needs. You may find a list of them [here](http://www.astropy.org/affiliated/).

```{note}
Astropy (but not affiliated packages) must have been installed on your computer by default while installing Anaconda.
```

Among the Astropy-affiliated packages, these will be heavily used:

[photutils](http://photutils.readthedocs.io/en/stable/)
: Photometry-related functionalities

[ccdproc](http://ccdproc.readthedocs.io/en/stable/)
: CCD data manipulation

[astroscrappy](https://github.com/astropy/astroscrappy)
: The cosmic ray rejection tool, which uses L.A.Cosmic algorithm ([van Dokkum 2001, PASP](http://www.astro.yale.edu/dokkum/lacosmic/)).

[APLPy](https://aplpy.github.io/)
: Astronomical image displaying tool (you *may* use it in the future...?).

[astroquery](https://astroquery.readthedocs.io/en/latest/)
: Querying astronomical catalog data: unlike most other packages, it is recommended to use the "developer's version" of astroquery rather than the stable version of it. This is why we install astroquery with ``git clone`` rather than ``conda install``.

[``fitsio``](https://github.com/esheldon/fitsio.git)
: The popular ``cfitsio`` in python.

```{note}
``fitsio`` is used (optionally) within ``ysfitsutilpy`` to boost the FITS I/O speed (factor of ~ 30x on macOS) when header is unnecessary.
```

## Check if Installed Correctly

Some packages have test module. Please run these to check if the installation worked correctly.

````{admonition} testing
:class: dropdown, tip

You can simply test the installation by running tests. Run an ipython or Jupyter notebook/lab, and type

``` python
>>> import astropy, photutils
>>> photutils.test()
>>> astropy.test()
```

These will take quite a while, especially astropy takes a very long time. (So I didn't show you the full result)

You need to do it only once when you first install these packages. If you want to test only some part of the whole package, you can specify the module, e.g., you can test `astropy.io.fits` by:

```python
>>> astropy.test(package='io.fits')
```

While the test is going on, look at the names of the directories, like `astropy/table`, `astropy/units`, etc. These are the names we will encounter very frequently, so this test is not only to **test** but also to get accustomed to the astropy and Python language.

Each dot(`.`) means `test passed` and `x` means `test failed`. But some of the failures are just OK. `s` means it is skipped for some reason.

An example test for **Astropy 1.3.1 and Photutils 0.3.1** (took ~ 10 mins):

```bash
(long long test explanations....)
======================== 1056 passed, 2 skipped, 2 xfailed in 82.18 seconds ========================
(long long test explanations....)
Some tests are known to fail when run from the IPython prompt, especially, but not limited to, those involving logging and warning handling.  Unless you are certain as to the cause of the failure, please check that the failure occurs outside IPython as well.  See http://docs.astropy.org/en/stable/known_issues.html#failing-logging-tests-when-running-the-tests-in-ipython for more information.
== 24 failed, 8717 passed, 75 skipped, 42 xfailed, 1 xpassed, 2 pytest-warnings in 573.02 seconds ==
```
\

The astropy will do the tests automatically (it takes ~ 10 minutes). There might be some errors, but usually they are not important, so you can ignore them. If "`astropy.test()`" itself does not work, please check whether the installation of Anaconda was done correctly.

````


## Troubleshoot

If you have any trouble installing the abovementioned packages, that's a big problem. Please immediately contact the TA or open an issue at this repo.
