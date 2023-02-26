# Remote Deploy with SSH
To deploy your code automatically when pushing to the master/main/prod branch, we need to set up a server with SSH access, enable pulling code via SSH and a github action script (as a `.yml` file) that wil run every time a PR is merged.

## 1.  Generate Keys
On your own machine (not the server) run the following (for full explanation see the github docs [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent):
```
ssh-keygen -t ed25519 -C "your_email@example.com"
```
and follow the instructions to name the file
```
Enter a file in which to save the key (/home/YOU/.ssh/ALGORITHM):[Press enter]
```
and  press enter to skip the passphrase
```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

## 2. Inspect Keys
The **PRIVATE** key will be in a file named `id_ed25519` (if you picked the default) without a file ending and will look something like this:
```
-----BEGIN OPENSSH PRIVATE KEY-----
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
-----END OPENSSH PRIVATE KEY-----
```

The **PUBLIC** key will be in a file named `id_ed25519.pub` (if you picked the default) with a `.pub` file ending and will look something like this:
```
ssh-ed25519 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX your_email@example.com
```

## 3. Server Authorised Files
SSH into your server and create a new file in the hidden SSH folder. You may need to check that SSH is installed and that the SSH agent is running [see here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent). Create the file using:
```
vim ~/.ssh/authorized_keys
```
and paste the **public** key at the top of the file. Then exit the server.

## 4. Add Github Secrets
Take the **private** key and add it as a secret named `DEPLOY_SSH_KEY` (full details on how to do that [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)).  You may also want to add the additional secrets for `DEPLOY_SSH_HOST` i.e. the host IP or URL and the `DEPLOY_SSH_USER` (usually root).

## 5. Create a Github Action
There are many actions on the marketplace to choose from, some use ssh directly but can be quite complex while others use docker and take a little longer to spool up.  This is a simple one to use:
```
name: CI
on:
  push:
    branches:
        - master
jobs:
  deploy:
    name: "Deploy"
    runs-on: ubuntu-latest
    steps:
      - name: Remote SSH Commands
            # You may pin to the exact commit or the version.
            # uses: fifsky/ssh-action@58b3c484be9c20cf118fd3b939a6d2cb3c769512
        uses: fifsky/ssh-action@v0.0.6
        with:
            command: |
                    cd /root/ 
                    ls -a          
                    # ADD MORE COMMANDS HERE OR REPLACE ABOVE
            host: ${{ secrets.DEPLOY_SSH_HOST }}
            user: ${{ secrets.DEPLOY_SSH_USER }}
            key:  ${{ secrets.DEPLOY_SSH_KEY }}
```
**Note**: you will need to add your own commands to deploy your code, if you are pulling from github you will also need to set up SSH deploy keys to be able to pull code from github without entering a password (guide available [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys)).
