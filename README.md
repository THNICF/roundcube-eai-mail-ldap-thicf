Roundcube Webmail - Support login with EAI mail by THNIC Foundation
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

How to make Roundcube can login with EAI mail
---------------
Roundcube Webmail is used by [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/) and it is not UA-Ready (Universal Aceeptance) platform because it cannot use other languages email except English to login. Then, [THNIC foundation](https://xn--42cl2bj2hxbd2g.xn--12cfi8ixb8l.xn--o3cw4h/) modified it by query English email when THNIC fondation users used Thai email.

For example, ไทย@อีเอไอ.ไทย and thai@eai.in.th, the added function will find thai@eai.in.th and use it as username for login instead of อีเอไอ@คน.ไทย when users use Thai email to login.

The function named "map_users" is in index.php:

```
function map_users(&$auth){
  $conn = new mysqli("localhost", "your-db-user", "your-db-password", "your-database");
  if ($conn->connect_error) {
      error_log("Connection failed: " . $conn->connect_error,0);
  }
  //youremailmappingtable = the table that have record of your EAI mail and english email from $auth['user']
  $result = $conn->query("SELECT yourenglishemail FROM youremailmappingtable WHERE youreaiemail='".$auth['user']."'");
  if ($result->num_rows > 0) {
      $row = $result->fetch_row();
      $auth['user'] = $row['0'];
  }
}
map_users($auth);
```
Here the basic sql for create mapping email table:

```
CREATE TABLE youremailmappingtable (youreaiemail varchar(80) NOT NULL, yourenglishemail TEXT NOT NULL, PRIMARY KEY (youreaiemail) ); 
INSERT INTO youremailmappingtable (youreaiemail,yourenglishemail) VALUES ('ไทย@อีเอไอ.ไทย','thai@eai.in.th');
```

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
