# Handling Multiple SSH Keys

Usernames and passwords work for a small home environment, but if you’re handling multiple VM’s (or if you want to seamlessly code in an IDE like VS Code and push your changes to GitHub) you need to be able to create and manage multiple SSH keys on your system.

There are good posts online about creating an SSH key to use for SSH’ing into a remote machine, but it starts to get a slightly more complicated when trying to handle multiple SSH keys as you have to give each one a unique name.

## Creating SSH keys for accessing remote machines/ VM’s

Creating a single SSH key for accessing a remote machine is straightforward, but we want to be able to create and use multiple different SSH keys. That requires us to name them uniquely. I will go through making two different SSH keys for two different remote machines.

    1. Change your directory
        ` cd ~/.ssh`

    2. Create SSH Key
        `ssh-keygen -t ed25519`
