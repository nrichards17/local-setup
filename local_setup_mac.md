# Local Setup (Mac)

- Install VS Code as your editor
- Manage python virtual environments with miniconda
- Install dbt Core (CLI tool)

On Mac, the default shell is `zsh` but `bash` is also available.

**NOTE:  this tutorial assumes you will be using the default `zsh` shell.**

## Download and Install Visual Studio Code

https://code.visualstudio.com/download

VS Code is an editor with a built-in terminal and plenty of 3rd party Extensions to customize your development environment.

### Install developer tools

Run the following command in the terminal:
```
python3
```

If not already installed, there will be a popup window prompting you to install the Mac command line developer tools:

![mac-dev-tools](https://user-images.githubusercontent.com/9124827/210427223-6288abf1-cd3e-45fd-87d2-0396c44ff623.png)

### Install homebrew

[Homebrew](https://brew.sh/) is a package manager for macOS, similar to `apt` (and others) for Linux.

Install with the following command:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Connect to GitHub via SSH

Prefer SSH with one-time key generation over HTTPS with constant password authentication.

1. [Generate new SSH key (local)](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
2. [Add new SSH key (GitHub)](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)


## Install miniconda

Find the latest installer for your OS/hardware [here](https://docs.conda.io/en/latest/miniconda.html) or use the latest Mac M1/M2 installer below:

https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.pkg

Install using the setup wizard.  

Conda will modify your `~/.zshrc` file such that the default conda environment will be activated with every new terminal session opened.

### Create conda virtual environment

Open a new terminal session; by default, the `(base)` conda environment will be activated:
```
(base) nrichards@TC-ML-NRichards Documents % 
```

Create a new environment with conda, optionally specifying the python version:
```
# use default latest version of python: 3.11 as of July 2023
conda create python -n yourenvname
# use specific version of python
conda create python=3.9 -n yourenvname2
```


## Create dbt environment and install dbt core for Databricks

```
# create conda env with latest version of python (3.11)
# named with the specific version of dbt we'll be installing
conda create python -n dbt-1.5

# activate new environment
conda activate dbt-1.5

# install dbt for Databricks at specific version
pip install dbt-databricks==1.5.5

# confirm dbt version
dbt --version
```


## Connect dbt to Databricks

https://docs.databricks.com/partners/prep/dbt.html#step-2-create-a-dbt-project-and-specify-and-test-connection-settings


Prerequisites
1. Create new Databricks access token
2. Identify Databricks SQL Warehouse connection info

### Create new Databricks access token

In Databricks, locate `User Settings > Access tokens > Generate new token`.  Provide a descriptive name (ex. "dbt core"), set a 90-day lifetime, and copy the access token to a temporary secure location.  

**DO NOT publish access tokens publicly or include them in version controlled code.**  You will either place it in your local file `~/.dbt/profiles.yml` OR use environment variables and access via `"{{ env_var('DATABRICKS_ACCESS_TOKEN') }}"` (needs to be set in your `~/.zshrc` file).

### Identify Databricks SQL Warehouse connection info

In Databricks, locate `Compute > SQL warehouses`.  Click on your desired warehouse and select `Connection details`.

Take note of `Server hostname` and `HTTP path` and populate your `~/.dbt/profiles.yml` appropriately.


### Set up your dbt connection to Databricks

NOTE: make sure your conda environment with dbt installed is activated before running these steps (ex. `conda activate dbt-1.5`)

Create a new project with `dbt init` and follow the CLI setup script prompts, or create/modify your `~/.dbt/profiles.yml` connection for your (personal) dev "target":
```
# profiles.yml

dbt_demo:
  outputs:
    dev:
      # catalog: <Not using Unity catalog currently>
      host: <Server hostname>
      http_path: <HTTP path>
      schema: dbt_XXXXXX-YOUR-NAME-XXXXXX
      threads: 2
      token: XXXXXXXXXXXXXXXXXXXXXXXXXX
      type: databricks
  target: dev
```

Confirm your connection to the Databricks SQL warehouse with:
```
dbt debug
# output details
...
15:58:17  Configuration:
15:58:18    profiles.yml file [OK found and valid]
...
15:58:20    Connection test: [OK connection ok]
```

If you are not running the command from dbt project's root directory, you will get an error `dbt_project.yml file [ERROR not found]`, but that is unrelated to your successful connection.  Navigate to the projects root directory and all connections/tests should pass.

Run a test build of your project in your dev environment:
```
dbt build
```

Tables that you build with `dbt run` or `dbt build` will appear in the schema `dbt_XXXXXX-YOUR-NAME-XXXXXX`.  It is dbt best practice to allow individual developers to recreate tables in a personal, isolated schema.

Staging and Production runs require additional targets to be defined, which is outside the scope of this Local Developer setup guide.