Set up instance for sourmash, and download and install sourmash and dependencies. 

```
sudo apt-get update && \
sudo apt-get -y install screen git curl gcc make g++ python-dev unzip \
        default-jre pkg-config libncurses5-dev r-base-core r-cran-gplots \
        python-matplotlib python-pip python-virtualenv sysstat wget 

```
Install sourmash
```
cd ~
python2.7 -m virtualenv sourmashEnv2
source sourmashEnv2/bin/activate
git clone https://github.com/dib-lab/sourmash.git
cd sourmash
make install
source sourmashEnv2/bin/activate
```

Install khmer from source code
```
cd ~/sourmashEnv2
pip install -U setuptools
git clone https://github.com/dib-lab/khmer.git
cd khmer
make install
```

install numpy
```
deactivate
pip install numpy 
source sourmashEnv2/bin/activate
```
--------------------------------------------

4/19/17 re-installed sourmash to include updates to `sourmash compare` when using `--scaled` parameter
```
rm -rf sourmash
cd sourmashEnv2/bin 
rm sourmash

source sourmashEnv2/bin/activate
pip install https://github.com/dib-lab/sourmash/archive/master.zip
```
------------------------------------
4/25/17 uninstalled sourmash, re-installed with the 2017-metagenome branch

```
source sourmashEnv2/bin/activate
pip uninstall sourmash
pip install https://github.com/dib-lab/sourmash/archive/2017-metagenome.zip
```
------------------------------------
5/16/17 some new changes have made there way in to sourmash, and the 2017-metagenome branch is out of date. Uninstall sourmash, and reinstall from the master.

```
source sourmashEnv2/bin/activate
pip uninstall sourmash
pip install https://github.com/dib-lab/sourmash/archive/master.zip
```

5/29/17 some new changes, etc.
```
source ~/sourmashEnv2/bin/activate
pip uninstall sourmash
pip install https://github.com/dib-lab/sourmash/archive/master.zip
```
