---
layout: post
title:  "You Should Script EVERYTHING"
date:   2015-05-05
categories: scripting
published: false
tags: powershell svn
---

Recently I was tasked with ensuring that all of our projects where configured (in eclipse) to be utf-8 format. Why? 
  Eclipse by default uses your system encoding, developers are using windows(CP-1252) and our deployment server 
  is Linux (utf-8). So instead of troubleshooting every time a deployment crashes we are going to proactively force each project 
   to use utf-8. My first instinct was that i'd be spending the day checking out every project, looking at the project properties
    and then running the unit tests.   As I was about to start my arduous task it hit me, I should script this.
    
    
###Checking out each project
Supplied with a list of projects, basic svn cli knowledge, and basic powershell knowledge I started with a very simple script.
An array of strings to signify projects, and a for each loop. It looked like this:

```powershell
$projects="project1","project2","project3","project4","project5","project6"

foreach($i in $projects)
{
    svn checkout "http://<svnhost>/$i/trunk" "$pwd/$i"
}
```

###Checking Eclipse encoding
Awesome, I just saved me self at least a dozen mouse clicks. What if I could save myself a dozen more?
  As it turns out, eclipse uses a specific project file in the .settings directory of each project.  So I added some logic 
  to copy the file in the right place
  
```powershell
$projects="project1","project2","project3","project4","project5","project6"

foreach($i in $projects)
{
    svn checkout "http://<svnhost>/$i/trunk" "$pwd/$i"
    if (!$(Test-Path "$pwd\$i\.settings\org.eclipse.core.resources.prefs"))
    {
        cp "C:\path\to\.settings\org.eclipse.core.resources.prefs" "$pwd\$i\.settings\org.eclipse.core.resources.prefs"
    }
}
```  

###Running the tests
Simple enough! But what about those pesky junit tests? o I know i'll just call the ant cli...

```powershell
$projects="project1","project2","project3","project4","project5","project6"

foreach($i in $projects)
{
    svn checkout "http://<svnhost>/$i/trunk" "$pwd/$i"
    if (!$(Test-Path "$pwd\$i\.settings\org.eclipse.core.resources.prefs"))
    {
        cp "C:\path\to\.settings\org.eclipse.core.resources.prefs" "$pwd\$i\.settings\org.eclipse.core.resources.prefs"
    }
    ant -f $pwd\$i\build.xml test
}
```

###Wooo i'm done time to go internet... err do more work!
Now my script is complete.  It took me approximately 2 hours to research and write the script for a task that was going 
to take 3/4 of my day.  Not only that, but I also have a baseline script available to me and my peers the next time we need
to do a bulk configuration change and that's the kind of gain that is invaluable.  So the next time you are facing some 
tedious task take some time to script it out.

