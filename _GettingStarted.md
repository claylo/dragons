# Slaying Atlassian's Dragons with Mac OS X

<div style="float: left; margin-right: 40px; margin-bottom: 20px;">
<a href="http://www.flickr.com/photos/mcampoillustrator/4456163361/" title="Dragon Green by Mark_Campo, on Flickr"><img src="http://farm5.static.flickr.com/4063/4456163361_a58ef5cb25_m.jpg" width="173" height="240" alt="Dragon Green"></a><br />
<span style="font-size: small"><i>Dragon courtesy of <a href="http://www.flickr.com/photos/mcampoillustrator/">Mark Campo</a></i>
</span></div>

A small shop wanting to get up and running with a secure suite of self-hosted Atlassian tools faces a far more daunting challenge than they should.

Wait, wait, why wouldn't that shop just use [JIRA Studio](http://www.atlassian.com/hosted/studio/), Atlassian's hosted service? A number of reasons, including:

  * Inability to customize pre or post-commit hooks to repositories.
  * JIRA Studio's supported versions often lag behind current releases.
  * Security policies may dictate that cloud hosting of source-related tools (repositories, wikis, tickets, or integration testing) is unacceptable.
  
So odds are many organizations will not find JIRA Studio to be adequate. What now?

_Duh, [Jenkins](http://jenkins-ci.org/)_, some will say. Not for me. I don't have a lot of desire to tinker with open source in this case, unless it's got a superior experience to a commercial product. I'm not loving Atlassian's ease of installation (hence this how-to article), but I've found them responsive to support requests. Plus, their docs are decent and the user-experience polish, once you've cleared the installation hurdles, is superior to Jenkins. Plus, Jenkins is only one piece of the puzzle -- issue tracking, collaboration tools, etc, must all be pulled in from other sources. Atlassian is at least a one-stop shop for banging your head against the screen.

Plus, at [$70 for startup licenses for their entire set of tools](http://www.atlassian.com/starter/), it's hard to beat ... provided you overlook how much [it sucks](https://twitter.com/#!/search/%23AtlassianDragons) installing them.

## Disclosure

I'm not a super-experienced Java or Tomcat wrangler. I'm also not an Atlassian nor an Apple stakeholder. I'm just a scripty kind of guy (who uses Jython for my Java wherever possible) who's trying to get some good engineering processes in place. It's possible that what I'm about to propose is not The Right Way to do this, and I'll happily accept patches to correct the article.

Finally, I take no responsibility for your implementation of what I'm suggesting. If you screw the pooch, I'll be sorry to hear it, but that'll be the extent of it. I'm just sharing what I've figured out; [YMMV](http://www.urbandictionary.com/define.php?term=YMMV).

## Assumptions

Despite Atlassian's claim that their target customer doesn't use Apple's OS X, I think that a lot of my startup compadres would be quite happy dropping a Mac Mini Server on their internal network to run their Atlassian setup.

So here's what I'm assuming:

  * You want all Atlassian products to be self-hosted
  * You want HTTPS access to those products
  * You want one domain for your people to use to access the suite. For this article, we'll call this domain "https://atlassian.example.com/"
  * You have a Mac Mini Server to devote to this setup (OS X Server not *required*, but has some UI sugar. If you're crafty, you can get it done on any Mac OS X machine.)
  * You want it all secured and backed up
  
If these assumptions don't match your needs, please head over to Atlassian's [Guide to Installing an Atlassian Integrated Suite](http://confluence.atlassian.com/display/ATLAS/Guide+to+Installing+an+Atlassian+Integrated+Suite "Guide to Installing an Atlassian Integrated Suite"), and good luck.

**From here on, we're on a Mac Mini Server running OS X Server 10.6.7, with Server Admin 10.6.5.** The one I'm working with is a 2.66 GHz Intel Core 2 Duo with 4GB of RAM.

Before starting, if you've just gotten a new Mac Mini Server for this setup, consider setting up the two 500GB disks as a RAID array. That's outside the scope of this article, but links like [this one](http://blog.scottlowe.org/2010/07/17/enabling-raid-1-on-a-mac-mini-server/) might help. (Be sure to read all the comments, etc.) Just keep in mind that you can't create the RAID without erasing both disks, so you'll want to do this right after you take your Mini Server out of the box.

**One final note:** I'm assuming that your organization is small enough for one Mac Mini Server to support the whole suite without going toes-up. If that's not the case, keep reading ... there may be some useful stuff for you anyway.

## Enable MySQL & Web Services on the Server

Obviously we'll need to have MySQL and Apache running on the Mac Mini Server. Atlassian's dragon-slaying guide uses PostgreSQL, but don't worry about that. Since OS X Server comes with a perfectly suitable MySQL installation, we'll use that. If you'd like to use PostgreSQL instead, feel free, but you're on your own.

Turn both of these on at once, since they're on the same screen. We'll come back and fiddle with specific settings later.

<a href="http://www.flickr.com/photos/claylo/5614748779/" title="Server Admin: Settings by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5265/5614748779_dfffcb8fde.jpg" width="439" height="500" alt="Server Admin: Settings"></a>


## One Secure Domain To Rule Them All

[Atlassian doesn't support running their suite in one Tomcat container](http://confluence.atlassian.com/display/JIRA/Deploying+Multiple+Atlassian+Applications+in+a+Single+Tomcat+Container). Yeah, I know, right? So, you're looking at multiple ports or multiple domains as I understand it.

We're going to get around this by setting up Apache as a proxy to the Atlassian multi-headed Tomcat Hydra. As such, our SSL certificate will be on Apache, and we don't have to worry about futzing around with Java certificate keystores and all that.

How we'll do this:
  
  * Create a self-signed SSL certificate
  * Enable Apache 2.2.17+
  * Enable mod\_proxy, mod\_proxy_http
  * Drop a hand-crafted custom snippet into `httpd.conf`
  
All of this can be done without OS X Server Admin tools, if you like. Google around and you'll how all that's done.

### Create a self-signed SSL certificate

Launch Server Admin and connect to your Mac Mini Server. Click on the Certificates button up top, and then add a new self-signed certificate. 

<a href="http://www.flickr.com/photos/claylo/5615293870/" title="Server Admin by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5230/5615293870_1163e7ab42.jpg" width="500" height="250" alt="Server Admin"></a>

<a href="http://www.flickr.com/photos/claylo/5615298828/" title="Certificate Assistant by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5190/5615298828_4c9bfebb35.jpg" width="500" height="371" alt="Certificate Assistant"></a>

Fill in your server's hostname, and click Continue. You'll get a prompt about how it's not for realz until you have a real certificate authority sign it, which we know. Approve that, and your cert will pop up in the list.

### Set up Apache with SSL

The Web service in OS X Server is provided by Apache 2.2.17, and isn't set up with SSL enabled by default. So, let's go turn that on.

<a href="http://www.flickr.com/photos/claylo/5614833317/" title="Server Admin: Web by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5108/5614833317_f3b515b4a7.jpg" width="500" height="422" alt="Server Admin: Web"></a>

Scroll down to the SSL module, check the box to turn it on. Then save changes in the lower right, and turn on the Web service.

<a href="http://www.flickr.com/photos/claylo/5614854171/" title="Server Admin: Web by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5141/5614854171_a75433df56.jpg" width="500" height="422" alt="Server Admin: Web"></a>

Hit your homepage of the Mac OS X Server's hostname (port 80, for now), and you should see the default homepage.

<a href="http://www.flickr.com/photos/claylo/5614862115/" title="Mac OS X Server by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5269/5614862115_e00e3e8a69.jpg" width="500" height="364" alt="Mac OS X Server"></a>

Now go back to Server Admin, and select the Sites section of the Web service. You won't see much to start with.

<a href="http://www.flickr.com/photos/claylo/5614891759/" title="Server Admin: Web &gt; Sites by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5070/5614891759_2b44d2a4bd.jpg" width="500" height="422" alt="Server Admin: Web &gt; Sites"></a>

Click on the top line of the site list. We're going to convert this default site to SSL, using the certificate we created earlier.

Once you've clicked on the site, the General chiclet will populate with settings. **Don't worry about those.** Click on the Security chiclet.

<a href="http://www.flickr.com/photos/claylo/5615506008/" title="Server Admin: Changing Site Security by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5102/5615506008_c3c1097ce8.jpg" width="500" height="405" alt="Server Admin: Changing Site Security"></a>

1.    Click "Enable Secure Sockets Layer (SSL)"
2.    Click "OK" on the resulting dialog sheet.
3.    Select the certificate created previously.
4.    Save your changes.

The next thing that pops up after all that will ask you to restart. You want to do that.

<a href="http://www.flickr.com/photos/claylo/5614920799/" title="Server Admin: Restart Web by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5185/5614920799_d8df36c440.jpg" width="500" height="236" alt="Server Admin: Restart Web"></a>

Once your Web service restarts, you'll find that you can't get to the page we saw before without switching the url to https. Now you've got an SSL site with a self-signed certificate on OS X. 

Next, we're going to start setting up the Atlassian suite, which we'll then access via proxy _through_ our nice SSL-enabled Apache setup.

## Here Be Dragons

Well, here _almost_ be dragons. Let's get a couple of global housekeeping things out of the way, so that you don't have to worry about these settings with each Atlassian app you install.

### Create an Atlassian User

First, let's get ready for Atlassian Dragons to run amok on the Mac Mini Server. You've already got a root user, and an admin-level user ... let's create a user called "atlassian" with minimal privileges.

**Note:** If you're sitting in front of your Mac Mini Server, this is as easy as going into System Preferences and creating a new user called "Atlassian" ... but if you're _not_ sitting in front of your server, you can follow these Workgroup Manager steps. _Skip ahead if you've created the user already._

First, fire up Workgroup Manager, and from the slim list of directories to manipulate, select the one that says "local". You'll get yelled at by the window.

<a href="http://www.flickr.com/photos/claylo/5615592294/" title="Workgroup Manager: Local User by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5227/5615592294_8e4563d4cf.jpg" width="500" height="371" alt="Workgroup Manager: Local User"></a>

Now click on "New User", which doesn't look very clickable, but it is. Then, get yelled at again.

<a href="http://www.flickr.com/photos/claylo/5615598992/" title="Workgroup Manager: New User by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5188/5615598992_67381e2991.jpg" width="500" height="380" alt="Workgroup Manager: New User"></a>

Fill in the information about the Atlassian user. Also, click on the "Home" chiclet and make sure a home directory gets created for this guy.

<a href="http://www.flickr.com/photos/claylo/5615024439/" title="Workgroup Manager: miniserver.local by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5061/5615024439_1bcf36a0e2.jpg" width="500" height="390" alt="Workgroup Manager: miniserver.local"></a>

<a href="http://www.flickr.com/photos/claylo/5615027305/" title="Workgroup Manager: miniserver.local by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5109/5615027305_d7b3555059.jpg" width="500" height="390" alt="Workgroup Manager: miniserver.local"></a>

**Note:** You might get an error about a failure creating the home directory. This is a bug in the Workgroup Manager app over a remote connection. If you log in to the Mini Server with screen sharing and perform these steps, or do them while sitting in front of the server with a local keyboard, you'll be ok. Worst case scenario, you can go down [this path](http://serverfault.com/questions/147288/mac-os-x-server-add-server-user).

With your limited-privileges user and your SSL Apache setup, you're ready to start getting your hands dirty.

### Set JAVA\_HOME Globally

Mac OS X doesn't set the JAVA\_HOME environment variable, and Atlassian apps rely on that variable. So, let's add it to the system-wide bash profile to cover all our bases.

`echo "export JAVA_HOME=/Library/Java/Home" | sudo tee -a /etc/profile`

### Tweak Your MySQL Configuration

Atlassian's guide to setting up the suite uses PostgreSQL. I've got nothing against PostgreSQL -- I just don't want to install any more than I have to. Since Mac OS X Server comes with MySQL installed, and Atlassian tools work with MySQL, let's use that.

Load up `/etc/my.cnf` in the editor of your choice (anything but TextEdit, that is). Add the following to the corresponding sections:

    [mysqld]
    character_set_server=utf8
    collation_server=utf8_bin
    
    # http://confluence.atlassian.com/display/CROWD/MySQL
    transaction-isolation = READ-COMMITTED
    
    # http://confluence.atlassian.com/display/JIRA/JIRA+Cannot+Create+Issues+when+Using+MySQL+with+Binary+Logging
    binlog_format = row

Next, find the chunk of values in `/etc/my.cnf` relating to InnoDB. It starts with:

    # Uncomment the following if you are using InnoDB tables
    
... and uncomment all that stuff. Atlassian apps are much happier dealing with InnoDB tables.

Once you're done with these changes, you should restart the MySQL server via your preferred method.

## A Final Word Before We Start

You're about to create a bunch of accounts in MySQL. Be smart and use different passwords for each one. Get yourself a copy of [1Password](http://agilewebsolutions.com/products/1Password/Mac) or something and keep track of what you're doing.

Also, remember that this article won't be 100% accurate for ever. URLs embedded below for pulling down Atlassian distributions, MySQL JDBC drivers, etc, **will** change. Just know that the stuff below worked for me when I wrote this article, but again, YMMV.
