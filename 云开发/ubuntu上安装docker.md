
## Installation methods

You can install Docker Engine in different ways, depending on your needs:

-   Docker Engine comes bundled with [Docker Desktop for Linux](https://docs.docker.com/desktop/install/linux-install/). This is the easiest and quickest way to get started.
    
-   Set up and install Docker Engine from [Docker’s `apt` repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository).
    
-   [Install it manually](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package) and manage upgrades manually.
    
-   Use a [convenience scripts](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script). Only recommended for testing and development environments.
    

### Install using the apt repository

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

#### Set up the repository

1.  Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:
    
    ```
     sudo apt-get update
     sudo apt-get install \
        ca-certificates \
        curl \
        gnupg
    ```
    
2.  Add Docker’s official GPG key:
    
    ```
     sudo install -m 0755 -d /etc/apt/keyrings
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
     sudo chmod a+r /etc/apt/keyrings/docker.gpg
    ```
    
3.  Use the following command to set up the repository:
    
    ```
     echo \
      "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```