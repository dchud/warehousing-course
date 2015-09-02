This describes installing a virtual machine configured for use in
a data warehousing for analytics course.  Students will be working
with Jupyter notebooks (Python, R, Spark), the unix (ubuntu-14.04)
command line, MySQL, Spyder, PostgreSQL, and a few other things as
they come up.

The box contains a lot of stuff, and is rather big compared to a
standard Ubuntu ISO, say.  You will need at least 3Gb free on your
host machine to download it, and probably at least double that to
run it.  Because the download file is big, you will want to be on
a network with a fat pipe.

I haven't set up a vagrant box before and my devops chops are rusty
so there are likely to be a few hiccups along the way.  Thanks a
ton for trying it out - all feedback is welcome.


Installation
------------

1. Install VirtualBox: https://www.virtualbox.org/wiki/Downloads

VirtualBox is a virtual machine container / manager.

2. Install Vagrant: http://www.vagrantup.com/downloads.html

Vagrant is a tool that makes installing preconfigured VMs easy.

3. On a Mac, open a terminal, and at the command prompt, type:

  ~ $ mkdir dbplus

On windows, open a cmd shell, and at the command prompt, type:

  C:\> mkdir dbplus
  
This directory will hold the configuration file.

4. Next, on a mac:

  ~ $ cd dbplus
  
Or on windows:

  C:\> cd dbplus

5. And then:

  ~/dbplus $ vagrant init dchud/dbplus
  
Or:
  
  C:\dbplus> vagrant init dchud/dbplus
  
This should generate a Vagrantfile which we'll edit next.

6. If you're not used to using a text editor, try installing and
using either TextWrangler (OS X,
http://www.barebones.com/products/textwrangler/) or Notepad++
(Windows, https://notepad-plus-plus.org/) for the next step.

7. Use a text editor to edit the file Vagrantfile just created.
Look for the section in the middle of the file, among the other
commented-out bits, that includes:

   # vb.gui = true

Right around there in the file, insert the following (and don't
comment it):

   config.vm.provider "virtualbox" do |v|
     v.gui = true
     v.customize ["modifyvm", :id, "--memory", "2048"]
     v.customize ["modifyvm", :id, "--cpus", "2"]
     v.customize ["modifyvm", :id, "--graphicscontroller", "vboxvga"]
     v.customize ["modifyvm", :id, "--accelerate3d", "on"]
     v.customize ["modifyvm", :id, "--ioapic", "on"]
     v.customize ["modifyvm", :id, "--vram", "128"]
     v.customize ["modifyvm", :id, "--hwvirtex", "on"]
   end

Note (from the line about "--cpus") that this assigns two vCPUs
to the VM.  This will work for you if, like me, you have a dual-cpu,
dual-core machine that can dedicate two cores to a guest VM and
still have two cores for the host OS.  If you have less than this,
set it to 1.  The VM will not perform as well, but it should work,
at least.  Same goes for the line about "--memory"... if you don't
have 2GB RAM to spare, lower this number. YMMV.

The graphics-related lines pass configuration details through to
allow the VM to load up with a GUI, rather than just a shell /
command-line.

Save the file and exit your editor.

8. Start up the image:

   ~/dbplus $ vagrant up

Or:
 
   C:\dbplus> vagrant up

This should download the box image.  It is big - at least 2.8Gb.
After the download completes, the image should boot up and you
should see a window open with the box coming online.

9. Inside the VM

The box should come right up and log you in as user "vagrant"
(password: vagrant).

If you want to change the screen resolution, the icons in the menu
at top right pop up when clicked to reveal a little tool/wrench
settings menu.  When the All Settings menu comes up, click Displays,
and tweach the resolution to something that works for you.



Basic features
--------------

To load up apps, mouse up to the top-left Activities menu.  It
should pop up some shortcuts on the left, including a web browser,
R Studio, and a terminal window.

To use Spyder, open up a terminal window, and enter:

  $ spyder &

To use Jupyter, open up a terminal window, and enter:

  $ jupyter notebook

A web browser should open from which you can fire up Python 2/3,
R, Julia, Bash, or PySpark notebooks.  Everything there should "just
work".  A lot of scipy/numpy libraries should be installed for both
Pythons, along with dplyr/ggplot2/rmysql for R, and a handful of
useful libraries for Julia too.  The PySpark notebooks should connect
automatically to Spark and -- after several seconds of the server
firing up -- should give you a working SparkContext as "sc".

To use MySQL, connect as user "mysqluser" with password "mysqlpass":

  $ mysql -u mysqluser -p
  Enter password: [there is where you type "mysqlpass" without the quotes]
  ...
  mysql> CREATE DATABASE testing;   # etc.

To use PostgreSQL, connect as user "pguser" with password "pgpass":

  $ createdb -U pguser testing
  Password: [this is where you type "pgpass" without the quotes]
  $ psql -U pguser testing
  Password for user pguser: ["pgpass", again]
  testing=# CREATE TABLE testtable () # etc.

The excellent ipython-sql (https://github.com/catherinedevlin/ipython-sql)
is also installed, so you can connect to sqlite, mysql, and pgsql
databases from within notebooks, too.



Maintenance and configuration
-----------------------------

This is a full ubuntu 14.04 box with all that entails.  If something's
missing, your "vagrant" account (password: vagrant) has sudo rights,
so install away.  If you mess something up, you can throw it out
and start over... it's virtual!


Credits
-------

This box/image started from the excellent Data Science Toolbox image
(http://datasciencetoolbox.org/), to which I added X/Gnome and a
long list of other tools.  If you're looking for a leaner, command-line
toolbox, you should definitely start with that instead of this.
