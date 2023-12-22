We treat out software as a part of a larger pipeline made out of third party components. It allows our program to:
- be console-friendly and dockerable
- be correctly split into easy-to-use components
- support batch (mass) processing
- be a GUI wrapper over a bunch of non-interactive console programs

It prolongs a life of the application my making it an infrastructure together with maintining community around it.
## Performance

Build optimization tips:
- build with [ThinLTO](https://clang.llvm.org/docs/ThinLTO.html)

### Version assignment with Git

### version.h.in

```cpp
/* version.h.in - a configuration file processed by tools/update_version.cmake
 *
 * Copyright (c) 2021 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

#ifndef GRAMMAR2CODE_VERSION_H
#define GRAMMAR2CODE_VERSION_H

/** A string ID of a Git commit used to build the project */
#cmakedefine GRAMMAR2CODE_BUILD_COMMIT_ID "@GRAMMAR2CODE_BUILD_COMMIT_ID@"

/** A flag of GRAMMAR2CODE_BUILD_COMMIT_ID being a development prerelease */
#cmakedefine01 GRAMMAR2CODE_BUILD_IS_PRERELEASE

/** A flag of uncommited changes ahead of GRAMMAR2CODE_BUILD_COMMIT_ID */
#cmakedefine01 GRAMMAR2CODE_BUILD_IS_PATCHED

/**
 * A string time when the project was built, in %Y-%m-%d.%H-%M-%S format
 *
 * Defined for out-of-git builds (if GRAMMAR2CODE_BUILD_COMMIT_ID is undefined)
 * and builds from uncommited changes (if GRAMMAR2CODE_BUILD_IS_PATCHED is
 * defined).
 */
#cmakedefine GRAMMAR2CODE_BUILD_TIME "@GRAMMAR2CODE_BUILD_TIME@"

/**
 * @def GRAMMAR2CODE_VERSION
 * A string version in semver format
 */
#if !defined(GRAMMAR2CODE_BUILD_COMMIT_ID)
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev.private.modified." GRAMMAR2CODE_BUILD_TIME
#elif GRAMMAR2CODE_BUILD_IS_PATCHED
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev." GRAMMAR2CODE_BUILD_COMMIT_ID \
                             ".modified." GRAMMAR2CODE_BUILD_TIME
#elif GRAMMAR2CODE_BUILD_IS_PRERELEASE
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@" \
                             "-dev." GRAMMAR2CODE_BUILD_COMMIT_ID
#else
#define GRAMMAR2CODE_VERSION "@GRAMMAR2CODE_BUILD_VERSION@"
#endif

#endif // GRAMMAR2CODE_VERSION_H
```
