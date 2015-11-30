---
layout: post
title: Creating a Virtual Environment with Conda
---

Hello there!

I was creating a virtual environment to be used for the next django project (Inventory Management System) when i realised 
that i was using python 2! This has to change now. I need to be using Python 3!

So lets start Googling!

Okay, so i found an answer on [stack overflow](http://stackoverflow.com/questions/23842713/using-python-3-in-virtualenv)
and it says that i can simply run the command: `virtualenv -p python3 envname` to create a virtual environment with 
python 3.

Looked pretty simple! So i did that BUT i encountered an error:   
``` 
.... OSError: Command /home/zishan/develop...ory/venv/bin/python3 -c "import sys, pip; sys...d\"] + sys.argv[1:]))" 
setuptools pip failed with error code 1
```

Then i found an answer on the same stack overflow link which involved updating virtualenv: `pip install --upgrade 
virtualenv`

I tried updating virtualenv but then i got a prompt saying virtualenv doesnt work with anaconda and you 
should use `conda` to create an evironment instead and then it asked me if i wanted to continue. 
I skipped it and googled for `conda` instead.

So to create an environment with `conda` we type the following command: `conda create --name envname python=3`

Note that `--name` can be shortened to `-n` instead.

Once i typed the above command, it gave me a list of packages that will be downloaded and installed. I didn't really 
expect that, virtualenv doesn't download anything, then why is conda downloading packages? Anyways, once the packages 
were downloaded, the environment was created and i got the following message:  
```
To activate this environment, use: source activate venv
To deactivate this environment, use: source deactivate
```

Now that my environment has been created, lets get on with django!

**Update:**
Pip is not installed by default in an environment created by conda. So there are two ways to install packages:  
1. Install using conda
  - To search for a package, type `conda search <packagename>`  
  - To install a package, type `conda install <packagename>`  
2. Install using pip 
  - Install pip using conda by typing: `conda install pip`
