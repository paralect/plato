# Documentation

If you want to took part in infrastructure development, follow this guide. 

### Tools

Install or upgrade the following tools to the latest versions: 

1. [Install Docker](https://www.docker.com/products/docker). Do not use package managers (such
as `brew` or `apt`). Download Docker from official site. They created native application
for Linux, Mac and Windows that will automatically update. We use version 1.12.

2. [Install VirtualBox](https://www.virtualbox.org/wiki/Downloads). Do not use package managers. 
Download from official site. We use version 5.1.6.

3. [Install Vagrant](https://www.vagrantup.com/downloads.html). Do not use package managers.
Download from official site. We use version 1.8.5.

4. [Install Go](https://golang.org/dl/). Do not use package managers. Download from official site.
We use version 1.7.1. Check [this page](https://golang.org/doc/install) for installation instructions. 

Check your installation and versions:

```bash
$ docker --version                # 1.12
$ virtualbox --help | head -n 3   # 5.1.6
$ vagrant --version               # 1.8.5
$ go version                      # 1.7.1    
```

### Enter Plato development shell

In the root project folder type:

```bash
$ bin/shell 
```

You are now inside Plato development shell which configures all required environment variables. 
Type `exit` if you want to go back to normal shell.

### Build Plato CLI tool

Inside Plato development shell type the following:

```bash
$ go install plato
$ plato --version     
```

If you see something like `Plato 0.1`, it means we are done. Congratulations!



