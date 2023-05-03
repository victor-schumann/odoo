# Arxi-Odoo
This repository is a guide that I wrote to myself for when I have to switch machines or reference frequent issues that I was having while learning Odoo.

## Installation and Configuration of ODOO on a Linux Environment (simplified)

Here are the general steps to install and configure Odoo on a Linux environment:

### Odoo

1.  Update and upgrade Linux system.
2. Clone Yenthe's scripted installation repository ([Odoo 16 branch](https://github.com/Yenthe666/InstallScript))
3. Edit the main .sh file to define the proper variables. The most important ones are directory, version, enterprise key. Request an enterprise key to your collaborators.
4. Give permissions to execute the script with `chmod +x script.sh` and execute it by running `./script.sh`.
5. After the script is done, go one level above the target installation folder, and giver permission to the computer user to access the files with `chown -R username:username instalaionfolder`.

### Pycharm
1. Open the Odoo folder as a project.
2. Create parallel to odoo-server, a new directory named 'custom', and inside this directory create three more: addons, servers, and confs. Inside conf create an odoo-server.conf and add the following lines, customizing it to your liking, but remember that the addons path must be specified. Normally, you would add your custom addons, the server addons, and the enterprise addons if you have the key.
3. Configure, in the top right corner, the .py file to run by adding a new python configuration file and changing the fields location and parameters. Change the location to the `odoo-bin` file, and the parameter to `-c path/to/configuration/file/odoo-server.conf`. Still on the configuration editing panel, check “emulate terminal”.
4. Now, in the bottom right corner, click on `Python > Add New Interpreter > Add Local Interpreter`, double-check the base interpreter and the location, and then click 'OK'. 
5. Then go to the python venv terminal with Alt+F12 and locate your requirements.txt. It is usually inside /odoo-server. After locating it, run `pip install -r requirements.txt`. 
6. In a beautiful world, that would be it, but most of the time there will be some incompatibilities. You will have to google the errors and find out the proper pip package versions, and then install them with `pip install package==1.x.x`, for example.
7. Then you are done. Hit run ot press SHIFT+F10 and you are good to create a new database and test Odoo. 

## Observations and suggestions

[//]: # (### On creating a new module)

[//]: # (```)

[//]: # (/home/victor/MEGA/git/odoo-arxi/odoo-server/odoo-bin scaffold custom_module /home/victor/MEGA/git/odoo-arxi/custom/addons/)

[//]: # (```)

<details>
<summary><h3>Reviewing</h3></summary>

It was important for me to review core concepts of Odoo development. The best links that helped me were:

| Description | Link |
| ----------- | ---- |
| Documentation &#40;training&#41; | https://www.odoo.com/documentation/16.0/developer/tutorials/getting_started/01_architecture.html |
| Documentation &#40;training solutions&#41; | https://github.com/odoo/technical-training-solutions/tree/16.0-core |
| Video tutorials &#40;currently incomplete&#41; | https://www.youtube.com/watch?v=r6YLxj3H5xc&list=PLqRRLx0cl0hpu9zH6o8gq6ORBoW5xMtA- |

</details>
<details>
<summary><h3>Troubleshoot</h3></summary>

  When facing problems while developing or learning, it is interesting to have a series of steps that you can follow to troubleshoot. When it comes to Odoo, I tend to do the following when facing problems:

1. Restart Odoo.
2. Check syntax.
3. Verify dependencies, VENV, and "Run/Debug Configurations".
4. Confirm module is in Odoo addons path and `odoo-server.conf`.
5. Check file (`ir.model.access.csv`) and folder (`sudo chown USER$:USER$ folder/to/aquire/permissions`) permissions.
6. Check logs and search online ([Forums](https://www.odoo.com/forum/), [Documentation](https://www.odoo.com/documentation/16.0/), [ChatGPT](https://chat.openai.com)).
7. Disable other modules/customizations to isolate issue.
8. Revert to a working commit and try again.
9. Seek help from the community or workplace.
</details>




<details>
<summary><h3>PostgreSQL</h3></summary>

During my setup, Odoo prompted me to create a PostgreSQL role for my $USER (victor). To do so, first switch to the postgres superuser and double check if you already got a role with your name and the superuser status:

```
sudo su - postgres
psql -U postgres
\du
```

If you do not have a role with the same name as your $USER, you will possibly have to create one. Some available options for the command are:

- `PASSWORD`: Specifies the password for the role. If this option is not used, the role will not have a password and will not be able to log in using a password.
- `SUPERUSER`: Grants superuser privileges to the role.
- `CREATEDB`: Allows the role to create new databases.
- `CREATEROLE`: Allows the role to create new roles.
- `INHERIT`: Allows the role to inherit privileges from other roles.
    
For example, I created one with `CREATE USER <role_name> SUPERUSER;`, and it seems to have solved the problem. For a more detailed explanation, check [this documentation link](https://www.postgresql.org/docs/9.1/sql-createrole.html) or [this blog post](https://severalnines.com/blog/postgresql-privileges-user-management-what-you-should-know/).
</details>

<details>
<summary><h3>Git</h3></summary>

It is important to have a good way of versioning your code. I struggled a bit with it, and there are better ways of doing that, but, in the end, I found this to be an easily replicable solution.

First, create and `git clone <your/repo/ssh-link>` a personal repository to work with your code. Now, to properly copy the folder to your git project, you can use `rsync -av`. rsync is good to copy large amounts of data while preserving file permissions, timestamps, and other attributes (a) and displaying the progress in the terminal (v).

```
rsync -av /path/to/odoo16/project/ /path/to/cloned/git/project/
```

Once it's there you can work with your project. Double check to see if your VENV is still functional, check your Run/Debug configurations, and you are good to go. Whenever you make changes, and want to make a commit, make sure to `git add altered/folders another_altered_file.py` to avoid problems with the Odoo repository.


#### Git cheat sheet (To actually learn Git, check [this](https://git-scm.com/book/en/v2))
##### Initialize a repository
- `git init`: Initialize a git repository in the current working directory
- `git clone <http link>`: Clone a remote repository over 'http'.
- `git clone <http ssh>`: Clone a remote repository over 'ssh'.

##### Basic commands
- `git add .`: Add all files to the staging area.
- `git add <file>`: Add a specific file to the staging area.
- `git commit -m "Commit message"`: Commit the changes to the local repository.
- `git commit -am "message"`: Stage any modified or deleted files that are already being tracked, and commit the changes to the local repository.
- `git commit --amend "New commit message"`: Amend the latest commit with a new message.
- `git log`: Show the commit history.

##### Push and pull with the remote
- `git remote -v `: Show the remote branches and associates URLs.
- `git push`: Push HEAD to the upstream url
- `git push origin master`: Push the changes to the remote repository.
- `git push -u origin master`: Push the changes to the remote repository and set the upstream branch.
- `git pull`: Pull and merge the changes from the remote repository.
- `git fetch`: Fetch the changes from the remote repository.

</details>
