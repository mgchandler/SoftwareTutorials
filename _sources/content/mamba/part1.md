## Setting up a new environment

Mamba discourages the installation of anything in the base environment. Instead, it is recommended to make new environments to install packages to. In my installation, I have four additional ones:

<img src="environments.png" alt="Matt's Python Environments" width="750"/>

- `daily` : a scratch environment for Python work which doesn't have many package requirements.
- `playground` : an experimental scratch environment for playing with new packages, which won't impact my daily use.
- `arim-env` : an environment for a major project, containing (amongst other things) a working [`arim`](https://github.com/ndtatbristol/arim) installation.
- `ssgb` : an environment for a major project, containing packages for working with [Gaussian Processes](https://gaussianprocess.org/).

To set up a new environment, use the following command template:
```
mamba create -n <environment_name> <list of packages...>
```

After creation, activate this environment to use it with
```
mamba activate <environment_name>
```
and deactivate an active environment with
```
mamba deactivate
```

Install a new package to an active environment from a specific channel (e.g. `conda-forge`) with
```
mamba install <package1> -c <channel> ...
```

Finally, to delete an environment, use
```
mamba env remove -n <environment_name>
```

For more info, see the more detailed [documentation page](https://mamba.readthedocs.io/en/latest/index.html).