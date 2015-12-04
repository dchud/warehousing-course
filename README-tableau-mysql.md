# Connecting Tableau to MySQL on your VM 

Tableau has support for connecting to MySQL databases, but it's not
straightforward to connect into your VM's MySQL server. It works, but
requires several steps.

## 1. Change MySQLd bind address on your VM

In a terminal on your VM, stop MySQL:

        % sudo service mysql stop

Edit the file ```/etc/mysql/my.cnf```:

        % sudo nano /etc/mysql/my.cnf
    
Find the line that reads:

        bind-address            = 127.0.0.1

Comment it out and add a duplicate line under it with a new address, 
so you end up with:

        # bind-address            = 127.0.0.1
        bind-address              = 0.0.0.0

This opens up a restriction to connections from other hosts.  You would
normally not want to do this with a production MySQL server, but in this
case, it's just in your VM, so it's fairly safe.

Save the file (Ctrl-X in nano).

Restart MySQL:

        % sudo service mysql start


## 2. Add a symbolic link to the MySQL socket file

MySQL clients connect to the server using a socket file.  On your VM,
MySQL places this socket file under the directory /var/run/mysqld, but
Tableau expects to see it under /tmp.  By creating a symbolic link,
we kinda fake Tableau out.

        % ln -s /var/run/mysqld/mysqld.sock /tmp/mysql.sock


## 3. Forward the MySQL port from the vagrant VM to your host

Shut down your VM using "vagrant halt" in your host shell or using the
"shut down" option within the VM like you normally do.

When it's successfully shut down, open up a shell in the dbplus
directory on your host machine and edit the ```Vagrantfile``` using
your text editor.

Add a line like this:

        config.vm.network "forwarded_port", guest: 3306, host: 3306

...somewhere near the other line that reads like that, probably forwarding
port 8888.  You can add this right after that one.

Save your edited ```Vagrantfile```.

Restart your VM with ```vagrant up```.


## 4. Connect from Tableau

Tableau has database connection drivers available from their [Drivers &
Activation](https://www.tableau.com/en-us/support/drivers) page.  Get
the MySQL drivers for your host platform and install them.

Restart Tableau if you already had it running.

In Tableau, connect to a server, selecting "MySQL".

In the pop-up that appears, enter the following:

        Server:  127.0.0.1
        Username:  mysqluser
        Password:  mysqlpass

Do not change the port unless you changed it yourself from the suggestion
above in your ```Vagrantfile```.

Do not select "Require SSL" unless you added SSL support yourself.

Connect.  If it works, select a database, pull in your tables, specify the
key relationships between tables as needed, and chart away.
