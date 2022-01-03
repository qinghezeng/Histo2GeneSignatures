Install Conda Environment for CLAM and Classic MIL
===========

Create conda environment
```shell
conda env create -n clam -f docs/clam.yml
```
It will fail with `CondaEnvException: pip failed` caused by `ERROR: Could not find a version that satisfies the requirement libtiff==0.5.0`.


Some pip dependencies need to be installed manually inside the created environment
```shell
conda activate clam
pip install libtiff openslide_python pyvips gdal mapnik pyproj glymur javabridge -f https://girder.github.io/large_image_wheels
```


Navigate to a path suitable for cloning new repository and install `smooth-topk` (topk==1.0)
```shell
git clone https://github.com/oval-group/smooth-topk.git
cd smooth-topk
python setup.py install
```


Install the rest pip dependencies
```shell
conda env update --name clam -f docs/clam.yml
```shell


***Possible issues and solutions:***
1. If it fails with `Exception: Error finding javahome on linux: ['bash', '-c', 'java -version']`, and give you a blank output when typing `echo $JAVA_HOME`, you will need to configure java
```shell
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get install openjdk-11-jdk
```
Add `export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64` to `~/.bashrc`. Verify `echo $JAVA_HOME` again to ensure that you have configured it correctly. And continue the installation
```shell
source ~/.bashrc # for current user
conda env update --name clam -f docs/clam.yml
```


It should work this time!


2. After finishing the installation, if there is the following when executing `pip check` or other command
`ModuleNotFoundError: No module named '_distutils_hack'`

Update SetupTools with command
```shell
pip install setuptools --upgrade
```

Another error will be thrown out
`ERROR: openslides 3.0 has requirement setuptools<42.0,>=29.0, but you'll have setuptools 60.1.0 which is incompatible.`

Thus reinstall the previous version
```shell
pip install setuptools==41.0.0
```

Now it should output `No broken requirements found.` when executing `pip check` again.

3. For the following segmentation/patching step, if you meet with imcomplete mask <img src="docs/incomplete_mask_AAVV.jpg" width="350px" align="below" /> or dirty stitched image <img src="docs/dirty_stitched_A95S.jpg" width="350px" align="below" />, it is an issue related to pixman 0.38. To solve this, please download `pixman-0.40.0.tar.gz` from [this link](https://www.cairographics.org/releases/). After navigating into the extracted directory (e.g. ~/Downloads/pixman-0.40.0), type `./configure` to configure the package for your system and type `make` to compile the package. Now you can find a file named `libpixman-1.so.0.40.0`. Use the following commands to replace the 0.38 version one in the ~/anaconda/lib folder and ~/anaconda/envs/clam/lib folder.
```shell
sudo cp ~/Downloads/pixman-0.40.0/libpixman-1.so.0.40.0 ~/anaconda/lib/libpixman-1.so.0.38.0
sudo cp ~/Downloads/pixman-0.40.0/libpixman-1.so.0.40.0 ~/anaconda/envs/clam/lib/libpixman-1.so.0.38.0
```shell

Now redo the process with the broken image should give correct results.