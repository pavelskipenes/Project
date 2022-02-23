# Elevator Project Template for Go
Project template for Elevator Project in Go. This repository includes drivers, elevator server for running in the lab and elevator simulator for working from outside the lab. Project description and requirements can be found in [requirements.md](requirements.md) file. This guide is targeting Ubuntu users. If you're using other distro then you probably know what you need to do.

## Prerequisites
Check go version
```
go version
```
If you don't have go installed (or if you want the latest version) install it following [these instructions](https://go.dev/doc/install).
> Note: the instructions tell you to run this command: `rm -rf /usr/local/go && tar -C /usr/local -xzf goVERSION.linux-amd64.tar.gz`. If you get access denied make sure to run it with `sudo` before `rm` and `tar` command like so: `sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf goVERSION.linux-amd64.tar.gz`

## Installation

Clone the repository. Make sure you clone this repo and not just download the zip file otherwise you'll not get the dependencies in this template.
```
git clone --recursive THIS_URL
cd Project
```

### Compile elevator server and elevator simulator
This step is not really necessary because lab computers usually have them installed by default. But there might be some situations where you would like to compile them anyway. Like:
- These programs are not installed on the lab computer
- Programs are not in the path and cannot be found
- You want to work from home
- You use an Arm processor like Raspberry Pi or M1 MacBook
- You just like to compile stuff

```
sudo wget https://netcologne.dl.sourceforge.net/project/d-apt/files/d-apt.list -O /etc/apt/sources.list.d/d-apt.list
sudo apt-get update --allow-insecure-repositories
sudo apt-get -y --allow-unauthenticated install --reinstall d-apt-keyring
sudo apt-get update && sudo apt-get install dmd-compiler dub
```
[source](https://dlang.org/download.html)

```
cd vendor/elevator-server/src/d/
dmd elevatorserver.d arduino_io_card.d -of=../../build/elevatorserver
chmod +x build/elevatorserver
cd -
cd vendor/Simulator-v2
dmd -w -g src/sim_server.d src/timer_event.d -of=build/SimElevatorServer
chmod +x build/SimElevatorServer
cd ../../
```

elevator server can now be started by running `vendor/elevator-server/build/elevatorserver`. Simulator can be started by running `vendor/Simulator-v2/build/SimElevatorServer`.
For usage check out `readme.md` in [elevator simulator](vendor/Simulator-v2/README.md) and [elevator server](vendor/elevator-server/README.md).

> Note that elevator-server and elevator simulator should not be running at the same time.

## Getting started

To test that your project is working copy `main.go` file from `driver-go` dependency into `src` and run it. Before you continue make sure elevator server (or if you're at home the elevator simulator) has started in its own terminal window first.
```
cd src
cp vendor/driver-go/main.go main.go
go run main.go
```
If elevator starts going up and down you're set. Clear out everything in `main.go` and start the project.

## Tips

### Disable middle click paste
For some reason someone decided that it's a good idea to have a separate clip board for selected text that is being pasted into any window when clicking middle mouse button. This behavior might cause accidental insertions into vs code. To disable this "functionality" click Ctrl+Shift+P in vs code and select "Preferences: Open Settings (JSON)" and paste this line in: `"editor.selectionClipboard": false`.

### Avoid commits with random authors

When attempting to create a commit for the first time you're usually presented with something like this:
```
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'pavel@Matebook.(none)')
```
git tells us that the author is unknown and suggest to add commiter email and name to global config if you're not reading carefully. This config is placed in `~/.gitconfig`. This is a bad idea in the lab because lab computers are shared and if you forget to delete it you'll end up with random authors in your project. According to [this stack overflow answer](https://stackoverflow.com/a/53013818/9590993) you can rather run `git config --local user.name "Your name"`, `git config --local user.email` and make author changes on a project level. Or just omit `--global` flag at all.

### Creating go modules
If you want to create a new module in the project then you need to place it inside a folder with the same name. For instance if you want to create module named `algorithm` then you need to create a folder `algorithms` inside `src` and then all files inside there needs to start with `package algorithm`.

### Updating the dependencies
If the dependencies got a new commit you can update them all by running `git submodule foreach git pull`. Then commit the changes.