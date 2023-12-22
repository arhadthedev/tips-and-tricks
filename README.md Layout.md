Both [[Desktop Application]] and [[Web Service Backend]] should have a file describing the purpose and instruction on how to deploy the sources.

1. Application name as a second level header
	1. Just a verbal report what the application gives
	2. A single concept describing what the application allows to get
	3. The single-concept approach allows to early find out what functions must be split into another application and how it should communicate with the first application
	4. If the report is longer than seven words, the application tries to chew more than a user can concentrate on
2. A single-sentence description of what the application allows to achieve. Commands to a user. Resolves a recurrent task/need of the user
3. Table of Content
4. Competition advantages
5. Developer-oriented build instructions (`How to Build`)
6. User-oriented installation instructions (`How to Install`)
7. A short instruction on how, when and where exactly to make a good report via GitHub Issues, Ubuntu Launchpad, and so on (`How to Report Bugs`)
8. Developer-oriented patch submission instructions (`How to Submit Changes`)
9. Licensing section ([[MIT License]])

# Example

```markdown
## 5s-seiri-desktop-background-generator

Implement the *Sort* part of the Japanese *5S* methodology on your desktop directories and files. Specify where the red labeled box corners should appear on your desktop then manually group files and directories into them.

### Table of Content

1. [How to Install](#how-to-install)
1. [How to Report Bugs](#how-to-report-bugs)
1. [How to Build](#how-to-build)
1. [How to Submit Changes](#how-to-submit-changes)
1. [Licensing](#licensing)

### How to Build

To compile 5s-seiri-desktop-background-generator, next components need to be installed:

- Python version 3.8 or above

From the top-level directory of the 5s-seiri-desktop-background-generator source (which contains this file), run:

	cmake

[TBD]

### How to Install

[TBD]

### How to Report Bugs

[TBD]

### How to Submit Changes

[TBD]

### Licensing

The source for *5s-seiri-desktop-background-generator* is released under the MIT license. See LICENSE.md for more details.
```

An older (circa 2013) example that shows how deviations from the convention influence a look:

```markdown
==================================================
Mead (Microsoft Executables All-Purposed Dwelling)
==================================================

Run x32 and x64 Microsoft© Windows© programs on Linux.

It consists of a program loader which loads and executes a Microsoft Windows binary, and a library set that implements Windows API calls using their Linux equivalents.

===================================================================
What difference is between Mead and [Wine](http://www.winehq.org/)?
===================================================================

Both Wine and Mead are API wrappers. However, there are some essential differences:

1. While Wine draws GUI by itself, Mead uses [Qt](http://qt.digia.com/)\`s GUI subsystem, that makes Windows applications more convenience with system theme and simplifies the code.
2. While Wine tries to completely reconstruct Windows© environment, providing system utilities, Mead *may* contains wrapping hyperlinks for essential utilities only.

=========
Compiling
=========

For compiling Mead next components need to be installed:

1. [Qt](http://qt.digia.com/) version 4 or above
2. Linux development headers, corresponding to your OS version

From the top-level directory of the Wine source (which contains this file), run:

    qmake

=========
Licensing
=========

The source for *Mead* is released under [GNU General Public License](http://www.gnu.org/licenses/gpl.html) as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.
```

Another, even older example:

```markdown
grammar2code, a generator of direct code scannerless parsers.

A version of the project is specified in vcpkg.json, see `version-string` key.

This generator was initially created for ECMAScript.


DOWNLOAD
--------

Release tarballs: <https://github.com/arhadthedev/grammar2code/releases>

Source code:

\`\`\`shell
$ git clone https://github.com/arhadthedev/grammar2code.git
\`\`\`

LICENSE
-------

Copyright © 2021 Oleg Iarygin <oleg@arhadthedev.net>

Distributed under the MIT software license; see the accompanying file
LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.

```

# Node.js Application or Library

Software published on NPM should have a different layout. They have lots of explanatory animations and describe exported functions.

Examples:
- https://web.archive.org/web/20231210110045/https://www.npmjs.com/package/faucet
- https://web.archive.org/web/20230323172203/https://www.npmjs.com/package/make-tap-output
- https://web.archive.org/web/20221230193953/https://www.npmjs.com/package/bl