# Handling Multiple SSH Keys

Usernames and passwords work for a small home environment, but if you’re handling multiple VM’s (or if you want to seamlessly code in an IDE like VS Code and push your changes to GitHub) you need to be able to create and manage multiple SSH keys on your system.

There are good posts online about creating an SSH key to use for SSH’ing into a remote machine, but it starts to get a slightly more complicated when trying to handle multiple SSH keys as you have to give each one a unique name.

## Creating SSH keys for accessing remote machines/ VM’s

Creating a single SSH key for accessing a remote machine is straightforward, but we want to be able to create and use multiple different SSH keys. That requires us to name them uniquely. I will go through making two different SSH keys for two different remote machines.

1. Change your directory
` cd ~/.ssh`

2. Create SSH Key
`ssh-keygen -t ed25519`

-  When it ask’s where to save the key, you want to save it in your .ssh directory. If you wanted to save it as the default id_ed25519 file, that would work for one machine, but if you want to use multiple keys, you need to give it a unique name
    - For example (if my user was named jonezy) I would enter `/home/jonezy/.ssh/id_ed25519_Remote1`
    - Remote 1 can be any identifier you want to use for the remote machine
        - You can add a password or if you would rather not you can just press Enter twice for no password
3. Add SSH key to the ssh-agent
    - Start the ssh agent in the background `eval "$(ssh-agent -s)"`
    - Now you want to edit the config file. If you don’t have one yet, don’t worry, this will create it. `vi config`
    - Add the following to your config file:
        ```bash
        Host <Remote machines IP>
        AddKeysToAgent yes
        IgnoreUnknown UseKeychain
        UseKeychain yes
        IdentityFile ~/.ssh/id_ed25519_Remote1
        ```
    - Save and quit by typing `ESC` followed by `:wq!` and `Enter`
5. Send your SSH key to the remote machine
    ```
    ssh-copy-id -i ~/.ssh/id_ed25519_Remote1 user@remote1
    ```
6. Test
    - Now attempt to ssh into your remote machine. If it doesn’t ask you for a password, it worked.
7. Disable password based authentication
    - Once SSH’d into your remote machine, it is best practice to disable password ssh authentication. `vim /etc/ssh/sshd_config`
    - Change `PasswordAuthentication` from `yes` to `no`
    - Restart ssh `sudo systemctl restart sshd`
### To do this for a second machine, repeat the steps above but change the filename of the ssh key and ssh-copy the public key to the second remote machine.

## Creating SSH keys for using GitHub with VS Code

This is assuming you already have git installed. If you don’t, you can run sudo yum install git if on Red Hat or sudo apt-get install git if on Debian. If you have a mac, git should be installed by default when you install [xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

1. Configure basic info

```bash
git config --global user.name "YOUR_GITHUB_USERNAME"
git config --global user.email "YOUR_GITHUB_EMAIL"
```

2. Generate SSH key

`ssh keygen -t ed25519 -C "YOUR_GITHUB_EMAIL"`
- Name it something like `/home/jonezy/.ssh/id_ed25519_Github`
- `Enter` twice to not set a password

3.  Add SSH key to SSH Agent

` vim ~/.ssh/config`

```bash
Host *
AddKeysToAgent yes
IgnoreUnknown UseKeychain
UseKeychain yes
IdentityFile ~/.ssh/id_ed25519_Github
```
- `ESC `and `:wq!` to save

Now run: `ssh-add ~/.ssh/id_ed25519_Github`

4. Add public key to Github

- On your machine where you created the SSH key `cat ~/.ssh/id_ed25519_Github.pub`

- Copy the output to your clipboard
- Navigate to [Github GPG and SSH Keys](https://github.com/settings/keys)
- Create a new SSH key, give it a descriptive name, and paste the contents from your clipboard as the key

5. Test
- `ssh -T git@github.com`

6. VSCode
- You should now be able to use github in VSCode or your IDE of your choice. If you decide to use Remote SSH in VSCode, repeat the steps above when SSH’d into the remote machine to connect to your github on the remote machine.


## View this post on my website [here](https://justinsjones.com/remote-ssh-keys/)
