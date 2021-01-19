# Remote VScode development in a container

Based on [this info](https://code.visualstudio.com/docs/remote/containers-advanced#_developing-inside-a-container-on-a-remote-docker-host)

These are the set of files I was required to created for a remote container development environment for vscode.

## My environment

I have a laptop I tend to do my development on which while _fast_ it is not overly amazing. If I want to run CPU intensive tasks fans will spin up and it's generally quite slow.

I also have a gaming PC with a 12 core CPU (24 threads) which sits idle unless I'm in FS2020 or Doom Eternal. I can install docker on the gaming PC and utilize it's CPU as my development environment from my laptop which provides a hugely faster CPU (the difference with cinebench is ludicrious).

Following these steps I could also set up an EC2 instance as my development environment.

### Setup

#### Docker 
In my laptop and gaming PC I've install Docker in WSL2 with [these steps](https://docs.docker.com/docker-for-windows/wsl/) (not the GPU para-virtualization yet - when drivers are stable)

#### SSH from Laptop to Gaming PC
On the gaming PC I've enabled SSH login in windows (windows 10 professional is required) via [these instructions](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)

On the gaming PC as my account is a local administrator I've had to change the location that sshd looks for authorized keys as per [these instructions](https://superuser.com/questions/1445976/windows-ssh-server-refuses-key-based-authentication-from-client).

On the laptop I've installed [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) - This is not required but it's a nicer environment for local powershell/wsl2 commands.

#### Docker and VScode
On the laptop I configure docker/vscode to connect over ssh with a new context via [these instructions](https://code.visualstudio.com/docs/containers/ssh)


# Config

## .devcontainer
This file tells vscode how to build the devleopment container environment which your local vscode will then connect to.

The important part in this file are:
- `dockerFile`: this refers to the `Dockerfile` you wish to use as your development environment.
- `workspaceFolder`: The folder that vscode will work within
- `workspaceMount`: The mount command to attach your local source files to the running container as a volume.

Note that you can leave out `workspaceMount` and it will default to using a `bind` mount in docker.  This works when your workspace files are local to the docker daemon but does not work when the docker daemon is remote. In the remote case you have to create a named volume which can be mounted in the remote docker container (see Usage below).

## Dockerfile
You don't need this file but this can be a custom docker file that vscode can build and run. In this instance i'm using the stable-baselines CPU development container and installing the stable baselines libraries within it. This gives me a container with python3, tensorflow-cpu, skikit-learn and other libaries for exploring reenfocement learning.

# Usage

1. Check out the Setup steps above to create the environment
1. Create a named volume using the command palette `Remote-Containers: Clone Repository in Container Volume...` 
1. Select this repository as the source (you will want to fork this repo to keep your own changes)
1. Use command palette for `Remote-Containers: Reopen in Container...`