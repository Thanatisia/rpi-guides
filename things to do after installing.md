# Raspberry Pi - Things to do on first startup/after installation

```
(My) things to do after initial startup process
```

## Steps
- [Update Packages](#update-packages)
- [Install Essential Packages](#install-essential-packages)
- [Setup Networking](#setup-networking)
- [User Management](#user-management)

## Things to do
### Update Packages
```
Always do this first because depending on the Operating System as well as time 
of installation, there may be new version of the installed packages as well as system
packages such as kernels available.
```
- Debian-based/Raspberry Pi OS
    - Get the latest official package updates
        ```console
        sudo apt update
        ```

    - Upgrade the retrieved updates
        ```console
        sudo apt upgrade
        ```

    - Upgrade to the latest release
        ```console
        sudo apt full-upgrade
        ```

### Install Essential Packages
- Some important starter "must-have" packages that I usually install Out-of-the-Box are
        - Text/Code Editors
	    + Vim
        - DevOps / Development
	    + git
        - Filesystems
            + ntfs3g : For NTFS
        - Networking
            + dnsutils : For DNS-related utilities - such as dig
	    - NetworkManager
                ```
                Raspberry Pi OS/Debian-based Distributions : network-manager
                ```
        - Server
            + arch-install-scripts
                ```
                This package contains genfstab, which is a tool used (generally) by ArchLinux during the manual installation step 
                to setup the filesystems table (fstab) easily.

                This means using genfstab, you do not need to 
                    1. manually get the device block id via blkid
                    2. manually write the blkid/UUID into filesystems table 
                    
                The arch-install-scripts package also comes with various useful sysadmin utilities on top of genfstab.
                ```

### Setup Networking
```
This is an important step to allow Headless Remote Management without the use of a physical 
display output
```

- Setup Network
    > Assuming you are using Wi-fi
    - Startup NetworkManager
        ```console
        sudo systemctl start NetworkManager
        ```
    - Run raspi-config
        ```console
        sudo raspi-config
        ```
        - Select "Wifi"
            + Add your Wifi
            + Done
    
- Setup SSH
    - Enable SSH
        - You will want to enable SSH so that the next time you reboot,
            + you can immediately SSH into the system
        ```console
        sudo systemctl enable ssh
        ```

    - Start SSH
        ```
        sudo systemctl start ssh
        ```

### User Management
```
NOTE:
- This is assuming you are using a distro that doesnt require you to create a new user from the get-go or are using an older version of Raspberry Pi OS
    - Raspberry Pi OS as of the September update requires the user to create a new user
```
- Create new user and add to group
    - Debian-based Distributions
        + Debian-based Distributions (such as Raspberry Pi OS) uses the 'sudo' group instead of the 'wheel' group for sudo capabilities
        - Parameters
            + -d [home-directory] : Specify home directory path
            + -g [primary-group] : Set primary group for the user
            + -G [secondary-groups,] : Set subsequent secondary groups to the user
            + -m : Specify to create a home directory but use custom home directory
        ```console
        sudo useradd -m -g sudo -G users -d [home-directory] [username]
        ```

- (OPTIONAL) Add user to a new group
    - Parameters
        + -a : Append the new groups to the specified user instead of overwriting
        + -G [new-groups,] : Specify new secondary groups to add the user in to
    ```console
    sudo usermod -aG [new-groups,] [username]
    ```

### Swap files
```
Swap files is a dynamic way of handling swap memory (aka paging in Windows), as opposed to creating Swap partitions.
```
- Creating new swap file
    ```console
    sudo fallocate -l [size] /swapfile
    ```

- Set permission of swapfile to Read + Write only by Root
    ```console
    sudo chmod 600 /swapfile
    ```

- Format swapfile filesystem
    ```console
    sudo mkswap /swapfile
    ```

- Enable the swapfile
    ```console
    sudo swapon /swapfile
    ```

- (OPTIONAL) To make the swapfile active permanently
    + Append the swapfile into the filesystems table (/etc/fstab)
    ```console
    echo -e "# /swapfile" | sudo tee -a /etc/fstab
    echo -e "/swapfile swap swap defaults 0 0" | sudo tee -a /etc/fstab
    ```

- (OPTIONAL) Verify swap is active
    - Using swapon
        ```console
        sudo swapon --show
        ```

    - Using free
        ```console
        free -h
        ```

- Remove swapfile
    - deactive swapfile
        ```console
        sudo swapoff -v /swapfile
        ```

    - Remove swapfile
        ```console
        sudo rm /swapfile
        ```

    - Remove the /swapfile entry from the filesystems table (/etc/fstab)
        ```console
        # /swapfile
        /swapfile swap swap defaults 0 0
        ```

