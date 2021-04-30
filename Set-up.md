## Set-up

**WARNING**: Very recently some changes were pushed to Pentoo which moved the Pentoo Overlay from /var/lib/layman to /var/db/repos.

#### Introduction
Pentoo Linux can be used as an overlay bringing a full set of tools and settings to an existing stable Gentoo setup.

#### Adding the overlay
##### Update the portage to the latest version
````
emerge --sync
````
##### Make sure that layman and subversion are installed
````
emerge app-portage/layman subversion 
````
##### Update list of overlays
````
layman -L
````
##### Add Pentoo overlay
````
layman -a pentoo
````
#### Add proper keywords/use flags and other settings. There are 3 options:

##### Option 1: Use one of Pentoo profiles
List all available profiles
````
eselect profile list
````
Select an appropriate Pentoo profile for your current setup
````
eselect profile set pentoo:pentoo/hardened/linux/amd64
````
##### Option 2: Use the "overlay" subprofile (gentoo profile + keywords/use files)
Remove an old profile
````
rm /etc/portage/make.profile
mkdir -p /etc/portage/make.profile
````
Create the /etc/portage/make.profile/parent file with the following context:
````
gentoo:default/linux/amd64/17.1/hardened
gentoo:targets/desktop/
pentoo:pentoo/overlay
````
switch to that profile
````
env-update && source /etc/profile 
````
##### Option 3: Manual
Create symlinks for necessary keywords/use files:
````
ln -s /var/lib/layman/pentoo/profiles/pentoo/base/package.accept_keywords/net-analyzer /etc/portage/package.keywords
ln -s /var/lib/layman/pentoo/profiles/pentoo/base/package.use/dev-ruby /etc/portage/package.use
````
Check changes and adjust other settings if required
See the next section for examples
````
emerge -DNupv world
````
##### Install the entire Pentoo 
````
emerge -DNu pentoo
````
or choose a separate package
````
emerge -DNu pentoo-wireless
````
##### Adjusting settings
To merge the overlay with your current setup smoother, additional changes can be made in a usual Gentoo way by modifying files in the /etc/portage directory. For example, you can disable additional wireless drivers by adding a file to the /etc/portage/package.use/ directory with the following content:
````
pentoo/pentoo -bluetooth
pentoo/pentoo-wireless -drivers
````
and disable unrequired global flags in the make.conf file:
````
USE="-bluetooth -gps -caps -livecd -ldap"
````
See the official Gentoo handbook for more details
