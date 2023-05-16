<div style='text-align: justify;'>

# Arxi-Odoo

This repository is a guide that I wrote to myself for when I have to switch machines or reference frequent issues that I
was having while learning Odoo.

## Installation and Configuration of ODOO on a Linux Environment (simplified)

Here are the general steps to install and configure Odoo on a Linux environment:

### Odoo

1. Update and upgrade Linux system.
2. Clone Yenthe's scripted installation repository (<a href="https://github.com/Yenthe666/InstallScript" target="_blank">Odoo 16 branch</a>)
3. Edit the main .sh file to define the proper variables. The most important ones are directory, version, enterprise
   key. Request an enterprise key to your collaborators. Here is an example with my changes:
```
OE_USER="odoo"
OE_HOME="/home/your_username/odoo/odoo16"
OE_HOME_EXT="/home/your_username/odoo/odoo16/${OE_USER}-server"
```
4. Give permissions to execute the script and run it with:
```
chmod +x script.sh
./script.sh
```
5. After the script is done, go one level above the target installation folder, and give permission to the computer
   user to access the files with:
```
sudo chown -R username:username instalation_folder
```

### Pycharm
A part of the bellow steps are suggestions, since different developers organize theri Odoo filesystem differently. Feel free to pick a different approach!
1. Open the Odoo folder as a project.
2. Create parallel to odoo-server, a new directory named 'custom', and inside this directory create three more folders: addons,
   servers, and configs. Inside config, create an odoo-server.conf and add the following lines, customizing it to your
   liking, but remember that the addons path must be specified. Normally, you would add your custom addons, the server
   addons, and the enterprise addons if you have the key.
```
[options]
admin_passwd = test
db_user = your_username
db_password =
http_post = 8069
limit_time_real = 6000
logfile = /var/log/odoo/odoo-server.log
addons_path = /home/your_username/odoo/odoo16/enterprise/addons,/home/your_username/odoo/odoo16/odoo-server/addons,/home/your_username/odoo/odoo16/custom/addons
```
3. Configure, in the top right corner, the .py file to run by adding a new python configuration file and changing the
   fields location and parameters. Change the location to the `odoo-bin` file, and the parameter
   to `-c path/to/configuration/file/odoo-server.conf`. Still on the configuration editing panel, check “emulate
   terminal”. Here is an image to illustrate:
   
<p align="center"> 
<img align="center" src="https://i.imgur.com/zrGpvAK.png" alt="Odoo Instalation PNG #1" height="500" width="auto"/>
</p>
   
4. Now, in the bottom right corner, click on `Python > Add New Interpreter > Add Local Interpreter`, double-check the
   base interpreter and the location, and then click 'OK'.

> **ATTENTION:** To follow the next steps, be absolutely sure that you are inside the VENV when operating with pip and
> the terminal.

5. On the python venv terminal (Alt+F12) and locate your requirements.txt. It is usually inside /odoo-server.
   After locating it, run:
```
pip install -r requirements.txt
```
6. In a beautiful world, that would be it, but sometimes of the time there will be some incompatibilities with the pip
   packages. You will have to google the errors and find out the **proper pip package versions for odoo** of the bad package, and then install them
   with `pip install package==1.x.x`, for example. Refer ot the requirements.txt file, github issues, stackoverflow, and
   odoo forums.
7. Then you are done. Hit run (SHIFT+F10) and you are good to create a new database and test Odoo. Bookmark http://localhost:8069/web/database/manager and http://localhost:8069/web/ for future reference. When creating a database, use the same password as defined during the script. The necessary fields are:
   - master password;
   - database name;
   - email;
   - password;
   - language. 

### Database creation Error: A postgreSQL side note

```
Database creation error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL: role "your_username" does not exist
```
If you used my config file, Odoo will prompt you to create a PostgreSQL role for your $USER (your_username). To do so, follow the next steps.

First switch to the postgres superuser and double check if you already got a role with your name and the superuser status:

```
sudo -u postgres psql
\du
```

The above commands put you inside the postgres user command line, start the interactive terminal commonly known as psql with the user postgres, and list all the roles (press `q` to quit the user screen).

Analyze the screen and check if you already got a user in PostgreSQL with the same name as your username. If you do not have, you will possibly have to create one. Some available options
for the command are:

- `PASSWORD`: Specifies the password for the role. If this option is not used, the role will not have a password and
  will not be able to log in using a password.
- `SUPERUSER`: Grants superuser privileges to the role.
- `CREATEDB`: Allows the role to create new databases.
- `CREATEROLE`: Allows the role to create new roles.
- `INHERIT`: Allows the role to inherit privileges from other roles.

For example, I created one with:
```
CREATE USER your_user_name SUPERUSER;
```
And it seems to have solved the problem. For a more detailed explanation, check [this documentation link](https://www.postgresql.org/docs/9.1/sql-createrole.html)
or [this blog post](https://severalnines.com/blog/postgresql-privileges-user-management-what-you-should-know/).

However, on a second note, if you get the same error above despite having already created a role, you might have to try a different approach. The same goes if you are getting the following error:
```
psql: could not connect to server: No such file or directory
Is the server running locally and accepting connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432?
```
The best to do in such cases is a complete purge of your postgreSQL version with `sudo apt purge postgresql-XY` (XY = your version number), and then reinstall it with `sudo apt install postgresql-XY`. This will delete all your databases, configurations and folders related to PostgreSQL, so if there is anything important there, make sure to have a backup.

Please reference the below optional suggestions for further configuration!

## Observations and suggestions

<details>
<summary><h3>How to create a new Odoo Module</h3></summary>
Before we begin, make sure you have a functional clean database (or with the dummy data), install one of the odoo modules (like Contacts), and activate the Developer Mode on settings. After doing that, you are ready to go.

1. Use the command `/path/to/odoo-server/odoo-bin scaffold custom_module_name /path/to/custom/addons/`. This will create all the basic files for you so set up a new Odoo Module. 
2. Go to your pycharm configurations in the top right corner of the IDE and add the _-u parameter_ when running the server, to upgrade your module everytime you compile the code. `-c /path/to/configs/odoo-server.conf -u custom_module_name,custom_module_name2`.
3. Go to your `/models/__init__.py` file and make sure the models.py is properly imported. If you do not plan on using models.py, this is a good moment to rename the file and properly import it instead of models.py.
4. Start the server and install the module by going to Apps > Update Apps List > Search bar (search for your custom module name) > Activate. Make sure to delete anu default filters, like "Apps" or "Installed".
5. You successfully compiled and installed your odoo module!
6. Now go to your models.py file (or your custom) and create the barebones of your first python class. For example, let's imagine I want to create a class that manages cars. My python file will look like this:
```
# -*- coding: utf-8 -*-
from odoo import models, fields, api


class Car(models.Model):
    _name = 'car.car'
    _description = 'Cars'

    name = fields.Char(string='Name')
```
7. If you did everything correctly, you should see a strange warning inside your terminal. It should look like this:
```
2023-05-15 15:18:53,930 39676 WARNING test odoo.modules.loading: The models ['car.car'] have no access rules in module custom_module0, consider adding some, like:
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
custom_module0.access_car_car,access_car_car,custom_module0.model_car_car,base.group_user,1,0,0,0 

```
8. That is the part where we thank the Odoo gods and add access rules to our model. Begin by uncommenting the _security_ line in the manifest file. Then, go to the .csv file in the security folder, and add the lines that Odoo suggested, except instead of '1,0,0,0', we are setting full permissions to alter, add, and delete files. In other words, your final security file should have only the following lines:
```
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
custom_module0.access_car_car,access_car_car,custom_module0.model_car_car,base.group_user,1,1,1,1
```
   When adding future models/classes to _the same module_, remember to simply copy the new line given by odoo, and adding it yo your original file. For example, if I created a new Car2 model _on the same module_, my security would look like this:
```
id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
custom_module0.access_car_car,access_car_car,custom_module0.model_car_car,base.group_user,1,1,1,1
custom_module0.access_car2_car2,access_car2_car2,custom_module0.model_car2_car2,base.group_user,1,1,1,1
```
9. The only exception is inheritance, which we deal with by using the dependencies section in the manifest (sometimes).
10. Remember that you also need to add lines to the manifest if you add new .xml files.
11. Enjoy the new module!



</details>

<details>

<summary><h3>Reviewing Odoo concepts</h3></summary>

It was important for me to review core concepts of Odoo development. The best links that helped me were:

| Description                                    | Link                                                                                             |
|------------------------------------------------|--------------------------------------------------------------------------------------------------|
| Documentation &#40;training&#41;               | https://www.odoo.com/documentation/16.0/developer/tutorials/getting_started/01_architecture.html |
| Documentation &#40;training solutions&#41;     | https://github.com/odoo/technical-training-solutions                                             |
| Video tutorials &#40;currently incomplete&#41; | https://www.youtube.com/watch?v=r6YLxj3H5xc&list=PLqRRLx0cl0hpu9zH6o8gq6ORBoW5xMtA-              |

</details>

<details>
<summary><h3>Troubleshoot</h3></summary>

When facing problems while developing or learning, it is interesting to have a series of steps that you can follow to
troubleshoot. When it comes to Odoo, I tend to do the following when facing problems:

1. Restart Odoo.
2. Check syntax.
3. Verify dependencies, VENV, and "Run/Debug Configurations".
4. Confirm module is in Odoo addons path and `odoo-server.conf`.
5. Check file (`ir.model.access.csv`) and folder (`sudo chown -R USER$:USER$ folder/to/aquire/permissions`) permissions.
6. Check logs and search
   online ([Forums](https://www.odoo.com/forum/), [Documentation](https://www.odoo.com/documentation/16.0/), [ChatGPT](https://chat.openai.com)).
7. Disable other modules/customizations to isolate issue.
8. Revert to a working commit and try again.
9. Seek help from the community or workplace.

</details>

<details>
<summary><h3>Git</h3></summary>

It is important to have a good way of versioning your code. I struggled a bit with it, and there are better ways of
doing that, but, in the end, I found this to be an easily replicable solution.

First, create and `git clone <your/repo/ssh-link>` a personal repository to work with your code. Now, to properly copy
the folder to your git project, you can use `rsync -av`. rsync is good to copy large amounts of data while preserving
file permissions, timestamps, and other attributes (a) and displaying the progress in the terminal (v).

```
rsync -av /path/to/odoo16/project/ /path/to/cloned/git/project/
```

Once it's there you can work with your project. Double check to see if your VENV is still functional, check your
Run/Debug configurations, and you are good to go. Whenever you make changes, and want to make a commit, make sure
to `git add altered/folders another_altered_file.py` to avoid problems with the Odoo repository.

#### Git cheat sheet (To actually learn Git, check [this](https://git-scm.com/book/en/v2))

##### Initialize a repository

- `git init`: Initialize a git repository in the current working directory
- `git clone <http link>`: Clone a remote repository over 'http'.
- `git clone <http ssh>`: Clone a remote repository over 'ssh'.

##### Basic commands

- `git add .`: Add all files to the staging area.
- `git add <file>`: Add a specific file to the staging area.
- `git commit -m "Commit message"`: Commit the changes to the local repository.
- `git commit -am "message"`: Stage any modified or deleted files that are already being tracked, and commit the changes
  to the local repository.
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

[//]: # (### On creating a new module)

[//]: # (/home/victor/MEGA/git/odoo-arxi/odoo-server/odoo-bin scaffold custom_module /home/victor/MEGA/git/odoo-arxi/custom/addons/)




</div>
