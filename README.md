# mod_lalimit.c
Control the number of processes in the system run queue averaged over the last 1 minutes by matsumoto_r Sep 2009 in Japan

- Date     2009/09/08
- Version  0.01-beta

- change log
```
2009/09/07 0.01 matsumoto_r coding start
2009/09/14 0.02 matsumoto_r pre-server-config add "LAlimitEnable ON|OFF"
2010/11/23 0.90 matsumoto_r real_path_for_mr() and _analyze_links() added
2011/03/08 0.91 matsumoto_r for 1.3 for 2.0 implement
```

## How To Compile
### Build
- for apache 1.3
```
apxs -c -D__MOD_APACHE1__ mod_lalimit.c
cp ./.libs/mod_lalimit.so /usr/local/apache/modules
```

- for apache 2.x
```
apxs -c -D__MOD_APACHE2__ mod_lalimit.c
cp ./.libs/mod_lalimit.so /usr/local/apache2/modules
```

- for apache 1.3 2.x DEBUG into syslog
```
apxs -c -D__MOD_APACHE1__ -D__MOD_DEBUG__ mod_lalimit.c
apxs -c -D__MOD_APACHE2__ -D__MOD_DEBUG__ mod_lalimit.c
```

- Add to  httpd.conf
```
LoadModule lalimit_module libexec/mod_lalimit.so
```

## How To Use
log file is `/tmp/mod_lalimit.log` by default or `#define MOD_LOG_FILE       "/tmp/mod_lalimit.log"`


### Server Config
```
LAlimitEnable <ON|OFF>
```

### Directive Config
* `LAlimit <number of Limit Load Average>`
    * Directory Access Control
    
        ```
        <Directory "/var/www/html">
            LAlimit 0.99
        </Directory>
        ```

    - File Access Control
    
      ```
      <Files "abc.cgi">
          LAlimit 10.02
      </Files>
      ```

    - Files Regex Access Control
    
      ```
      <FilesMatch ".*\.cgi$">
          LAlimit 30
      </FilesMatch>
      ```

    - File Access Control and Output log
    
      ```
      <Files "mt.cgi">
          LAlimit 0.51 log
      </Files>
      ```

    - File Access Control Full Path Matching
    
      ```
      example: symlinks /var/www/html/cgi/abc.cgi -> /var/www/cgi-bin/abc.cgi
               symlinks /var/www/html/cgi/abc.cgi -> /var/www/cgi-bin2/abc.cgi
                   when access /var/www/cgi-bin/abc.cgi or /var/www/cgi-bin2/abc.cgi
                       control the cgi of realpath.

      <Files "abc.cgi">
           LAlimit 10.02 /var/www/html/cgi/abc.cgi
      </Files>
      ```

    - Setting .htaccess write /www/hoge/fuga/.htaccess
    
      ```
      LAlimit 10
      ```
