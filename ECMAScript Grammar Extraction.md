To simplify writing a parser for [[Our Browser]], we extract the grammar from the specification using the following script:

`tools/update_grammar.py`:

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

"""
Update src/ecmascript/grammar.txt to the latest draft of ECMAScript.

This script first pulls the latest version of ECMA-262 parts into submodules:

- from <https://github.com/tc39/ecma262> to docs/specifications/ecma262
- from <https://github.com/tc39/test262> to tests/conformance/test262
- from <https://github.com/tc39/test262-parser-tests> to
  tests/conformance/test262-parser-tests

then uses them to regenerate src/ecmascript/grammar.txt.

This script may be called from any directory inside Dutyblasm repository.

Copyright © 2021 Oleg Iarygin <oleg@arhadthedev.net>

Distributed under the MIT software license; see the accompanying
file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
"""

from datetime import datetime
from pathlib import Path
from textwrap import dedent
from zoneinfo import ZoneInfo

from babel.dates import format_date  # type: ignore
from bs4 import BeautifulSoup  # type: ignore
from git.repo.base import Repo, Submodule


def update_submodule(dutyblasm: Repo, name: str) -> Submodule:
    """Update submodule to the latest version of its upstream.

    Args:
        dutyblasm: A Dutyblasm repository which submodule needs to be updated.
        name: A name of the submodule to update.

    Returns:
        An updated submodule for further manipulations.
    """
    submodule = dutyblasm.submodule(name)
    submodule.update(to_latest_revision=True)
    return submodule


def update_ecma262_copy(dutyblasm: Repo) -> tuple[Path, datetime]:
    """Update all components of ECMA-262 specification to the latest version.

    Args:
        dutyblasm: A Dutyblasm repository that needs update of ECMA-262.

    Returns:
        A tuple describing the updated specification: a path to its spec.html
        and its version as a timezone aware datetime of the last modification.
    """
    update_submodule(dutyblasm, 'tests/conformance/test262')
    update_submodule(dutyblasm, 'tests/conformance/test262-parser-tests')
    spec_text = update_submodule(dutyblasm, 'docs/specifications/ecma262')

    spec_datetime = spec_text.module().head.commit.committed_datetime
    return (Path(spec_text.path), spec_datetime)


def get_normative_grammar(specification_text: str):
    """Extract normative grammar parts from a specification.

    Note: For ECMAScript, the specification must be 2018 or later
    (Draft ECMA-262 / August 23, 2017 to be precise) because it introduces
    necessary attributes of `<emu-grammar>` element (see tc39/ecma262@aab1ea3).

    Args:
        specification_text: A Grammarkdown document.

    Returns:
        An iterator over extracted blocks of normative grammar.
    """
    ecma262 = BeautifulSoup(specification_text, 'html.parser')
    return ecma262('emu-grammar', type='definition', example=None)


# As manually taken from Draft ECMA-262 / November 16, 2021
OUTPUT = """/** grammar.txt - grammar described in Draft ECMA-262 / {version}
 *
 * This file is generated from docs/specifications/ecma262/spec.html.
 * To build a human readable version of the specification using npm, navigate
 * your command line interface to docs/specifications/ecma262 and run
 * `npm i && npm run build-only`; generation is done to `out` subdirectory.
 *
 * Copyright and Software License of the source:
 *
 * > Ecma International
 * > Rue du Rhone 114
 * > CH-1204 Geneva
 * > Tel: +41 22 849 6000
 * > Fax: +41 22 849 6001
 * > Web: https://ecma-international.org/
 * >
 * > Copyright Notice
 * >
 * > © {copyright_year} Ecma International
 * >
 * > This draft document may be copied and furnished to others, and derivative
 * > works that comment on or otherwise explain it or assist in its
 * > implementation may be prepared, copied, published, and distributed, in
 * > whole or in part, without restriction of any kind, provided that the above
 * > copyright notice and this section are included on all such copies and
 * > derivative works. However, this document itself may not be modified in any
 * > way, including by removing the copyright notice or references to Ecma
 * > International, except as needed for the purpose of developing any document
 * > or deliverable produced by Ecma International.
 * >
 * > This disclaimer is valid only prior to final version of this document.
 * > After approval all rights on the standard are reserved by Ecma
 * > International.
 * >
 * > The limited permissions are granted through the standardization phase and
 * > will not be revoked by Ecma International or its successors or assigns
 * > during this time.
 * >
 * > This document and the information contained herein is provided on an "AS
 * > IS" basis and ECMA INTERNATIONAL DISCLAIMS ALL WARRANTIES, EXPRESS OR
 * > IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTY THAT THE USE OF THE
 * > INFORMATION HEREIN WILL NOT INFRINGE ANY OWNERSHIP RIGHTS OR ANY IMPLIED
 * > WARRANTIES OF MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE.
 * >
 * > Software License
 * >
 * > All Software contained in this document ("Software") is protected by
 * > copyright and is being made available under the "BSD License", included
 * > below. This Software may be subject to third party rights (rights from
 * > parties other than Ecma International), including patent rights, and no
 * > licenses under such third party rights are granted under this license even
 * > if the third party concerned is a member of Ecma International. SEE THE
 * > ECMA CODE OF CONDUCT IN PATENT MATTERS AVAILABLE AT
 * > https://ecma-international.org/memento/codeofconduct.htm FOR INFORMATION
 * > REGARDING THE LICENSING OF PATENT CLAIMS THAT ARE REQUIRED TO IMPLEMENT
 * > ECMA INTERNATIONAL STANDARDS.
 * >
 * > Redistribution and use in source and binary forms, with or without
 * > modification, are permitted provided that the following conditions are
 * > met:
 * >
 * > 1. Redistributions of source code must retain the above copyright notice,
 * >    this list of conditions and the following disclaimer.
 * > 2. Redistributions in binary form must reproduce the above copyright
 * >    notice, this list of conditions and the following disclaimer in the
 * >    documentation and/or other materials provided with the distribution.
 * > 3. Neither the name of the authors nor Ecma International may be used to
 * >    endorse or promote products derived from this software without specific
 * >    prior written permission.
 * >
 * > THIS SOFTWARE IS PROVIDED BY THE ECMA INTERNATIONAL "AS IS" AND ANY
 * > EXPRESSOR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
 * > WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
 * > DISCLAIMED. IN NO EVENT SHALL ECMA INTERNATIONAL BE LIABLE FOR ANY DIRECT,
 * > INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
 * > (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
 * > SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
 * > CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 * > LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY
 * > OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF
 * > SUCH DAMAGE.
 */

{grammar}
"""


def generate_output_grammar(grammar, version: datetime) -> str:
    """Create content for grammar.txt.

    Args:
        grammar: An iterator over blocks of normative grammar for grammar.txt.
        version: A timezone aware datetime of the last modification.

    Returns:
        The content ready to be written.
    """
    aligned_sections = (dedent(section.string).strip() for section in grammar)
    merged_sections = '\n\n'.join(aligned_sections)

    version_date = version.astimezone(ZoneInfo('Europe/Zurich')).date()
    version_string = format_date(version_date, locale='en_US')

    return OUTPUT.format(
        version=version_string,
        copyright_year=version_date.year,
        grammar=merged_sections,
    )


if __name__ == '__main__':
    dutyblasm = Repo(search_parent_directories=True)
    ecma262_path, ecma262_version = update_ecma262_copy(dutyblasm)

    with open(ecma262_path / 'spec.html', 'r', encoding='utf8') as ecma262:
        grammar = get_normative_grammar(ecma262.read())

    with open('src/ecmascript/grammar.txt', 'w', encoding='utf8') as output:
        output.write(generate_output_grammar(grammar, ecma262_version))
```

`tools/requirements.txt`:

```plain
babel
beautifulsoup4
gitpython
tzdata
```