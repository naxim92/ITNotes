#linux #locale #cyrillic

```
update-locale "LANG=en_HK.UTF-8"
locale-gen --purge "en_HK.UTF-8"
dpkg-reconfigure --frontend noninteractive locales
```