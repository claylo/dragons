## Dragon 4: Install Confluence

In this stage, we'll install Confluence, hook it up to Crowd & JIRA. By the end, we'll have a Confluence activity stream in the JIRA dashboard.

The steps to get Confluence up and running aren't all that different from JIRA and Crowd from a mechanics standpoint, so we'll rely less on screenshots and step-by-stp instructions, and more on scripts that we can use to blow through it. You trust me by now, right?

Here's what we'll do:

  * Switch to atlassian user, go to the Apps directory, and download Confluence.
  * Create a symlink and install our MySQL driver.
  * Create a data directory, then set up the database & db user
  * Get Apache ready for proxying Confluence.
  * Install Confluence
  * Hook it up to Crowd & JIRA
  
### Step 1: Prepare to Install Confluence

Download & Setup Confluence:
<script src="https://gist.github.com/954255.js?file=confluence_setup.sh"></script>

Create our MySQL database & user:
<script src="https://gist.github.com/954262.js?file=confluence.sql"></script>

Tell Confluence about our confluence.home directory:
<script src="https://gist.github.com/954292.js?file=set_confluence_home.sh"></script>

Get Apache and Tomcat for Confluence ready:
<script src="https://gist.github.com/954387.js?file=prep_confluence_proxy.sh"></script>

.... restart Apache, then start Confluence with `confluence/bin/startup.sh`.

### Step 2: Set Up Confluence

If all went according to plan in the last step, you should be able to load up the Confluence Setup Wizard at https://atlassian.example.com/wiki/ (with your own hostname, of course).

_If you don't see the Confluence Setup Wizard, make sure that your `handcrafted.conf` file has the default `/` ProxyPass and ProxyPassReverse directives **after** the `/wiki` directives._

<a href="http://www.flickr.com/photos/claylo/5719508942/" title="Confluence Setup Wizard - Confluence by claylo, on Flickr"><img src="http://farm4.static.flickr.com/3019/5719508942_70387cf029.jpg" width="500" height="479" alt="Confluence Setup Wizard - Confluence"></a>

Upon choosing to set up as a 'Production Installation', you'll get to the database configuration screen.

<a href="http://www.flickr.com/photos/claylo/5719106009/" title="Choose a Database Configuration - Confluence by claylo, on Flickr"><img src="http://farm4.static.flickr.com/3625/5719106009_12ed64b91a.jpg" width="500" height="479" alt="Choose a Database Configuration - Confluence"></a>

After selecting MySQL from the previous screen, you'll want to select "Direct JDBC" as the connection method.

<a href="http://www.flickr.com/photos/claylo/5719121639/" title="Configure Database - Confluence by claylo, on Flickr"><img src="http://farm3.static.flickr.com/2052/5719121639_df3ef8e78f.jpg" width="500" height="188" alt="Configure Database - Confluence"></a>

Then you'll finally get to actually configure the MySQL connection using the credentials we created previously for Confluence.

<a href="http://www.flickr.com/photos/claylo/5719689708/" title="Configure Database - Confluence by claylo, on Flickr"><img src="http://farm4.static.flickr.com/3574/5719689708_6a61ac0b17.jpg" width="500" height="479" alt="Configure Database - Confluence"></a>

If you're following along with the Atlassian Dragons steps to the letter, you'll want to click the Example Site content setup.

<a href="http://www.flickr.com/photos/claylo/5719693352/" title="Load Content - Confluence by claylo, on Flickr"><img src="http://farm3.static.flickr.com/2750/5719693352_0111456198.jpg" width="500" height="479" alt="Load Content - Confluence"></a>

Now set up the "Charlie of Atlassian" user account, as we've done with previous components.

<a href="http://www.flickr.com/photos/claylo/5719706236/" title="Setup System Administrator - Confluence by claylo, on Flickr"><img src="http://farm4.static.flickr.com/3337/5719706236_e95b55647f.jpg" width="500" height="479" alt="Setup System Administrator - Confluence"></a>

... and we're done. Don't worry about the Atlassian Dragons step (if you're following it closely) about switching the configuration away from `http://localhost:8090`, etc. That doesn't apply to how we've done things to far.

<a href="http://www.flickr.com/photos/claylo/5719715294/" title="Confluence Setup Successful - Confluence by claylo, on Flickr"><img src="http://farm3.static.flickr.com/2321/5719715294_4f8c290ccc.jpg" width="500" height="290" alt="Confluence Setup Successful - Confluence"></a>

### Step 3: Hook Confluence to Crowd

In this step you'll configure Confluence to use Crowd for SSO and centralized user management. It'll be similar to what we did previously with JIRA.

  1. If Crowd isn't running, fire it up with `{CROWD_INSTALL}/start_crowd.sh`
  2. Go to your secured & proxied Crowd URL and log in with username **`charlie`** (or whatever admin user you created).
  3. Click **`Applications`** in the top navigation bar.
  4. When the "Application Browser" appears, clikc **`Add Application`** in the left menu.
  
  [image]
  5. The Crowd Add Application Wizard appears. Fill it in as the screen shots indicate:
  
    * Application Type: Confluence
    * Name: confluence
    * Description: Atlassian Confluence
    * Password: The password Confluence will use to access Crowd information. Enter again to confirm. _(You'll use this in the setup script in a few minutes.)_
    
  [image]
  6. The connection tab will ask for the Confluence URL and remote IP. 
    
    * URL: If you're following along with this tutorial, you should only need to add `http://localhost:8080` in this box.
    * Remote IP Address: `127.0.0.1` -- Everything is local for our Atlassian approach.
    
  [image]
  7. In the `Directories` tab, select the Crowd directory you created in Step 1.

  8. 
  
<script src="https://gist.github.com/972590.js?file=confluence_crowd_prep.sh"></script>

  10. Now start Confluence back up! with `confluence/bin/startup.sh`

### Step 4: Get JIRA and Confluence Talking


IF YOU RUN INTO PROBLEMS GETTING JAVA talking to your certificate (perhaps if you installed a real cert instead of a self-signed one?) follow these instructions for installing the cert in Tomcat 6.
http://help.godaddy.com/article/5239
