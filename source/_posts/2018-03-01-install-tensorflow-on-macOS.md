---
title: install Tensorflow on macOS
date: 2018-03-01 17:12:57
categories: development
tags:
- Machine Learning
- Deep Learning
- TensorFlow
- macOS
---

This is a simple note for installing TensorFlow from source on macOS.
<!-- more -->
## Install python
```sh
brew install python
```
It will install Python-2.7.14. Type `python2.7` and check the version.

## Install pip
```sh
sudo easy_install pip
```

## Install virtualenv
```sh
sudo pip install virtualenv --upgrade
```

## Create a env
```sh
virtualenv --system-site-packages ~/tensorflow --python=python2.7
```

## activate the env
```sh
cd ~/tensorflow
source bin/activate
```

## Install Tensorflow from source code

```sh
brew cask install caskroom/versions/java8
brew install bazel
pip install six numpy wheel
git clone https://github.com/tensorflow/tensorflow
cd tensorflow # the source code dir
git checkout r1.1 # up to you
./configure
bazel build --local_resources 3072,4,1.0 --config=opt //tensorflow/tools/pip_package:build_pip_package
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
pip install /tmp/tensorflow_pkg/tensorflow-1.6.0rc1-cp27-cp27m-macosx_10_12_x86_64.whl
```