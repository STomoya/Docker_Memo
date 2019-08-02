
# Ubuntu

Linux is complicated...  
Especially around the dependencies :<

## 1 Installation

The best is to carefully follow the instructions in the [official document](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

- 1.1 Repository setup
  1. erase old version

  ```console
      $ sudo apt-get remove docker docker-engine docker.io containerd runc
  ```

  2. Install packages to allow apt to use a repository over HTTPS

  ```console
      $ sudo apt-get update
      $ sudo apt-get install \
          apt-transport-https \
          ca-certificates \
          curl \
          gnupg-agent \
          software-properties-common
  ```

  3. add docker's GPG key

  ```console
      $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  ```

  4. Verify Key

    Check if the output is as same as the number shown in the [document](https://docs.docker.com/install/linux/docker-ce/ubuntu/).  
    (Maybe it changes? I don't know so I won't write it here.)

  ```console
      $ sudo apt-key fingerprint 0EBFCD88
  ```

  5. Add repository (stable)

  ```console
      $ sudo add-apt-repository \
          "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
          $(lsb_release -cs) \
          stable"
  ```

- Install Docker

  1. Install Docker

  ```console
      $ sudo apt-get update
      $ sudo apt-get install docker-ce docker-ce-cli containerd.io
  ```

    This command will install the latest docker version.  
    - If you want a specific version you can

        ```console
            $ apt-cache madison docker-ce

                docker-ce | 5:19.03.1~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
                docker-ce | 5:19.03.0~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
                docker-ce | 5:18.09.8~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
                docker-ce | 5:18.09.7~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
                docker-ce | 5:18.09.6~3-0~ubuntu-bionic | https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
                ...
        ```

        The output is only for my evironment. It will change on your's.  
        The format is  `docker-ce | (version) | (url)  `  
        For example, if you want `5:18.09.6~3-0~ubuntu-bionic`, you can install the version by

        ```console
            $ sudo apt-get update
            $ sudo apt-get install docker-ce=5:18.09.6~3-0~ubuntu-bionic docker-ce-cli=5:18.09.6~3-0~ubuntu-bionic containerd.io
        ```

        But I think the latest release is the best selection...

  2. Verify Docker

  ```console
      $ sudo docker run hello-world
  ```

### 2 Use Docker as a non-root user

  At this point, you need a `sudo` to use docker.  
  This might cause some problems, (and is frustrating to always type in "sudo"),  
  you can set Docker as a non-root user, by some ways.

- method 1
  
  Simple  
  Do not forget to login and out.

  ```console
      $ sudo usermod -aG docker $USER
  ```

- method 2

  I have not checked if this works.  
  A method from [here](https://qiita.com/1000k/items/03a17c49471de881d5c0).  
  Try this when method 1 does not work.

  ```console
      $ sudo gpasswd -a $(whoami) docker
      $ sudo chgrp docker /var/run/docker.sock
      $ sudo service docker restart
  ```

### 3 Install docker-compose

As same as installing Docker, you should follow the [official document](https://docs.docker.com/compose/install/)

  1. Download docker-compose

  ```console
      $ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  ```

  2. Executable permission to binary

  ```console
      $ sudo chmod +x /usr/local/bin/docker-compose
  ```

  3. Verify docker-compose

  ```console
      $ docker-compose --version
  ```

### 4 Usage

Make files to run Docker and run it.  
Please learn this by yourself. (Or I might make a different file for this.)

### 5 Issues I got

- config.json

  When you run Docker, you might get warnings like this.

  ```console
      WARNING: Error loading config file: /home/USER/.docker/config.json: stat /home/USER/.docker/config.json: permission denied
  ```

  You can fix this by the commands right after.  
  The solution is from [here](https://askubuntu.com/questions/747778/docker-warning-config-json-permission-denied)

  ```console
      $ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
      $ sudo chmod g+rwx "/home/$USER/.docker" -R
  ```

- Recreating error

  When I tried `docker-compose up -d`, after fixing some bugs in my Dockerfile and built it,  
  Docker kept on saying that the image does not exist.  
  The error message was like

  ```console
      ERROR: for xxx no such image: sha256:xxxx: No such image: sha256:xxxx
  ```

  The problem was that there was an old image, that was not working, in the process.  
  Run the command bellow and you can see all the processes, including the ones that are not moving.  

  ```console
      $ docker ps -a
  ```

  You will get a set of, (CONTAINER ID, IMAGE, COMMAND, CREATED, STATUS, PORTS, NAMES) for each image.  
  Erase the old image by

  ```console
      $ docker rm [CONTAINER ID]
  ```

  After erasing the old image, erase the latest image too for certainty  
  Then build the image again.

  If this happens you should always run

  ```console
      $ docker-compose stop
      $ docker-compose rm
  ```

  Before rebuilding the image.

- Installation

  I have already wrote how, but I have not wrote why.  

  When you do not have Docker installed in your computer, and run `docker` or `docker-compose` in the shell, it will say that it could not find docker or docker-compose, and also will recommend you that it can be installed by commands.  

  Please do not use the commands they recommend!!  
  
  I have followed the recommendation once and the result was horible.  
  After installing Docker, I also installed docker-compose by their recommendation.  
  
  snap went somewhere...  

  I did several things like reinstall snap or find the directory the snap should be recognizing, but it did not work and I had to do a clean-install to fix this.  

  I choosed to clean-install ubuntu because the environment was on Virtualbox, and also I made it really resently, so there was almost no damage to me, except for my effort. But imagine this happening to your main pc...  

  So I really recommend you to follow the official documents : )

## From the Author

Have a good Docker life.

## Author

[Sawada Tomoya](https://github.com/STomoya/)
