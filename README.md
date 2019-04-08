The command line is your friend: WP-CLI, the shell and what they can do for you!
================================================================================

[Sergio Scabuzzo](https://www.ecotechie.io/about-me/) - Twitter/[@ecotechie](https://twitter.com/ecotechie) - WordCamp Santa Clarita Valley 2019


Who is this guy?  
 - Linux user  
 - Sustainability warrior  
 - WordPress Core and WP-CLI contributor  
 - Founder of [Ecotechie.io](https://www.ecotechie.io)  
 - Brewer of things  
 - VW bus owner/mechanic  

***

### First Things First


> With great power comes great responsibility!


 The command line takes no prisoners.
 If you have a `typo` or miss something in the logic of your command or script,
 it can come back to bite you!!!

 Don't `blindly copy/paste` commands off of the internet before knowing what they do.
 Including from these slides.



##### With that out of the way, let's get rolling...

***

Command Line Usage
------------------

***

### Why use the command line:


 - Your friends will think `you are the coolest` :)

 - It's been around (`No bugs`)

 - Script writing allows `many commands` to work together

 - Repetitive tasks take `seconds not minutes`, or even hours

***

### Why NOT use the command line:


 - Your friends will think `you are a dork` :(

 - A single command can `easily break` things if you aren't careful

 - Script writing makes breaking even more things `super easy`

 - You can spend hours writing a script `that won't get used`

***

### Bash and The Easy Stuff

##### Some common/useful commands:

 `pwd`	Print Working Directory

    $ pwd         
    /home/sergio  


 `mkdir`	Make directories  


 `cd`	Change Directory


 `ls`	List a directory


 `cat`	Show a file's content


 `man`	System's manual pager  
	Use it to find information on installed programs, utilities or functions


***

### You Have Options, With Commands


##### Some handy ls options are:

 `ls -a`	do not ignore entries starting with a `.`, AKA show `hidden` files and directories  
 `ls -l`	use a `long listing` format, AKA verbose  
 `ls -lh`	with -l to print `human readable sizes` (e.g., 1K 234M 2G)  


##### Jump around directories with:

 `cd - ` 	change to previous directory  
 `cd `	use it by itself to change to your home directory

***

### More Advanced Features of the Command line

> Remove files or directories

 `rm -rf !(file1|file2)`


##### What does all that do?

 `-r`, `-R`, `--recursive`	remove directories and their contents `recursively`  
 `-f`, `--force`		ignore nonexistent files and arguments, never prompt  
 `!()`			negates whatever is in the parentheses  

    $ touch file{1..5}                 
    $ ls                               
    file1  file2  file3  file4  file5  
    $ rm -rf !(file1|file2)            
    $ ls                               
    file1  file2                       

***

### AKA, Aliases


 Sometimes you want to `reuse a complicated/convoluted` command often enough.

 That's where `aliases` become very handy.

 Add this line to the bottom of your `~/.bashrc` file so it persists across reboots.

> The `~` represents your home directory.

    alias getpass='openssl rand -base64 20'

> Why type sudo all the time? No more of that, by creating corresponding aliases.

    alias apt-get='sudo apt-get'

***

WP-CLI for the Easy Win
-----------------------

***

### WordPress Command Line Interface:


 "The project’s goal is to offer a complete alternative to the WordPress admin"
 "For any action you might want to perform in the WordPress admin, there should be an equivalent WP-CLI command."


 When these `actions` become `scriptable` we can start to really see the power of the command line!

***

### Often Used commands


 Backup you database, export a .sql file.

    wp db export


 Regenerate all missing thumbnails for attached images.

    wp media regenerate --yes --only-missing


 Verify file integrity by comparing to published checksums.

    wp checksum core && wp checksum plugin --all


***

### Fix Broken Update


> Got a broken update?
> White Screen of Death?
> Don't want to update to the latest version?

    wp plugin update slug --version=x.x.x


> If that doesn't fix it...

    wp plugin deactivate slug


> Really tired of this plugin causing trouble?

    wp plugin uninstall slug --deactivate

***

### Update Password

> Have you ever forgotten your WordPress password?

 How do we get back in?

 1. Click on the "Forgot Password" link
 2. Enter email or username
 3. Wait for email and click on the password reset link
 4. Reset Password
 5. Finally login again

 Or...

 1. SSH into Server
 2. Go to the WordPress Directory


    wp user update adminuser@example.com --user_pass=C00lP4ssw0rd


##### We could even use our handy bash alias from earlier and create a more robust password:

    getpass                       
    G3Rni/ruEWMoz0U86Ow1gkkoQDQ=  

***

### Create a Child Theme

> Ever edit a theme's code? Only to have your changes erased by the next update...

##### Let's create a child theme from the (non-child) active one, then activate it:

    wp scaffold child-theme slug --parent_theme=$(wp theme list --status=active --field=name) --activate


> What's happening here?

##### The $() is known as [`command substitution`](https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html)

> Bash performs the expansion by executing the command inside the parentheses in a subshell environment.
> Then replacing the `command substitution` with the standard output of that command.


##### This is one of the magic tricks of WP-CLI

> [`Commands should be composable... The output from one command should be easily pipe-able to another command.`](https://make.wordpress.org/cli/handbook/philosophy/#composability-is-always-a-good-idea)

***

### So Many Themes, so Little Time


 Cleaning up over 132 installed themes?

##### Some of your options:

 1. Remove the unused themes one by one from the `WordPress admin`.
 2. Login through `(S)FTP` and remove all the `disabled` theme directories.
 3. Use `WP-CLI` and delete all inactive themes in seconds!


    wp theme delete $(wp theme list --status=inactive --field=name)

***

### Search for This and Replace with That

> What's this fun thing?

    wp search-replace something_old something_new —report-changed-only —dry-run

***

Let's do a demo #FamousLastWords...
===================================

***

Create a link on "Hello World"
==============================

***

### Sub Demo! Changing Permalink Structure


##### #Bonus

    wp rewrite structure '/%postname%/'

***

What Happens to Our Link?
=========================

***

### No Big Deal


 We could edit the link and keep on being awesome!

***

### But Wait!


##### What if we have more links to edit?

 How about one more link to edit?  

    wp post create --from-post=1


 How about after this?

    for i in {1..10}; do !!; done

***

### WP-CLI Search-Replace Cleanup Time


    wp search-replace '\/[0-9]{4}\/[0-9]{2}\/[0-9]{2}\/([a-zA-Z0-9-_]+)\/' '/\1' --regex --regex-flags='mi' wp_posts --include-columns=post_content --dry-run

***

### Command All Sites With @alias


Instead of having to SSH into each site, then go to the WordPress directory, then run a WP-CLI command...

> Aliases can be registered in your project’s `wp-cli.yml` file
> ...or your user’s global `~/.wp-cli/config.yml` file:


    @prod:                                            
      ssh: dev_user@example.com~/webapps/production   
    @dev:                                             
      ssh: vagrant@192.168.50.10/srv/www/example.dev  
    @local:                                           
      ssh: vagrant:default                            
    @all:                                             
      - @prod                                         
      - @dev                                          
      - @local                                        

***

### Useful Uses for Using @alias Commands


> Security vulnerability in a popular plugin?
> All the sites you manage have it installed?
> Quickly update the plugin on all sites using WP-CLI's [`@alias`](https://make.wordpress.org/cli/handbook/running-commands-remotely/#aliases)

    wp @all plugin update slug


> Heck why not "update all the things"?
> This feels like a bit of a `cowboy coder` move, but when in a pinch...

    wp @all core update && wp @all plugin update --all && wp @all theme update --all


> One of the million ways to `sync databases` from a development server to production:

    wp @production db export &&
    wp @development db export - | wp @production db import - &&
    wp @production search-replace "example.dev" "example.com"


> Remember when we updated our forgotten user password?
> How about this for an even faster way:

    wp @alias user update adminuser@example.com --user_pass=C00lP4ssw0rd

***

### Install/Activate Multiple Plugins at Once


You could easily install and activate `several, often used, plugins` at once.
Do you have a list of plugins you install on every site?

    wp @alias plugin install --activate duplicator health-check query-monitor redirection

***

Scripting Examples (Putting it All Together)
--------------------------

***

### Install WP-CLI and WordPress on Remote Server


    wp-init

https://github.com/ecotechie/wp-init

***

### Backing Up WordPress to a Local Directory


    wp-backup

https://github.com/ecotechie/wp-backup

***

Thanks! Questions?
-------

[@ecotechie](https://twitter.com/ecotechie)
https://www.ecotechie.io/wp-cli-and-the-shell/



Powered by a fork of Daniel Bachhuber's WP-CLI command: [present](https://github.com/danielbachhuber/wp-cli-present-command)
