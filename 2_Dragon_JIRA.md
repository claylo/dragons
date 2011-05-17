## Dragon 2: JIRA

If you're following along with Atlassian's guide, [we're at Stage 2](http://confluence.atlassian.com/display/ATLAS/Dragons+Stage+2+-+Install+JIRA).

Since we did a lot of this stuff with Crowd already, here's what we're going to do (briefly):

  * Switch to our atlassian user, go to the Apps directory, and download JIRA.
  * Create a symlink, and install our MySQL driver.
  * Create a data directory, then set up our database & db user.
  * Install JIRA.
  
<script src="https://gist.github.com/941287.js?file=jira_setup.sh"></script>
<script src="https://gist.github.com/941331.js?file=jira.sql"></script>
<script src="https://gist.github.com/941365.js?file=set_jira_home.sh"></script>

Just as before with Crowd, you'd be firing up JIRA right now. But instead, let's lock down JIRA's Tomcat environment to accept connections only from localhost, like we did with Crowd. (Since I walked you through the specifics above, I'll just go straight to the script here.)

One main difference you'll notice in the following script is related to manually patching in your MySQL connection details. The Crowd installation wizard is cool about asking you where you want to store stuff, but the JIRA installation is *not* cool about it. Rather than make you go through the setup, only to export it and re-import it into MySQL when you want to switch, we're going to set it up right now and skip the export/import routine.

<script src="https://gist.github.com/943532.js?file=prep_jira_proxy.sh"></script>

Now we're just about ready to boogie.

### Tying JIRA to Apache

With the good stuff we added to JIRA's server.xml file in the last step, we don't have much to do on the Apache side. Just add the following lines to the `handcrafted.conf` file we created and included during the Crowd installation:

    ProxyPass           / http://localhost:8080/
    ProxyPassReverse    / http://localhost:8080/

... and restart Apache. Then fire up JIRA using the `jira/bin/start-jira.sh` script.

### Set Up JIRA

Hit your proxied & secured hostname (`https://atlassian.example.com/` for this article), and you should get:

<a href="http://www.flickr.com/photos/claylo/5661710389/" title="Your Company JIRA - JIRA Setup by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5227/5661710389_5b8a6e276a.jpg" width="431" height="500" alt="Your Company JIRA - JIRA Setup"></a>

For your visual roadmap, Step 2 looks like this. Might be a good idea to create the "Charlie of Atlassian" account if you're going for the Atlassian Dragons t-shirt.

<a href="http://www.flickr.com/photos/claylo/5661862869/" title="JIRA - JIRA Setup by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5067/5661862869_480edc40f4.jpg" width="500" height="324" alt="JIRA - JIRA Setup"></a>

You'll need to pay attention to your choices on the Mail Setup step. The form is different from the one we just saw during the Crowd setup. 

<a href="http://www.flickr.com/photos/claylo/5662450658/" title="JIRA Setup Step 3 by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5103/5662450658_930d35d8e9.jpg" width="490" height="500" alt="JIRA Setup Step 3"></a>

At ths risk of being overly complete ...

<a href="http://www.flickr.com/photos/claylo/5662484506/" title="JIRA Setup Complete! by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5230/5662484506_4d0976f369.jpg" width="500" height="284" alt="JIRA Setup Complete!"></a>

Once you log in, you'll wind up here:

<a href="http://www.flickr.com/photos/claylo/5662494552/" title="Administration by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5184/5662494552_9a2494a3cc.jpg" width="500" height="447" alt="Administration"></a>

After clicking on General Configuration, you'll get:

<a href="http://www.flickr.com/photos/claylo/5662504998/" title="Administrator Access by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5221/5662504998_5d61d793ca.jpg" width="500" height="447" alt="Administrator Access"></a>

In order to complete the Atlassian Suite setup, you've got to enableAllow Unassigned Issues and Accept Remote API calls. Turn this stuff on by editing the config, which is way down at the bottom of this screen.

<a href="http://www.flickr.com/photos/claylo/5662519824/" title="JIRA Configuration by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5228/5662519824_b623a9cf01.jpg" width="376" height="500" alt="JIRA Configuration"></a>

In addition to the Allow Unassigned Issues and Remote API Calls settings, make sure you have compression turned OFF. If you want your payloads gzipped, do that through Apache, not JIRA.

<a href="http://www.flickr.com/photos/claylo/5662587414/" title="Edit JIRA Configuration by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5263/5662587414_e7784152d0.jpg" width="376" height="500" alt="Edit JIRA Configuration"></a>


### Hook JIRA Up to Crowd

We're on Step 4 of [Dragons Stage 2](http://confluence.atlassian.com/display/ATLAS/Dragons+Stage+2+-+Install+JIRA). We'll configure JIRA to use Crowd for SSO and centralized user management.

  1. If Crowd isn't running, fire it up with `{CROWD_INSTALL}/start_crowd.sh`.
  2. Go to your secured & proxied Crowd URL with your browser, such as `https://atlassian.example.com/crowd`
  3. Login with username **`charlie`** (or whatever admin user you created)
  4. Click on **`Applications`** in the top navigation bar.
  5. When the "Application Browser" appears, click **`Add Application`** in the left menu.
  6. The Crowd **Add Application Wizard** appears. Fill it in as the screen shots indicate:
  
    <a href="http://www.flickr.com/photos/claylo/5668014853/" title="Atlassian Crowd - Add Application by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5222/5668014853_203c734dfd.jpg" width="500" height="306" alt="Atlassian Crowd - Add Application"></a>
    * Application Type: JIRA
    * Name: jira
    * Description: Atlassian JIRA
    * Password: The password JIRA will use to access Crowd. Enter again to confirm. _Make note of this password. It will need to be added to a setup script shortly._
  
    <a href="http://www.flickr.com/photos/claylo/5668614774/" title="Atlassian Crowd - Add Application by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5067/5668614774_eb9dc8c3d5.jpg" width="500" height="306" alt="Atlassian Crowd - Add Application"></a>
    * URL: If you're following along with this tutorial, you should only need to add http://localhost:8080 in this box.
    * Remote IP Address: 127.0.0.1 -- Everything is local for our Atlassian approach.
  
    <a href="http://www.flickr.com/photos/claylo/5668091831/" title="Atlassian Crowd - Add Application by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5189/5668091831_a81c84d039.jpg" width="500" height="306" alt="Atlassian Crowd - Add Application"></a>
    * Directories: You probably only have one directory set up so far. It should be a "Crowd Internal Directory". Check off that box and move on.  
  
    <a href="http://www.flickr.com/photos/claylo/5668679610/" title="Atlassian Crowd - Add Application by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5304/5668679610_fcd2557d31.jpg" width="500" height="306" alt="Atlassian Crowd - Add Application"></a>
    * Allow all users to authenticate: yes. 
    
    <a href="http://www.flickr.com/photos/claylo/5668118033/" title="Atlassian Crowd - Add Application by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5306/5668118033_9450ae5cba.jpg" width="500" height="306" alt="Atlassian Crowd - Add Application"></a>
    * ... and we're done! With this step, anyway.
    
  7. Now, connect JIRA to the Crowd user directory.
    * Go to your JIRA URL, such as https://atlassian.example.com/
    * Click the **Administration** tab, then **User Directories** under _Users, Groups & Roles_.
    * Add a new directory, type "Atlassian Crowd", and click "Next".
    
    <a href="http://www.flickr.com/photos/claylo/5681090857/" title="User Directories - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5064/5681090857_929d8594d8.jpg" width="500" height="362" alt="User Directories - JIRA"></a>
    
    * On the next screen, set the following:
      - Name: Crowd Server
      - Server URL: http://localhost:8095/crowd/
      - Application Name: jira
      - Application Password: [something you record somewhere safe] _Note: this is the password you defined for JIRA in the Crowd 'Add Application' wizard earlier._
      - Crowd Permissions: select Read-Write.
      - Test Settings, and you should be good to go.
      <a href="http://www.flickr.com/photos/claylo/5681107193/" title="Configure Atlassian Crowd Server - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5143/5681107193_6cab7d56fd.jpg" width="500" height="398" alt="Configure Atlassian Crowd Server - JIRA"></a>
  
    * You'll wind up on the 'User Directories' screen. Adjust the order so your Crowd Directory is at the top of the list, which means that your Crowd directory will be searched first for any login attempts.
    * Log out of JIRA so we can adjust its config files.
    <a href="http://www.flickr.com/photos/claylo/5681709212/" title="User Directories - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5190/5681709212_aeda64c5cd.jpg" width="500" height="398" alt="User Directories - JIRA"></a>
    
  * Shut down JIRA, but leave Crowd up and running. You can stop JIRA using the `jira/bin/stop-jira.sh` script.
  * Next, configure the JIRA property files for Single Sign-On with Crowd by running the following commands from your `atlassian` user's Apps directory. **NOTE:** The last command requires the use of the JIRA app password you defined previously.
  <script src="https://gist.github.com/947691.js?file=jira_crowd_prep.sh"></script>
  * Now you have SSO between JIRA and Crowd! Make sure you start up JIRA again using `jira/bin/start-jira.sh`.
    
### Set up a Project & Create your JIRA Dashboard

In this step you'll create some data in JIRA, including a project and an issue, for use in subsequent stages of this integration process. Then you'll create your own JIRA dashboard with a couple of gadgets.

  1. Create a project in JIRA.
    - Add a project.
      
      <a href="http://www.flickr.com/photos/claylo/5681771130/" title="Projects - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5149/5681771130_ff5ae457e9.jpg" width="500" height="378" alt="Projects - JIRA"></a>
    
    - Fill in the New Project form.
      
      <a href="http://www.flickr.com/photos/claylo/5681238965/" title="Create New Project - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5110/5681238965_83f14b2734.jpg" width="500" height="481" alt="Create New Project - JIRA"></a>
     
  2. Add two versions (1.0 and 2.0)
    - On the Project Detail screen, click Versions to edit.
      <a href="http://www.flickr.com/photos/claylo/5681246231/" title="Project: Dragons - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5225/5681246231_734bd378be.jpg" width="500" height="481" alt="Project: Dragons - JIRA"></a>
    
    - Add versions 1.0 and 2.0
      
      <a href="http://www.flickr.com/photos/claylo/5681816894/" title="Manage Versions - Jexy JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5223/5681816894_8ee21030c6.jpg" width="500" height="481" alt="Manage Versions - Jexy JIRA"></a>
      
      Here's how it looks with two versions in place.
      
      <a href="http://www.flickr.com/photos/claylo/5681341023/" title="Manage Versions - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5106/5681341023_0665731bfc.jpg" width="500" height="481" alt="Manage Versions - JIRA"></a>
      
  3. Add an issue to your project.
    * Click 'Create Issue' at the top right of your JIRA screen.
      
      <a href="http://www.flickr.com/photos/claylo/5681837242/" title="Manage Versions - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5224/5681837242_d09d8b287d.jpg" width="500" height="478" alt="Manage Versions - JIRA"></a>
      
    * Select Project Dragons and Issue Type of Bug, then click 'Create'.
     
      <a href="http://www.flickr.com/photos/claylo/5681843768/" title="Manage Versions - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5061/5681843768_a4382c0ec8.jpg" width="322" height="213" alt="Manage Versions - JIRA"></a>
  
    * Enter the following information about your new issue, then click 'Create'.
      - Summary: Dragon slayer's equipment is defective.
      - Affects Version/s: 1.0
      - Assignee: Charlie of Atlassian (Click 'Assign to me')
      - Description: There's a hole in the dragon slayer's water bucket.
      - Original estimate: 1d
      
    <a href="http://www.flickr.com/photos/claylo/5681861010/" title="Create Issue - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5225/5681861010_918cba6be5.jpg" width="446" height="500" alt="Create Issue - JIRA"></a>
      
      
    * You now have an issue with a key of 'DRA-1'.
    
      <a href="http://www.flickr.com/photos/claylo/5681865138/" title="[#DRA-1] Dragon slayer's equipment is defective - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5028/5681865138_e09c686f4d.jpg" width="499" height="500" alt="[#DRA-1] Dragon slayer's equipment is defective - JIRA"></a>
      
  4. Create a new dashboard for your dragon-related tasks.
    * Click 'Dashboards' at the top left of your JIRA screen.
    * Select 'Create Dashboard' from the Tools menu.
    
      <a href="http://www.flickr.com/photos/claylo/5681888054/" title="System Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5189/5681888054_7903cb2bfb.jpg" width="499" height="500" alt="System Dashboard - JIRA"></a>
      
    * Fill in the form Name and Description, and then click Add.
    
      <a href="http://www.flickr.com/photos/claylo/5681899252/" title="Create New Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5146/5681899252_ccb4a1b78f.jpg" width="499" height="500" alt="Create New Dashboard - JIRA"></a>
      
  5. Now you have a new, empty dashboard. Let's add some stuff to it.
    
    <a href="http://www.flickr.com/photos/claylo/5681358059/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5230/5681358059_5ea30f367e.jpg" width="499" height="500" alt="Dragon Dev Dashboard - JIRA"></a>
    
    * Click 'Add Gadget'
    * The 'Gadget Directory' will appear. Find the 'Projects' gadget, and click 'Add it Now'.
      
      <a href="http://www.flickr.com/photos/claylo/5681365375/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5226/5681365375_72b7b92212.jpg" width="499" height="500" alt="Dragon Dev Dashboard - JIRA"></a>
      
    * Now add the "Assigned to Me" gadget the same way
      
      <a href="http://www.flickr.com/photos/claylo/5681371053/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5106/5681371053_50a9ce630f.jpg" width="499" height="500" alt="Dragon Dev Dashboard - JIRA"></a>
      
    * ... then click "Finished"
    
  6. Configure your gadgets
    * Drag your "Assigned to Me" gadget to the right column. Click "Save"
    * Set your "Projects" gadget to show the "Dragons" project. Click "Save".
    * When finished, your Dragon Dev Dashboard will look similar to this:
      
      <a href="http://www.flickr.com/photos/claylo/5681380651/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5190/5681380651_919bf2cbed.jpg" width="499" height="500" alt="Dragon Dev Dashboard - JIRA"></a>
      
**BOOM!** Dragons Step 2 done.
