# MySQL full-text parser plugin - mecab

This plugin is adding parser of mecab tokenizer for MyISAM Fulltext Index.  
You can query in natural langauge mode or in boolean mode.

## Summary

It's fork repository of mysqlftppc (MySQL Full-Text Parser Plugin Collection) which has developed by hiroaki-kawai.  
This repository add a feature of geting compatible with `MySQL-5.5` and `MySQL-5.6`.

The family of mysqlftppc are below.

* https://github.com/y-ken/mysqlftppc-bigram
* https://github.com/y-ken/mysqlftppc-mecab
* https://github.com/y-ken/mysqlftppc-snowball
* https://github.com/y-ken/mysqlftppc-space
* https://github.com/y-ken/mysqlftppc-suffix

## Usage

It is a example to install mysqlftppc-mecab for CentOS-6.x.

#### install dependency libraries

install ICU to normalize unicode sentences.

```sh
$ sudo yum install libicu libicu-devel
```

install mecab with IPA dictionary.

```
$ sudo yum localinstall http://packages.groonga.org/centos/6/x86_64/Packages/mecab-0.996-1.el6.x86_64.rpm \
  http://packages.groonga.org/centos/6/x86_64/Packages/mecab-devel-0.996-1.el6.x86_64.rpm \
  http://packages.groonga.org/centos/6/x86_64/Packages/mecab-ipadic-2.7.0.20070801-6.el6.1.x86_64.rpm
```

#### edit my.cnf

add configuration to `[mysqld]` section. then, restart mysql.

```
# mysqlftppc
mecab_normalization = KC
mecab_unicode_version = 3.2
mecab_dicdir = /usr/lib64/mecab/dic/ipadic
```

Note: You can get `mecab_dicdir` path with command `mecab --dump-config | grep dicdir`.

```sh
$ sudo /etc/init.d/mysql restart
```

#### install mysqlftppc-mecab

build mysqlftppc-mecab and install it.

```sh
$ git clone https://github.com/y-ken/mysqlftppc-mecab.git
$ cd mysqlftppc-mecab
$ aclocal && libtoolize --automake && automake --add-missing && automake && autoconf
$ ./configure --with-mysql-config=`which mysql_config` --with-mecab-config=`which mecab-config` --with-icu-config=`which icu-config`
$ make
$ sudo make install
$ mysql -uroot -p -e "install plugin mecab soname 'libftmecab.so';"
```

#### working check

```sql
mysql> CREATE TABLE ft_mecab (c TEXT, FULLTEXT(c) WITH PARSER mecab) ENGINE=MyISAM DEFAULT CHARSET=utf8;
mysql> INSERT INTO ft_mecab VALUES("今日の天気は晴れです。");
mysql> SELECT * FROM ft_mecab WHERE MATCH(c) AGAINST('+"天気"' IN BOOLEAN MODE);
```
## Blog Articles

* 全文検索パーサプラグイン「mysqlftppc」のMySQL-5.6対応版を作りました  
http://y-ken.hatenablog.com/entry/mysql-parser-plugin-mysqlftppc

## How to contribute

Fork it via GitHub, commit changes and send me a pull request.  
Pull requests are very welcome!!

## Authors

* hiroaki-kawai
* Kentaro YOSHIDA [y-ken@GitHub](https://github.com/y-ken)

## License

it has forked from http://sourceforge.net/projects/mysqlftppc/

* Apache License V2.0
* BSD License
* GNU General Public License (GPL)
* GNU Library or Lesser General Public License (LGPL)
