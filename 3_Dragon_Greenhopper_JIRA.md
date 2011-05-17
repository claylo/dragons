## Dragon 3: Install GreenHopper into JIRA

Compared to what we've been through so far, this dragon is an easy one. If you're following along on the Atlassian site, [here's where we are](http://confluence.atlassian.com/display/ATLAS/Dragons+Stage+3+-+Install+GreenHopper+into+JIRA).

### Step 1: Install GreenHopper Plugin into JIRA

Fortunately, we can do this from _inside_ our already-running JIRA installation.

  1. Go to your JIRA installation, such as https://atlassian.example.com/
  2. Select 'Plugins' from the 'System' section of the 'Administration' menu. (It's waaaaaay down at the bottom on the left.)
  3. The plugin management page will appear, defaulting to the 'Manage Existing' tab. Click the 'Install' tab.
  
    <a href="http://www.flickr.com/photos/claylo/5681988931/" title="Plugins - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5182/5681988931_9ae9cdf6c4.jpg" width="500" height="435" alt="Plugins - JIRA"></a>
    
  4. Find GreenHopper either by searching for it or picking it out of the Featured plugins. Click on it to expand details, then click the 'Install Now' button.
  
  5. When the plugin is successfully installed, shut down JIRA and start it back up again using the `stop-jira.sh` and `start-jira.sh` scripts we've used previously.
  
  6. Set up your GreenHopper license key.
    * Select 'License Details' from the new 'GreenHopper' section of the 'Administration' menu.
    * The 'GreenHopper License Information' screen will appear. Follow the prompts and enter in your Starter License or Evaluation License. (Or full-blown license, if you have it!).
    * Click 'Add'

  7. Now click 'Agile' in the top navigation bar. You should see the Dragons planning board, powered by GreenHopper in JIRA, similar to the following:
  
    <a href="http://www.flickr.com/photos/claylo/5682004631/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5104/5682004631_c2815a3458.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>
    
### Step 2: Add Another JIRA Issue and a Sprint

Now that you have GreenHopper, you can choose to update your issues via the GreenHopper tabs or via the standard JIRA interface. For this step, you'll do your updates via GreenHopper.

First, create two 'sprints,' also known as 'milestones.' A sprint is a short period of time, such as two weeks. In each sprint, your developers focus on a particular set of tasks. 

Then create a new issue and include it in one of the sprints. Finally, add the issue you already created to the same sprint.

<a href="http://www.flickr.com/photos/claylo/5682628888/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5148/5682628888_5a359da789.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>

  1. Click 'Add' in the top right of the planning board.
  2. The 'Add Version' screen appears. Create a sprint with the following information:
    * Version Name: 2.0.S1
    * Parent: 2.0
    * Description: Version 2.0 Sprint 1
    * Click 'Create and Close'
  3. Repeat these steps to add another sprint with the following information:
    * Version Name: 2.0.S2
    * Description: Version 2.0 Sprint 2

<a href="http://www.flickr.com/photos/claylo/5682080475/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5105/5682080475_04629e61a1.jpg" width="308" height="177" alt="Dragons - Planning Board - JIRA"></a>

So now we have some loose sprints. Let's clean things up.

  1. Click the sprint box for '2.0.S1'.
  2. Select 'Toggle Visibility' (or just double-click) to expand the sprint details.
    
    <a href="http://www.flickr.com/photos/claylo/5682090097/" title="Dragons - Planning Board - Jira by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5223/5682090097_5bd7cf7a89.jpg" width="326" height="248" alt="Dragons - Planning Board - Jira"></a>
    
  3. Mouseover the 'Parent' item, and click it.
  
    <a href="http://www.flickr.com/photos/claylo/5682664874/" title="Dragons - Planning Board - Jira by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5023/5682664874_ae92d4c549.jpg" width="295" height="328" alt="Dragons - Planning Board - Jira"></a>
    
  4. Select '2.0' from the pulldown that appears.
  
    <a href="http://www.flickr.com/photos/claylo/5682670550/" title="Dragons - Planning Board - Jira by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5070/5682670550_ee3889c896.jpg" width="287" height="340" alt="Dragons - Planning Board - Jira"></a>
    
  5. Repeat these steps with the 'Parent' for '2.0.S2'.

Now we'll create a new bug (as a "New Card") and then drag it around into a sprint. So, don't assign a sprint to it in the New Card pop-up.

<a href="http://www.flickr.com/photos/claylo/5682683990/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5230/5682683990_9cb791e563.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>

  1. Click 'New Card'
  2. Write a summary for the issue.
  3. Give it an estimate.
  4. Click 'Create and Close'.
  
Now you've got two issues, neither of which are scheduled or assigned to sprints. However, your planning board probably shows no cards. Let's clean up:

  1. Click the 'Version' dropdown near the top left of the screen. Select 'Unscheduled' to see all the cards.
  2. Click and drag the card for issue DRA-1. Drop it onto the box for sprint '2.0.S1'. The box will turn green to indicate which drop-target you're dropping the card on.
  3. Repeat by dragging DRA-2 onto sprint '2.0.S1'.
  4. Your planning board will now be empty, because there are no 'Unscheduled' cards. Click the version number '2.0.S1' at the top of the version 2.0.S1 box on the right. You should now see your two cards for DRA-1 and DRA-2.
  5. Click the version number '2.0' at the top of the version 2.0 box on the right. Notice the following points:
    * The value in the version number dropdown box at the top of the planning board also changes to '2.0'.
    * Your two issue cards are included in version 2.0 as well as in sprint 2.0.S1.
    * You can double-click the title bar of each version box to minimize or expand the box.
  6. Mark one of your issues as complete:
    * Click the down arrow next to 'Planning Board' to select 'Task Board'.
    * Your task board will appear, with your two issue cards in the 'To Do' column on the left. Click the card for 'DRA-1', drag it to the right and drop it on the 'Done' column.
    * The 'Transitioning Issue' screen will appear. Select 'Close Issue' and click 'Update'.
    
      <a href="http://www.flickr.com/photos/claylo/5682820634/" title="Dragons - Task Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5022/5682820634_66fa65954b.jpg" width="500" height="435" alt="Dragons - Task Board - JIRA"></a>
      
    * The 'Close Issue' screen will appear. Click 'Close Issue'.
  
### Step 3: Use the Scrum Template for your Project and Add a Story

GreenHopper includes a project template for projects using the Scrum methodology. The template adds custom fields like ranking fields, story points, etc to your project. It also allows you to create stories, epics and technical tasks. In this step, you'll apply the Scrum template to your project and create a story.

<a href="http://www.flickr.com/photos/claylo/5682266335/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5102/5682266335_7fd14eef3f.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>

  1. Open the 'Tools' menu on the planning board and click 'Configuration'.
  2. The project configuration page will appear. Click the 'General' tab.
  3. Select 'Scrum' from the 'Project Template' dropdown menu.
    
    <a href="http://www.flickr.com/photos/claylo/5682842492/" title="Dragons - Project Configuration - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5149/5682842492_dcd1d20bf9.jpg" width="500" height="435" alt="Dragons - Project Configuration - JIRA"></a>
    
  4. Click 'Change Template' on the confirmation window.
  5. Next, create a new story. Go back to the planning board and click 'New Card'. Enter the following values:
  
    <a href="http://www.flickr.com/photos/claylo/5682850228/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5187/5682850228_8cd4131f04.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>
  
    * Card type: `Story`
    * Priority: `Major`
    * Summary: `As a dragon slayer I would like to wield an extremely big sword`
    * Version: `2.0`
    * Component: `Unknown`
    * Business Value: `10`
    * Story Points: `10`
    
  6. Click 'Create and Close'. You'll see your planning board again, showing your story.
  
<a href="http://www.flickr.com/photos/claylo/5682868920/" title="Dragons - Planning Board - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5066/5682868920_e308d2482b.jpg" width="500" height="435" alt="Dragons - Planning Board - JIRA"></a>

### Step 4: Add the GreenHopper Gadget to your JIRA Dashboard

Now you'll add the GreenHopper 'Agile' gadget to your Dragon Dev Dashboard.

  1. Click 'Dashboards' at the top left of your JIRA screen.
  2. Your 'Dragon Dev Dashboard' will appear. Click 'Add Gadget'.
  3. The 'Gadget Directory' will appear, showing a list of the available gadgets for your JIRA dashboard. Find the 'GreenHopper Agile Gadget' and click 'Add it Now'.
    
    <a href="http://www.flickr.com/photos/claylo/5682315501/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5223/5682315501_455ab2a498.jpg" width="500" height="435" alt="Dragon Dev Dashboard - JIRA"></a>
    
  4. Click 'Finished' to go back to your dashboard.
  5. Configure the 'GreenHopper Agile Gadget'
    * Start typing 'Dragons' in the 'Project or Saved Filter' box and select 'Dragons (DRA)' from the dropdown list that appears.
    * Leave the default values for 'Display chart values' and 'Display chart legend'.
    * Click the dropdown arrow next to 'Refresh Interval' and select 'Every 15 minutes'.
    * Click 'Save'.
    
      <a href="http://www.flickr.com/photos/claylo/5682892544/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5189/5682892544_b9e938bab1.jpg" width="483" height="368" alt="Dragon Dev Dashboard - JIRA"></a>
      
    * Click the version dropdown arrow next to 'Unscheduled' and select '2.0' for the release to watch.
    
      <a href="http://www.flickr.com/photos/claylo/5682897812/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5181/5682897812_2f00b7fd0d.jpg" width="483" height="402" alt="Dragon Dev Dashboard - JIRA"></a>
      
    * The gadget displays the 'Hours' burndown chart by default. Click the 'Issues' tab to see the issues burndown chart. 
    
  6. Choose a different color for your 'GreenHopper Agile Gadget'.
  
    <a href="http://www.flickr.com/photos/claylo/5682903284/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5222/5682903284_c02c55e1ac.jpg" width="485" height="404" alt="Dragon Dev Dashboard - JIRA"></a>
    
### Victory! 

Your JIRA dashboard now has three gadgets:

  * The GreenHopper Agile Gadget
  * The 'Assigned to Me' gadget
  * the 'Projects' gadget.
  
<a href="http://www.flickr.com/photos/claylo/5682342731/" title="Dragon Dev Dashboard - JIRA by claylo, on Flickr"><img src="http://farm6.static.flickr.com/5066/5682342731_a04e4fe42b.jpg" width="500" height="435" alt="Dragon Dev Dashboard - JIRA"></a>
  
Onward!

