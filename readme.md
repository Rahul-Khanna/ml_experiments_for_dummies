### How to set up a Virtual Machine for ML Experimentation for non-Computer-Scientists on Google's Compute Engine Platform

* Note: Some of the terminal/command prompt commands might not work on a Windows machine. I've tried to include Windows commands as well, but I'm not well versed in them. Hopefully the commands are clear enough that someone used to using the Windows Command Prompt can figure out the equivalent commands.

* Note: I am assuming some level of familiartiy with python and using terminal/command prompt

### Contents:
* [Creating your virtual machine](#creating-your-virtual-machine)
* [Connecting to your virtual machine for the first time](#connecting-to-your-virtual-machine-for-the-first-time)
* [Connecting to your machine in general](#connecting-to-your-machine-in-general)
* [Setting up your ML Environment on your Virtual Machine](#setting-up-your-ml-environment-on-your-virtual-machine)
* [Setting up Jupyter Notebook on your Virtual Machine](#setting-up-jupyter-notebook-on-your-virtual-machine)

### Creating your virtual machine:
1. Go to https://console.cloud.google.com and sign in to your Google Account
2. Create a project and name the project appropriately. Either google will prompt you do to this or click on the button just to the right of the "Google Cloud Platform" text in the upper left corner––you should be able to from the resulting pop-up
3. Hit the three-lined button on the top left side of the page
    * Find the phrase "IAM & admin" and hover over it
    * Click on Quotas
    * Click on the button under "Metric" and hit None
    * Then type in the seach bar (in that same menu) "GPUs (all regions)"
    * Now request one GPU from Google, you'll have to fill out some information, unfortunately. Do so though. A good reason to put down is "ML Practioner trying to practice deep learning techniques"
    * **Pause** in your setup process until Google sends you back an approval email. This should take no more than one day.
4. Now that Google has approved your request, follow step 1's instructions, but proceed to step 5 (the next step)
5. Select the project you created in step 2
6. Find the search bar on the top of your screen and type in "deep learning vm" and click on the result that pops up
7. Hit the "Launch On Compute Engine" button
8. Where it says *Deployment name* you can name your machine
9. Under *Zone*, just go with whatever they give you baring its the correct coast. If you are on the east coast the zone should reflect that. If you'd like, you can google "how to pick zone on google cloud engine" and that should give you an idea of pricing per zone.
10. You can play around with the *Machine Type*, up to "8 vCPUs with 32 GBs" of memory––if you go above this you will then need more than one GPU
11. Under *GPUs* you can play around with *GPU type*, just note the cost in the top right corner as you play around with *Machine Type* and *GPU type*
12. Select the Framework you would to like to use, this step doesn't really matter as you can easily set up any framework on the machine. This is just Google making your life easy by installing the frameworks for you. I personally use PyTorch, but you can use whatever you'd like.
13. Click the "Install NVIDIA GPU driver..." checkbox
14. Again you can play around with the options under *Boot Disk*, but you also can lave it as is.
15. Finally you hit "deploy"
    * If there are warnings, don't worry about them
    * If there are errors, I would suggest googling the errors

### Connecting to your virtual machine for the first time:
1. Once the machine is deployed, hit the three-lined button on the top left of the screen and find "Compute Engine". Click on it.
2. You will now see the machine you deployed in the middle of the screen
3. There are many ways to connect to the machine, but to begin with find the word *SSH* and click on it. That should open a new screen.
    * This is one way that you can connect to your machine. However, to fully use your machine you're going to want to follow the steps below.
4. In this new screen (which now acts as your connection to your virtual machine) run the following command: `cd .ssh`
5. Run the following command: `vim authorized_keys`
6. Now switching back to your **own** computer open up terminal/command prompt
7. Run the following command: `cd .ssh`
    * if this fails please run the following command: `mkdir .ssh`
    * then run: `cd .ssh`
8. Run the following command to create a "ssh key": `ssh-keygen -t rsa`
    * Name your key: `google_cloud_key`
    * You don't have to set a passphrase for your key, but I do
9. Now run `chmod 400 google_cloud_key*`
    * Makes your keys read only
10. Now run `ls` or `dir` (windows) and you should see these two files:
    * google_cloud_key
    * google_cloud_key.pub
11. NEVER, EVER, EVER open `google_cloud_key`, as soon as you do the key is destroyed and you will have to redo this process from step 8
12. Run the following command: `cat google_cloud_key.pub` or `type google_cloud_key.pub` (windows) and copy the output, it should start with `ssh-rsa`
13. Now switch back to your **virtual machine** where you opened up *authorized_keys*:
    * hit **i**
    * make room at the top of the file (if something else is already there) by hitting *enter* 
    * paste what you copied from your **own** computer into *authorized_keys* (ctrl+v)
    * hit the *esc* key on your computer
    * hit the *:* key on your computer
    * type *wq*
    * hit enter
14. You have now added your public key to your machine allowing you to connect to your machine from your own computer––i.e. skipping the google cloud console.
15. To connect from your own machine, **close** your current connection to your **virtual machine** (i.e. close the screen you were just playing around on), switch back to your **own** machine and do the following:
    * `ssh-add ~/.ssh/google_cloud_key` (the `~` command might not work on Windows, so please just write out the full path to your newly created `.ssh` folder––from step 7)
    * `ssh -i ~/.ssh/google_cloud_key your_google_cloud_user_name@your_machines_external_ip`
        - `your_google_cloud_user_name` = the part of your email before the @ sign
        - `your_machines_external_ip` = the numbers and periods found on the google compute engine dashboard under the heading *External IP*

### Connecting to your machine in general:
1. So you can always just navigate to the Google Compute Engine Screen and hit the *SSH* button described above.
2. Or once you've succesfully added your key to your virtual machine, you can always run these commands:
    * `ssh-add ~/.ssh/google_cloud_key` (you don't always need to run this, but in case you see an error, run this and the error should go away)
    * `ssh -i ~/.ssh/google_cloud_key your_google_cloud_user_name@your_machines_external_ip`

### Setting up your ML Environment on your Virtual Machine:
1. Hit the green *Clone or download* button on this page and hit *Download ZIP*. Unzip the folder.
    * Make a note as to where you've saved this unzipped folder.
2. Now switch back to your virtual machine
2. Run the following command to download an important package: `sudo apt-get install python3-venv`
3. Run the following command: `python3.5 -m venv clouds`
    * You have now created a virtual environment called *clouds*. This is important when it comes to replication of your work.
4. Run the following command to start your virtual enviroment: `source clouds/bin/activate`
    * You should now see the words (clouds) on your screen
5. Copy the downloaded *rqs.txt* file to your machine
    * If you have connected to your instance via the *SSH* button on the Google Compute Engine Dashboard find the settings button in top right portion of your screen (the button that looks like a wheel). Find the *upload file* option, and upload the provided "rqs.txt" file
    * If you have connected via your command prompt/terminal, open a new tab in your command prompt/terminal and navigate to where you saved the unzipped folder. Then run the following command:

        `scp -i ~/.ssh/google_cloud_key rqs.text your_google_cloud_user_name@your_machines_external_ip:/home/your_google_cloud_user_name`
        - You can use this `scp` command to copy any file on **your** machine to your **virtual machine** and vice-versa
        - To copy from your machine to your virtual machine:
            * The format is as follows: 

                `scp -i (what ssh key to use) (filename) (username@external_ip):/path_where_you'd_like_to_save_your_file`
                *  In the above case:
                    + `what ssh key to use` = google_cloud_key
                    + `filename` = rqs.txt
                    + `path_where_you'd_like_to_save_your_file` = home/your_google_cloud_user_name
        - To copy from your virtual machine to your machine:
            * The format is as follows: 

                `scp -i (what ssh key to use) (username@external_ip):/path_where_your_file_is/(filename) path_where_you'd_like_to_save_the_file_on_your_own_machine`
6. Run the following command: `pip install -r rqs.txt`
    * This will now install all needed python packges for machine learning
    * Don't worry about any errors/warnings, as along as it the end of the output the screen lists out all the packages that have been installed

### Setting up Jupyter Notebook on your Virtual Machine
1. Connect to your virtual machine
2. Start up your virtual environment, `source clouds/bin/activate`
3. Run the following command: `jupyter notebook --no-browser --port=8887`
4. Now switch back to your **own** machine and run the following command: 

    `ssh -i ~/.ssh/google_cloud_key -N -L localhost:8888:localhost:8887 your_google_cloud_user_name@your_machines_external_ip`
5. Now open your browser and type in *localhost:8888*
6. It will ask you for a token, go back to your **virtual machine** screen and find the following line on the screen `http://localhost:8887/?token=*************************************`
7. Copy everything after the `=` sign and paste that into the text box on the screen asking you for a token
