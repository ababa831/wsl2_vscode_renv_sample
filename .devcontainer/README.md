Docker development enviroment setup for R project
==================================================

## Features

- Git & GitHub
- Docker & Docker Compose
- RStudio Server
- renv
- dotfiles
- Visual Studio Code Remote - Containers

## How to start

### Create a new project

Create a new repository (e.g. `your-org/your-rproject`) using this template repository [`terashim/rproject`](https://github.com/terashim/rproject) on GitHub, or you can just copy this `.devcontainer` folder into your existing project.

### Local machine setup

- Install Docker
- Install SSH
- Install Git
- Setup GitHub auth by SSH key
- enable SSH Agent and add your key
- Install VS Code and its Remote Container extension
- Clone the project repository (e.g. `your-org/your-rproject`) into your local machine (e.g. `~/ghq/github.com/your-org/your-rproject`).

## Workflow

### Start and stop the container

Assuming the Docker daemon is running on your local machine

1. Open the local repository with VS Code
2. Open the command pallete by hitting Ctrl/Cmd+Shift+P and select "Reopen folder in container" command to start the RStudio Server container
3. Open <http://localhost:8787> in your web browser to connect to the RStudio Server
4. Do your development/analysis work on the RStudio Server
5. Close the VS Code window to stop the container

### Version Control by Git

- Use Git for version control of your source code.
- You can use Git installed in the Docker host.
- You can also use Git installed in the container via RStudio or VS Code.
- Share your code and collaborate with your team via GitHub.

### Package management by renv

- [The "explicit" snapshot type](https://rstudio.github.io/renv/articles/renv.html#explicit-snapshots) is set as default in this project by [`renv/settings.dcf`](../renv/settings.dcf) file.
- To use new packages in the project, write those dependencies to the [`DESCRIPTION`](../DESCRIPTION) file and execute `renv::install()` and `renv::snapshot()`.
- Share the [`renv.lock`](../renv.lock) file generated by `renv::snapshot()` with your team by pushing it to the GitHub repository so that collaborators can reproduce the same package versions.

## Configuration

### Customization by .env file

Copy [`.env.example`](./.env.example) to `.env` file and edit it
to change the environment variables used in [`compose.yml`](./compose.yml.).
The `.env` file is not tracked by Git
so that you can configure your local environment without affecting other enviroments.

### renv package cache

By default, downloaded packages are cached in the mounted directory [`./data/renv/cache`](./data/renv/cache).
This cache directory is specific to this project and not shared with other projects.

It is recommended to use a single global package cache on your machine so that cached package files can be reused in other projects.
For example, `~/Library/Application Support/renv/cache` is the standard path for macOS.
To specify the global cache path, set `RENV_PATHS_CACHE_HOST` variable in the `.env` file like:

```
RENV_PATHS_CACHE_HOST="~/Library/Application Support/renv/cache"
```

See <https://rstudio.github.io/renv/articles/renv.html#cache> for details.

### RStudio and Git global configuration

You can change the RStudio configuration in "Tools" > "Global Options" window as usual.
The change is saved in the config files in [`./dotfiles/.config/rstudio/`](./dotfiles/.config/rstudio/) folder by default.

When you change the Git global configuration by `git config --global` command in the container, the config is saved in [`./dotfiles/.config/git/`](./dotfiles/.config/git/) folder by default.

These config files are specific to the project.
Use **dotfiles** to sync those settings with other projects.

### Dotfiles by VS Code

You can specify your dotfiles GitHub repository in VS Code user settings.
See <https://code.visualstudio.com/docs/remote/containers#_personalizing-with-dotfile-repositories> for details.

When you use this setting,
disable the default mounted dotfiles to avoid conflict
by adding the lines below to `.env` file:

```
DOTFILES_DIR_HOST=/dev/null
DOTFILES_INSTALL_COMMAND=":"
```

### Mount dotfiles

You can also make your own common dotfiles directory in the Docker host (e.g. `~/dotfiles`) and mount it into the container.
Edit `.env` file to set the dotfiles path in the Docker host, the mount path in the container, and the install command like this:

```
DOTFILES_DIR_HOST=~/dotfiles
DOTFILES_DIR_CONTAINER=/home/rstudio/dotfiles
DOTFILES_INSTALL_COMMAND="~/dotfiles/install.sh"
```

### SSH auth

Follow the steps below to access to GitHub from the container with SSH

1. Create an SSH key and add it to your GitHub account.
2. Enable SSH Agent on the Docker host.
3. Add the key to SSH Agent.
4. The socket of SSH Agent will be mounted into the container automatically by VS Code, and the key will be available in the container.

### Workspace paths

By default, the parent of this folder is mounted into `/home/rstudio/project` in the container.
you can change the source and target paths by editing `.env` variables `LOCAL_WORKSPACE_FOLDER` and `CONTAINER_WORKSPACE_FOLDER`, respectively.

```
LOCAL_WORKSPACE_FOLDER=../sub-dir
CONTAINER_WORKSPACE_FOLDER=/home/rstudio/your-project-name
```

## References

Git  
- [Git - Book](https://git-scm.com/book/en/v2)
- [Connecting to GitHub with SSH - GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

Docker  
- [Docker Desktop overview | Docker Documentation](https://docs.docker.com/desktop/)
- [Compose specification | Docker Documentation](https://docs.docker.com/compose/compose-file/)
- [docker compose | Docker Documentation](https://docs.docker.com/engine/reference/commandline/compose/)
- [Networking features in Docker Desktop for Mac | Docker Documentation](https://docs.docker.com/desktop/mac/networking/#ssh-agent-forwarding)

dotfiles  
- [GitHub does dotfiles - dotfiles.github.io](https://dotfiles.github.io/)
- [RStudio ??????????????????????????? dotfiles ??????????????? - terashim.com](https://terashim.com/posts/rstudio-dotfiles/) (Japanese)

RStudio  
- [RStudio - RStudio](https://rstudio.com/products/rstudio/#rstudio-server)
- [RStudio 1.3 Preview: Configuration and Settings | RStudio Blog](https://blog.rstudio.com/2020/02/18/rstudio-1-3-preview-configuration/)
- [Customizing Keyboard Shortcuts ??? RStudio Support](https://support.rstudio.com/hc/en-us/articles/206382178-Customizing-Keyboard-Shortcuts)
- [Version Control with Git and SVN ??? RStudio Support](https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN)
- [Rocker Project](https://www.rocker-project.org/)

renv  
- [Project Environments ??? renv](https://rstudio.github.io/renv/index.html)
- [Using renv with Docker ??? renv](https://rstudio.github.io/renv/articles/docker.html)
- [R????????????????????????????????????renv???????????? - Qiita](https://qiita.com/okiyuki99/items/688a00ca9a58e42e3bfa) (Japanese)
- [renv ??? Docker ??????????????????????????? - terashim.com](https://terashim.com/posts/renv-docker-patterns/) (Japanese)

Visual Studio Code Remote - Containers  
- [Developing inside a Container using Visual Studio Code Remote Development](https://code.visualstudio.com/docs/remote/containers)
- [???R???Windows10???RStudio Desktop?????????????????????VSCode??????rocker???????????????????????????????????? - Qiita](https://qiita.com/eitsupi/items/ae0f89266b560b4e7096#devcontainerdevcontainerjson) (Japanese)
