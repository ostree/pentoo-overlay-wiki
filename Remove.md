## Remove

Uninstall whatever was installed
````
emerge -C pentoo
````
or a separate package
````
emerge -C pentoo-wireless
````
If you installed multiple individual packages, use the following command to get the list:
````
cd /var/db/pkg ; for x in */* ; do [[ `cat ${x}/repository` == "pentoo" ]] && echo "${x}"; done
````
Clean up the system and remove the overlay from your repository:
````
emerge --depclean
eclean-dist
revdep-rebuild
layman -d pentoo
````