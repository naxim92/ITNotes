#nginx #variables

- A named regular expression capture can be used later as a variable:
	``` nginx
	 server {
	     server_name   ~^(www\.)?(**?<domain>**.+)$;
	 
	     location / {
	         root   /sites/**$domain**;
	     }
	 }
```
- The PCRE library supports named captures using the following syntax:
	- ``?<`_name_`>`` Perl 5.10 compatible syntax, supported since PCRE-7.0
	- ``?'`_name_`'`` Perl 5.10 compatible syntax, supported since PCRE-7.0
	- ``?P<`_name_`>`` Python compatible syntax, supported since PCRE-4.0