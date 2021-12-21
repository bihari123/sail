[![StackShare](http://img.shields.io/badge/tech-stack-0690fa.svg?style=flat)](https://stackshare.io/gopaddleio/gopaddle)

# sail
Migrate Linux Processes to Docker Containers

# Prerequisite
## docker service
1. Make sure docker service is running
```
$ systemctl status docker
```
The output of the above command should have the line:
```sh
Active: active (running)
```
2. If there a user group called 'docker' created already, and if the
current user is already added to this group, you can skip this step:
```
sudo groupadd docker
sudo usermod -aG docker <current-user-name>
```
If you had to perform the above step, then:  
- Log out and log back in so that your group membership is re-evaluated.  
- On a desktop Linux environment such as X Windows, log out of your session
completely and then log back in.  
- If testing on a virtual machine, if the above do not work, restart the
virtual machine for changes to take effect.  
  
3. Verify that you can run docker commands without sudo.  
Example:
```
$ docker images
```
The above command should not show any 'permission denied' errors

## golang (Go language)
1. Make sure 'go' is installed.
```
Follow the steps listed in https://go.dev/doc/install
```

2. Ensure that 'go' executable is reachable via PATH
```
$ export PATH=$PATH:/usr/local/go/bin
```

3. Confirm that 'go' command is in your search path
```
$ which go
/usr/local/go/bin/go
```

# SAIL Command Line Utility
This is written in Go language and has to be built from the sail source in the following steps:  

1. do a git clone of sail code in the work directory of your choice
```
$ cd <workdir>
$ git clone https://github.com/gopaddle-io/sail.git
```

2. Go to 'sail' directory, and initiate it as the root of the module
that sail expects to import its 'go' packages into
```
$ cd sail
$ go mod init github.com/gopaddle-io/sail
go: creating new go.mod: module github.com/gopaddle-io/sail
...
```

3. Synchronize the vendor directory in sail code
```
$ go mod vendor
go: downloading golang.org/x/sys v ...
```

4. Go to the 'cmd' folder and build the 'sail' tool
```
$ cd cmd
$ go build sail.go
```

You should now see the an executable file called 'sail' built in this 'cmd' directory

## To List All Processes on the system:
```
$ ./sail list --all process
```

Example output:
```
 1124  1113 /opt/google/chrome/chrome --type=zygote --no-zygote-sandbox
 1125  1113 /opt/google/chrome/chrome --type=zygote
 1126  1124 /opt/google/chrome/nacl_helper
 1127  1125 /opt/google/chrome/nacl_helper
 1130  1125 /opt/google/chrome/chrome --type=zygote
 ```
 
## To Containerize the Process:

### Help command:
```
$ ./sail dockerize -h

Migrate a running linux process in to a Docker Image. 

sail dockerize --pid <process id> [--time <time in seconds>] [--imageName <docker image name>]

    -p, --pid           pid of the process to trace.
    -t, --time          Time in seconds to trace the process to build its docker profile. Defaults to 2 seconds.
    -i, --imageName     Name of the final docker image. Defaults to 'final'.
    -v, --verbose       Run with Verbose Mode
    -d, --directories   Directories to be copied(seperated by comma)  
```

### To trace your process, create a docker image, and run as container:


Assuming that you have a process with PID 14141 currently running on your system:  

##### Note: Use -v or --verbose to run with verbose mode 
```
$ ./sail dockerize -p 14141 -i nodechecker -t 20 -d /home/bluemeric/codebase/src/gopaddle/nodechecker 

start tracing...
tracing completed
Docker creating...
imageName:  nodechecker
Docker creation completed
Docker file copying ...
Docker file copying completed
Copying fmt file of trace to container...
nodechecker created
```
You should now have the process corresponding to PID 14141 running in a container.  
In the above run, the image name corresponding to this container is 'nodechecker'.  

##### Note: The traced process information will be stored under $HOME/.sail/ Directory.  

 

