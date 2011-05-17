## Dragon 1: Crowd

If you're following along with Atlassian's guide, [we're just getting started](http://confluence.atlassian.com/display/ATLAS/Dragons+Stage+1+-+Install+Java%2C+PostgreSQL+and+Crowd).

You don't need to worry about installing Java, as a suitable Java is already installed on Mac OS X Server. You also don't need to worry about installing PostgreSQL, since we can make do with MySQL that's already installed (which we configured in the previous step).

**Note:** In this step, we're also going to pull down the MySQL Java connector, which Atlassian doesn't include by default in every one of its apps. We'll need it multiple times, so make sure to grab it now.

Here we go:

<script src="https://gist.github.com/918865.js?file=crowd_setup.sh"></script>

Before we get into running the installation sequence, let's create a MySQL database for Crowd to use, and special database user for Crowd. Connect to MySQL as a privileged user, then:

<script src="https://gist.github.com/918871.js?file=crowd.sql"></script>

### Setting up Crowd

First, tell the Crowd installation (aka `{CROWD_INSTALL}`) where the Crowd data  directory (aka `crowd.home`) lives. The file that needs editing is `{CROWD_INSTALL}/crowd-webapp/WEB-INF/classes/crowd-init.properties`. You can use a text editor, or edit in one shot:

<script src="https://gist.github.com/918927.js?file=set_crowd_home.sh"></script>

In the Atlassian guide, you'd be firing up Crowd now. You can do that, if you like -- but, for me, I'd rather go ahead and get it running under Apache. 

First, lock down Crowd's Tomcat environment to accept connections only from localhost. There are two things that are necessary to do this:

1. In {CROWD_INSTALL}/apache-tomcat/conf/context.xml, add:
`<Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.0\.0\.1"/>`

2. In {CROWD_INSTALL}/apache-tomcat/bin/setenv.sh, add:
`-Djava.net.preferIPv4Stack=true` just before `$JAVA_OPTS`

(Some JVMs are buggy around IPv6, and since we just want to lock down localhost, IPv4 is perfectly adequate. I couldn't get my current setup working with IPv6 and filtering out non-localhost connections, YMMV.)

Need to set up across more than _just_ localhost? If you've got multiple servers on your private network and want to proxy across them, you can add additional comma-separated IPs, like this:

    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.0\.0\.1,10\.0\.1\.20">

Next you need to prepare the Tomcat container for Crowd to expect to be proxied. For that, we add `proxyName` and `proxyPort` properties to the `Connector` element in {CROWD\_INSTALL}/apache-tomcat/conf/server.xml. Find this chunk:

    <Connector acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" enableLookups="false" maxHttpHeaderSize="8192" maxSpareThreads="75" maxThreads="150" minSpareThreads="25" port="8095" redirectPort="8443" useBodyEncodingForURI="true"/>
     
And change it to:

    <Connector proxyName="atlassian.example.com" proxyPort="443" acceptCount="100" connectionTimeout="20000" disableUploadTimeout="true" enableLookups="false" maxHttpHeaderSize="8192" maxSpareThreads="75" maxThreads="150" minSpareThreads="25" port="8095" redirectPort="8443" useBodyEncodingForURI="true"/>

**_PLEASE remember to use YOUR hostname, not atlassian.example.com!_**

You can run the following commands to get all this done from the Apps directory, if you've been following along.

<script src="https://gist.github.com/922309.js?file=prep_crowd_proxy.sh"></script>

### Setting up Apache for Crowd

Now we need to set up Apache to proxy requests for Crowd to the Tomcat container that's running it. You'll need mod\_proxy and mod\_proxy_http enabled, if they're not already.

<a href="http://www.flickr.com/photos/claylo/5622210753/" title="Server Admin: Web by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5103/5622210753_b54f711713.jpg" width="500" height="409" alt="Server Admin: Web"></a>

Unfortunately, I haven't been able to get what's necessary for Apache proxying to Crowd to work _completely_ with the Server Admin UI. So, after making those changes, you need to pull in a config file that we'll manage from here on by hand.

<script src="https://gist.github.com/940191.js?file=add_apache_inc.sh"></script>

**Restart Apache** and you're good to go. Navigate to your secure proxy URL, and you should see something similar to this:

### Running Crowd for the First Time

Fire up Crowd by running the `start_crowd.sh` script in the top level of your {CROWD\_INSTALL} directory. Then hit your secured Apache host, which as we've been going along, would be `https://atlassian.example.com/crowd/`.

<a href="http://www.flickr.com/photos/claylo/5642054711/" title="Atlassian Crowd - License by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5146/5642054711_18589cb56e.jpg" width="500" height="339" alt="Atlassian Crowd - License"></a>

Note that if you _don't_ see this exact screen, you'll see a screen asking you to enter in your "Support Entitlement Number (SEN)". Follow the steps there, _then_ you'll see the license entry screen above.

<a href="http://www.flickr.com/photos/claylo/5654574372/" title="Atlassian Crowd - Installation type by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5226/5654574372_15307c3727.jpg" width="500" height="339" alt="Atlassian Crowd - Installation type"></a>

Of course, we're doing a new Crowd installation.

<a href="http://www.flickr.com/photos/claylo/5654010171/" title="Atlassian Crowd - Database Configuration by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5302/5654010171_f2dc75e8c1.jpg" width="500" height="422" alt="Atlassian Crowd - Database Configuration"></a>

As we've been planning, we're using a MySQL connection, with "crowduser" and a localhost connection.

<a href="http://www.flickr.com/photos/claylo/5654018917/" title="Atlassian Crowd - Options by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5265/5654018917_ec3dee129b.jpg" width="500" height="422" alt="Atlassian Crowd - Options"></a>

Make sure you use the Base URL of http://localhost:8095/crowd ... Crowd itself doesn't know or care about our Apache SSL proxy.

<a href="http://www.flickr.com/photos/claylo/5654642906/" title="Atlassian Crowd - Mail Server by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5150/5654642906_17da006eea.jpg" width="500" height="422" alt="Atlassian Crowd - Mail Server"></a>

Here's how you'll want to configure your Mail Configuration screen if you're planning on using Gmail to deliver your notifications.

<a href="http://www.flickr.com/photos/claylo/5654870342/" title="Atlassian Crowd - Internal Directory by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5149/5654870342_0d53d6f902.jpg" width="500" height="422" alt="Atlassian Crowd - Internal Directory"></a>

The Atlassian Dragons documentation suggests this Internal Directory Name be left to "Crowd" -- I've set mine to Jexy. It's up to you, of course. 

Obviously, we need ATLASSIAN-SECURITY as the Password Encryption method. 

**Note:** if you don't see anything in the pulldown, you need to start over with the Crowd installation. Delete your /Users/atlassian/Documents/crowd directory and begin again.

<a href="http://www.flickr.com/photos/claylo/5654086797/" title="Atlassian Crowd - Default Administrator by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5224/5654086797_0042d306c5.jpg" width="500" height="422" alt="Atlassian Crowd - Default Administrator"></a>

No surprises here, just showing the screen to lay out the steps. 

If you'd like to follow the Atlassian Dragon instructions to the letter here, then do the following:

  * Email address: Enter the address of your administrator email account. We recommend that you give your own email address here.
  * Username: Enter the administrator's login name **`charlie`**
  * Password: Enter the administrator's password, and again to confirm it.
  * First Name: Enter the administrator's first name, **`Charlie`**
  * Last Name: Enter the administrator's last name, **`of Atlassian`**

<a href="http://www.flickr.com/photos/claylo/5654683662/" title="Atlassian Crowd - Integrated Applications by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5265/5654683662_153b305135.jpg" width="500" height="422" alt="Atlassian Crowd - Integrated Applications"></a>

Up to you if you want to enable the OpenID Server or Demo Application. I'm skipping these myself for the time being.

<a href="http://www.flickr.com/photos/claylo/5654121683/" title="Atlassian Crowd - Setup Wizard Complete by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5304/5654121683_59a4deaae9.jpg" width="500" height="422" alt="Atlassian Crowd - Setup Wizard Complete"></a>

That's it! 

### Configure Crowd Users & Groups

Now you need to log into Crowd and configure the groups. If you didn't create the Charlie of Atlassian user earlier, you might want to, just so your usernames and groups match the [Atlassian Dragons setup setups](http://confluence.atlassian.com/display/ATLAS/Dragons+Stage+1+-+Install+Java%2C+PostgreSQL+and+Crowd#DragonsStage1-InstallJava%2CPostgreSQLandCrowd-Step5.SetUpCrowd). **Note:** If you're adding Charlie after the fact, you must add him to the `crowd-administrators` group before he can log in.

#### Add jira-users Group.

<a href="http://www.flickr.com/photos/claylo/5654274953/" title="Atlassian Crowd - Group Browser by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5261/5654274953_3d750b6c92.jpg" width="500" height="355" alt="Atlassian Crowd - Group Browser"></a>

Log in as Charlie of Atlassian, then click the "Groups" tab, then "Add Group".

<a href="http://www.flickr.com/photos/claylo/5654318839/" title="Atlassian Crowd - Add Group by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5025/5654318839_27a4bf34a8.jpg" width="500" height="355" alt="Atlassian Crowd - Add Group"></a>

Add the `jira-users` group as shown, in your main internal directory. Leave the "Active" checkbox checked, and click create.

Repeat this process for these groups:

  * `jira-developers` - JIRA developers
  * `jira-administrators` - JIRA administrators
  * `confluence-users` - Confluence users
  * `confluence-administators` - Confluence administrators
  * `bamboo-administrators` - Bamboo administrators
  
Once those groups are created, you'll want to add **`Charlie of Atlassian`** to the admin groups for JIRA, Confluence, and Bamboo.

<a href="http://www.flickr.com/photos/claylo/5654952266/" title="Atlassian Crowd - View User by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5230/5654952266_19a460b8f0.jpg" width="500" height="355" alt="Atlassian Crowd - View User"></a>

  1. Click **'Users'** in the top navigation bar and find **'Charlie of Atlassian'**.
  2. Click the name to view Charlie's user information.
  3. Click the **'Groups'** tab under **'View User'**, then click **'Add Groups'**.
  4. The 'Add Groups' screen will appear. Click **'Search'** to see all the groups in the directory.
  5. Select the checkbox at the top left, next to the 'Name' button, to select all groups.
  6. Click **'Add Selected Groups'** to add Charlie to the groups.
  
### Dragons Stage 1 Victory!

<a href="http://www.flickr.com/photos/claylo/5655160952/" title="Atlassian Crowd - Groups by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5061/5655160952_e8871bd068.jpg" width="500" height="355" alt="Atlassian Crowd - Groups"></a>

**`Charlie of Atlassian`** can now log into Crowd _on our kick-ass SSL Apache proxy_. If he checks his profile (using the "My Profile" link at the top right of the Crowd screen), he will see the groups he belongs to. Bask in the glow of winning, and let's move on.
