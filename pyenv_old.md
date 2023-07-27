DO NOT USE:  This was my attempt at using `pyenv` over `miniconda`, but failed to see any benefits in switching over.  Use [miniconda](https://docs.conda.io/en/latest/miniconda.html) for managing both python versions and python virtual environments!

-------


## Python Virtual Environments

The **system python** should be left alone, as it is responsible for running some machine processes.  Updating packages or modifying the system python runs the risk of interfering with important system processes.  As such, it is best to have a way to manage virtual python environments: separate areas where you can install different python and package versions without affecting the system python.  You can have multiple virtual environments for separate projects, different package version, etc.

### Install pyenv

- `pyenv` - manage multiple versions of python (e.g. 3.9, 3.10, etc.)
- `pyenv-virtualenv` - manager multiple python virtual environments

Install using brew:
```
brew install pyenv
brew install pyenv-virtualenv
```
It's recommended to modify `~/.zprofile` instead of `~/.zshrc` because [reasons](https://ggkbase-help.berkeley.edu/how-to/install-pyenv/).

Update your `~/.zprofile` so `pyenv` is activated every shell session:
```
# add commands to ~/.zprofile
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zprofile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zprofile
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.zprofile
echo -e 'eval "$(pyenv virtualenv-init -)"' >> ~/.zprofile

# source file to activate changes
source ~/.zprofile
```


Install different versions of python.  The latest as of July 2023 is 3.11.4:
```
# Install some versions of python
pyenv install 3.9
pyenv install 3.11
```

See installed versions:
```
pyenv versions
  system
* 3.9.17 (set by /Users/nrichards/.pyenv/version)
  3.11.4
```

Set global default version:
```
pyenv global 3.9.17
```

Confirm the global version:
```
python --version
Python 3.9.17
```

Additional Resources

- https://www.youtube.com/watch?v=547Jr26duHQ
- https://ggkbase-help.berkeley.edu/how-to/install-pyenv/
- https://github.com/pyenv/pyenv-virtualenv


### Create virtual environments

https://github.com/pyenv/pyenv-virtualenv#usage


### Install poetry

Don't use brew to install poetry!  Instead, install poetry with pip (?).

