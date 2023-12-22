A part of [[Software Documentation]].

Example:

```man
.TH MEAD 1

.SH NAME
Mead (Microsoft Executables All-purposed Dwelling)\- Runs Windows applications
on POSIX-comparatible systems.

.SH SYNOPSIS
.В mead \fI[OPTIONS]\fR \fIAPPLICATION\fR \fI[APPLICATION OPTIONS]\fR

.SH DESCRIPTION
.B mead
is an Microsoft PE EXE loader.

The options supported by mead are:

.B --help
Show help message and exit

.B --version
Show version info and exit


.SH APPLICATION OPTIONS STRING
\fIAPPLICATION OPTIONS\fR is passed to running applications without any
preprocessing. According with Windows call conversion, the keys must begin
with “\\”, instead of POSIX “-” and “--” prefixes.

#Write about config file format in mead(5)
#.SH FILES
#BI \-~/.mead.conf
#Discs mapping, 

.SH COPYRIGHT
This manual page was written by Oleg Yarigin <dralife@yandex.ru>. Permission
is granted to copy, distribute and/or modify this document under the terms of
the GNU General Public License, Version 3 or any later version published by
the Free Software Foundation.
```