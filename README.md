Roundcube Webmail - Support login with EAI mail by THNIC Foundation (LDAP method)
=================
1. [roundcube.net](https://roundcube.net)

2. [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/)

[![Tests Status](https://github.com/roundcube/roundcubemail/actions/workflows/tests.yml/badge.svg?branch=master)](https://github.com/roundcube/roundcubemail/actions/workflows/tests.yml)

ATTENTION
---------
1. This is just a snapshot from the GIT repository and is **NOT A STABLE
version of Roundcube**. It's not recommended to replace an existing installation
of Roundcube with this version. Also using a separate database for this
installation is highly recommended.

2. This is a modified version by [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/) to make a Roundcube Webmail can loging by using EAI (Email Address Internationalization) focus on Thai language. 

3. This is a proof of concept version (POC) 

INTRODUCTION
------------
Roundcube Webmail is a browser-based multilingual IMAP client with an
application-like user interface. It provides full functionality you expect
from an email client, including MIME support, address book, folder management,
message searching and spell checking. Roundcube Webmail is written in PHP and
requires the MySQL, PostgreSQL or SQLite database. With its plugin API it is
easily extendable and the user interface is fully customizable using skins.

The code designed to run on a webserver is mainly written in PHP and Javascript.
It includes a custom framework with an IMAP library derived from [IlohaMail][iloha]
and requires a set of external libraries (see composer.json and jsdeps.json files).

THNIC Foundation forked and modified code to support login by [EAI email](https://xn--12cn4frcvb5f.xn--o3cw4h/%e0%b8%8a%e0%b8%b7%e0%b9%88%e0%b8%ad%e0%b8%ad%e0%b8%b5%e0%b9%80%e0%b8%a1%e0%b8%a5%e0%b8%a0%e0%b8%b2%e0%b8%a9%e0%b8%b2%e0%b9%84%e0%b8%97%e0%b8%a2-eai/)


INSTALLATION
------------
For detailed instructions on how to install Roundcube webmail on your server,
please refer to the INSTALL document in the same directory as this document.

If you're updating an older version of Roundcube please follow the steps
described in the UPGRADING file.


BROWSER SUPPORT
---------------
Roundcube uses jQuery 3.x (and other libs) for its client and therefore
inherits the browser support from there. This currently includes:

- Chrome: (Current - 1) and Current
- Edge: (Current - 1) and Current
- Firefox: (Current - 1) and Current, ESR
- Internet Explorer: 11+
- Safari: (Current - 1) and Current
- Opera: Current

How to make Roundcube can login with EAI mail (LDAP method)
---------------
Roundcube Webmail is used by [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/) and it is not UA-Ready (Universal Aceeptance) platform because it cannot use other languages email except English to login. Then, [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/) modified it by query English email when THNIC fondation users used Thai email.

For example, ไทย@อีเอไอ.ไทย and thai@eai.in.th, the added function will find thai@eai.in.th and use it as username for login instead of อีเอไอ@คน.ไทย when users use Thai email to login.

The LDAP script to get English email user from EAI email user:

```
list($local, $domain) = explode('@', $auth['user']);
exec('ldapsearch -H ldaps://your_ldap_rul -D "cn=your_cn_value,dc=your_dc_value,dc=your_dc_value" -w "your_ldap_password" -b  "ou=your_ou_value,ou=domains,dc=your_dc_value,dc=your_dc_value" -s sub "(cano='.$local.')" | grep "cn:"', $resp, $returnResp);
if($returnResp==0) {
    $auth['user'] = substr($resp[0],4) . '@' . "your.email.value";
}
```

The LDAP script to save log data to your ldap (Can delete if not use):

```
       function open_ldap_connection() {
            global $log_prefix, $LDAP, $SENT_HEADERS, $LDAP_DEBUG;
            $LDAP['uri'] = "ldaps://your_ldap_rul";
            $LDAP['admin_bind_dn'] = "dc=your_dc_value,dc=your_dc_value";
            $LDAP['admin_bind_pwd'] = "your_ldap_password";
            $log_prefix = date('Y-m-d H:i:s') . " - LDAP manager ";

            $ldap_connection = @ ldap_connect($LDAP['uri']);
            if (!$ldap_connection) {
                print "Problem: Can't connect to the LDAP server at ${LDAP['uri']}";
                die("Can't connect to the LDAP server at ${LDAP['uri']}");
                exit(1);
            }
            ldap_set_option($ldap_connection, LDAP_OPT_PROTOCOL_VERSION, 3);
            if(!preg_match("/^ldaps:/", $LDAP['uri'])) {
                $tls_result = @ ldap_start_tls($ldap_connection);
                if ($tls_result != TRUE) {
                    error_log("$log_prefix Failed to start STARTTLS connection to ${LDAP['uri']}: " . ldap_error($ldap_connection),0);
                    if ($LDAP["require_starttls"] == TRUE) {
                        print "<div style='position: fixed;bottom: 0;width: 100%;' class='alert alert-danger'>Fatal:  Couldn't create a secure connection to ${LDAP['uri']} and LDAP_REQUIRE_STARTTLS is TRUE.</div>";
                        exit(0);
                    } else {
                        if ($SENT_HEADERS == TRUE) {
                            print "<div style='position: fixed;bottom: 0px;width: 100%;height: 20px;border-bottom:solid 20px yellow;'>WARNING: Insecure LDAP connection to ${LDAP['uri']}</div>";
                        }
                        ldap_close($ldap_connection);
                        $ldap_connection = @ ldap_connect($LDAP['uri']);
                        ldap_set_option($ldap_connection, LDAP_OPT_PROTOCOL_VERSION, 3);
                    }
                } else if ($LDAP_DEBUG == TRUE) {
                    error_log("$log_prefix Start STARTTLS connection to ${LDAP['uri']}",0);
                }
            }
            $bind_result = @ ldap_bind( $ldap_connection, $LDAP['admin_bind_dn'], $LDAP['admin_bind_pwd']);

            if ($bind_result != TRUE) {
                $this_error = "Failed to bind to ${LDAP['uri']} as ${LDAP['admin_bind_dn']}";
                if ($LDAP_DEBUG == TRUE) {
                    $this_error .= " with password ${LDAP['admin_bind_pwd']}";
                }
                $this_error .= ": " . ldap_error($ldap_connection);
                print "Problem: Failed to bind as ${LDAP['admin_bind_dn']}";
                error_log("$log_prefix $this_error",0);
                exit(1);
            } else if ($LDAP_DEBUG == TRUE) {
                error_log("$log_prefix Bound to ${LDAP['uri']} as ${LDAP['admin_bind_dn']}",0);
            }
            return $ldap_connection;
        }

        //Updte login time to your ldap server
        //Example ou=people,ou=eai.in.th,ou=domains,dc=eai,dc=th
        function ldap_log($user) {
            $ldap_connection = open_ldap_connection();
            $to_update["lastlogintime"] = time();
            list($local, $domain) = explode('@', $user);
            $updated_login_log = ldap_mod_replace($ldap_connection, "cn=$local,ou=your_ou_value,ou=your_ou_value,ou=domains,dc=your_dc_value,dc=your_dc_value", $to_update);
        }
        ldap_log($auth['user']);
```

Also we have modified 3 files to make Adress book and Sending EAI mail working:

1. rcmail_sendmail.php
2. rcube_utils.php
3. rcube_user.php


This is not the best solution yet. Please feel free to fork or modified this repositories.

LICENSE
-------
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License (**with exceptions
for skins & plugins**) as published by the Free Software Foundation,
either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see [www.gnu.org/licenses/][gpl].

This file forms part of the Roundcube Webmail Software for which the
following exception is added: Plugins and Skins which merely make
function calls to the Roundcube Webmail Software, and for that purpose
include it by reference shall not be considered modifications of
the software.

If you wish to use this file in another project or create a modified
version that will not be part of the Roundcube Webmail Software, you
may remove the exception above and use this source code under the
original version of the license.

For more details about licensing and the exceptions for skins and plugins
see [roundcube.net/license][license]


CONTACT
-------
For bug reports or feature requests please refer to the tracking system
at [Github][githubissues] or subscribe to our mailing list.
See [roundcube.net/support][support] for details.

You're always welcome to send a message to THNIC Foundation:
info(at)thnic(dot)or(dot)th.

MORE INFORMATION
----------------
1. [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/)

2. [รู้จัก.ไทย](https://xn--12cn4frcvb5f.xn--o3cw4h/)


[iloha]:        https://sourceforge.net/projects/ilohamail/
[gpl]:          https://www.gnu.org/licenses/
[license]:      https://roundcube.net/license
[contrib]:      https://roundcube.net/contribute
[support]:      https://roundcube.net/support
[githubissues]: https://github.com/roundcube/roundcubemail/issues
