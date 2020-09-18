---
title: Linux编译安装php常见错误
date: 2019-08-17 10:30:14
toc: true
categories: 
    - Linux
tags: 
    - Linux
    - PHP
cover: https://cdn.jsdelivr.net/gh/CrazyDuck827/CrazyDuck827.github.io/icon/P6Cqpi8ypvau1mSLeA4NledwC281Z3s7.jpg
---

1、configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution
　　解决方法：
　　yum -y install libxslt-devel

2、configure: error: Could not find net-snmp-config binary. Please check your net-snmp installation.
　　解决方法：
　　yum -y install net-snmp-devel

3、configure: error: Please reinstall readline - I cannot find readline.h
　　解决方法：
　　yum -y install readline-devel


4、configure: error: Cannot find pspell
　　解决方法：
　　yum -y install aspell-devel

5、checking for unixODBC support... configure: error: ODBC header file '/usr/include/sqlext.h' not found!
　　解决方法：
　　yum -y install unixODBC-devel

6、configure: error: Unable to detect ICU prefix or /usr/bin/icu-config failed. Please verify ICU install prefix and make sure icu-config works.
　　解决方法：
　　yum -y install libicu-devel

7、configure: error: utf8mime2text() has new signature, but U8TCANONICAL is missing. This should not happen. Check config.log for additional information.
　　解决方法：
　　yum -y install libc-client-devel

8、configure: error: freetype.h not found.
　　解决方法：
　　yum -y install freetype-devel

9、configure: error: xpm.h not found.
　　解决方法：
　　yum -y install libXpm-devel

10、configure: error: png.h not found.
　　解决方法：
　　yum -y install libpng-devel

11、configure: error: vpx_codec.h not found.
解决方法：
　　yum -y install libvpx-devel

12、configure: error: Cannot find enchant
解决方法：
　　yum -y install enchant-devel

13、configure: error: Please reinstall the libcurl distribution - easy.h should be in /include/curl/
解决方法：
　　yum -y install libcurl-devel

14、configure: error: mcrypt.h not found. Please reinstall libmcrypt.
　　解决方法：
　　wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/libmcrypt/libmcrypt-2.5.7.tar.gz
　　tar zxf libmcrypt-2.5.7.tar.gz
　　cd libmcrypt-2.5.7
　　./configure
　　make && make install
15、Cannot find imap

　　解决方法：
　　ln -s /usr/lib64/libc-client.so /usr/lib/lio

16、configure: error: utf8_mime2text() has new signature, but U8T_CANONICAL is missing.
　　解决方法：
　　yum -y install libc-client-devel

17、Cannot find ldap.h
　　解决方法：
　　yum -y install openldap
　　yum -y install openldap-devel

18、configure: error: Cannot find ldap libraries in /usr/lib
　　解决方法：
　　cp -frp /usr/lib64/libldap* /usr/lib/

19、configure: error: Cannot find libpq-fe.h. Please specify correct PostgreSQL installation path
　　解决方法：
　　yum -y install postgresql-devel

20、configure: error: Please reinstall the lib curl distribution
　　解决方法：
　　yum -y install curl-devel

21、configure: error: Could not find net-snmp-config binary. Please check your net-snmp installation.
　　解决方法：
　　yum -y install net-snmp-devel

　　checking whether to enable XMLWriter support… yes, shared checking for xml2-config path… (cached) /usr/bin/xml2-config checking 　　whether libxml build works… (cached) yes checking for XSL support… yes, shared configure: error: xslt-config not found. Please 　　　　reinstall the libxslt >= 1.1.0 distribution
　　解决方法：
　　yum -y install libxslt-devel

23、checking for BZip2 support… yes checking for BZip2 in default path… not found configure: error: Please reinstall the BZip2 distribution

　　解决方法：
　　
　　yum -y install bzip2-devel

24、checking for cURL support… yes checking if we should use cURL for url streams… no checking for cURL in default path… not found configure: error: Please reinstall the libcurl distribution – easy.h should be in/include/curl/

　　解决方法：
　　
　　yum -y install curl-devel

25、checking for curl_multi_strerror in -lcurl… yes checking for QDBM support… no checking for GDBM support… no checking for NDBM support… no configure: error: DBA: Could not find necessary header file(s).

　　解决方法：
　　yum -y install db4-devel

26、checking for fabsf… yes checking for floorf… yes configure: error: jpeglib.h not found.

　　解决方法：
　　yum -y install libjpeg-devel

27、checking for fabsf… yes checking for floorf… yes checking for jpeg_read_header in -ljpeg… yes configure: error: png.h not found.

　　解决方法：
　　yum -y install libpng-devel

28、checking for png_write_image in -lpng… yes If configure fails try –with-xpm-dir=

configure: error: freetype.h not found.
　　解决方法：
　　yum -y install freetype-devel
29、checking for png_write_image in -lpng… yes configure: error: libXpm.(a|so) not found.

　　解决方法：
　　yum -y install libXpm-devel

30、checking for bind_textdomain_codeset in -lc… yes checking for GNU MP support… yes configure: error: Unable to locate gmp.h

　　解决方法：
　　yum -y install gmp-devel

31、checking for utf8_mime2text signature… new checking for U8T_DECOMPOSE… configure: error: utf8_mime2text() has new signature, but U8T_CANONICAL is missing. This should not happen. Check config.log for additional information.

　　解决方法：
　　yum -y install libc-client-devel

32、checking for LDAP support… yes, shared checking for LDAP Cyrus SASL support… yes configure: error: Cannot find ldap.h

　　解决方法：
　　yum -y install openldap-devel

33、checking for mysql_set_character_set in -lmysqlclient… yes checking for mysql_stmt_next_result in -lmysqlclient… no checking for Oracle Database OCI8 support… no checking for unixODBC support… configure: error: ODBC header file ‘/usr/include/sqlext.h' not found!

　　解决方法：
　　yum -y install unixODBC-devel

34、checking for PostgreSQL support for PDO… yes, shared checking for pg_config… not found configure: error: Cannot find libpq-fe.h. Please specify correct PostgreSQL installation path

　　解决方法：
　　yum -y install postgresql-devel

35、checking for sqlite 3 support for PDO… yes, shared checking for PDO includes… (cached) /usr/local/src/php-5.3.7/ext checking for sqlite3 files in default path… not found configure: error: Please reinstall the sqlite3 distribution

　　解决方法：
　　yum -y install sqlite-devel

36、checking for utsname.domainname… yes checking for PSPELL support… yes configure: error: Cannot find pspell

　　解决方法：
　　yum -y install aspell-devel


37、checking for kstat_read in -lkstat… no checking for snmp_parse_oid in -lsnmp… no checking for init_snmp in -lsnmp… no configure: error: SNMP sanity check failed. Please check config.log for more information.

　　解决方法：
　　yum -y install net-snmp-devel

38、checking whether to enable XMLWriter support… yes, shared checking for xml2-config path… (cached) /usr/bin/xml2-config checking whether libxml build works… (cached) yes checking for XSL support… yes, shared configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution

　　解决方法：
　　yum -y install libxslt-devel

39、configure: error: xml2-config not found. Please check your libxml2 installation.

　　解决方法：
　　yum -y install libxml2-devel

40、checking for PCRE headers location… configure: error: Could not find pcre.h in /usr

　　解决方法：
　　yum -y install pcre-devel

41、configure: error: Cannot find MySQL header files under yes. Note that the MySQL client library is not bundled anymore!

　　解决方法：
　　yum -y install mysql-devel

42、checking for unixODBC support… configure: error: ODBC header file ‘/usr/include/sqlext.h' not found!

　　解决方法：
　　yum -y install unixODBC-devel

43、checking for pg_config… not found configure: error: Cannot find libpq-fe.h. Please specify correct PostgreSQL installation path

　　解决方法：
　　yum -y install postgresql-devel

44、configure: error: Cannot find pspell

　　解决方法：
　　yum -y install pspell-devel

45、configure: error: Could not find net-snmp-config binary. Please check your net-snmp installation.

　　解决方法：
　　yum -y install net-snmp-devel

46、configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution

　　解决方法：
　　yum -y install libxslt-devel

- 备注：

    原文地址：[https://www.cnblogs.com/liang-wei/p/6564424.html](https://www.cnblogs.com/liang-wei/p/6564424.html)
