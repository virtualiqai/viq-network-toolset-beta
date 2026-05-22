# Third-Party Open-Source Component Acknowledgments

Virtual IQ AI NetOps Toolset incorporates open-source software components from the projects listed below. We are grateful to the open-source community for their work. Each component is used under the terms of its respective license, summarized in the table below.

The full license text for each component is included with the distributed binary and is also available from the project's upstream repository.

---

## Bundled Components

| Component | Version | License | Upstream |
|-----------|---------|---------|----------|
| **Flask** | ≥ 2.3 | BSD-3-Clause | https://palletsprojects.com/p/flask/ |
| **flask-cors** | ≥ 4.0 | MIT | https://github.com/corydolphin/flask-cors |
| **pysnmp** | ≥ 7.0 | BSD-2-Clause | https://github.com/lextudio/pysnmp |
| **psutil** | ≥ 5.9 | BSD-3-Clause | https://github.com/giampaolo/psutil |
| **dnspython** | ≥ 2.4 | ISC | https://www.dnspython.org/ |
| **requests** | ≥ 2.31 | Apache-2.0 | https://github.com/psf/requests |
| **paramiko** | ≥ 3.3 | LGPL-2.1 | https://www.paramiko.org/ |
| **asyncssh** | ≥ 2.14 | EPL-2.0 (used under EPL-2.0 election) | https://github.com/ronf/asyncssh |
| **openpyxl** | ≥ 3.1 | MIT | https://openpyxl.readthedocs.io/ |
| **Pillow** | ≥ 10.0 | HPND (MIT-compatible) | https://python-pillow.org/ |
| **pystray** | ≥ 0.19 | LGPL-3.0 | https://github.com/moses-palmer/pystray |
| **cryptography** | (build dep) | Apache-2.0 / BSD-3-Clause (dual) | https://cryptography.io/ |
| **cffi** | (build dep) | MIT | https://github.com/python-cffi/cffi |
| **pywin32** *(Windows only)* | (build dep) | PSF License | https://github.com/mhammond/pywin32 |
| **PyInstaller** | (build tool) | GPL-2.0 with bootloader exception | https://pyinstaller.org/ |
| **Python** | 3.13 | PSF License | https://www.python.org/ |
| **xterm.js** *(frontend)* | latest | MIT | https://xtermjs.org/ |

---

## LGPL Components — Replacement Notice

This software incorporates the following components licensed under the **GNU Lesser General Public License (LGPL)**:

- **paramiko** (LGPL-2.1)
- **pystray** (LGPL-3.0)

In accordance with the LGPL, users are entitled to use, copy, and modify these libraries. The bundled binaries link to these libraries dynamically (via Python's import system). To replace a bundled LGPL library with a modified version:

1. Obtain or build a modified copy of the library (e.g., `paramiko.zip` or installed `pystray/` package)
2. Locate the application's bundled libraries:
   - **Windows:** `C:\Program Files\VIQ Engineer Toolset\_internal\` (PyInstaller `_internal` directory)
   - **macOS:** `/Applications/VIQ Engineer Toolset.app/Contents/Resources/` and `/Applications/VIQ Engineer Toolset.app/Contents/Frameworks/`
3. Replace the corresponding library files with your modified version
4. Relaunch the application

Note that replacing bundled libraries may affect application stability; you assume responsibility for any such replacement.

The full LGPL-2.1 and LGPL-3.0 license texts are available from the Free Software Foundation:
- https://www.gnu.org/licenses/old-licenses/lgpl-2.1.txt
- https://www.gnu.org/licenses/lgpl-3.0.txt

---

## EPL-Licensed Component

**asyncssh** is dual-licensed under EPL-2.0 and GPL-2.0. Virtual IQ AI LLC elects to use asyncssh under the terms of the **Eclipse Public License 2.0**. The full EPL-2.0 text is available at https://www.eclipse.org/legal/epl-2.0/.

---

## PyInstaller Bootloader Exception

**PyInstaller** is licensed under GPL-2.0 with an explicit bootloader exception that permits commercial distribution of bundled applications without applying the GPL to the bundled application's own code. The full exception text is reproduced below:

> In addition to the permissions in the GNU General Public License, the authors give you unlimited permission to link or embed compiled bootloader and runtime files into combinations with other programs, and to distribute those combinations without any restriction coming from the use of those files.

This exception means the application code itself is not subject to GPL-2.0 by virtue of being packaged with PyInstaller.

---

## Frontend Components

The web user interface incorporates additional client-side libraries loaded at runtime in the user's browser. Each is governed by its own license, which is bundled with the library or available from its upstream project.

---

## Reporting an Issue with Attribution

If you believe a third-party component is missing from this list, or that an attribution is incorrect, please contact:

**info@virtualiqai.com** (subject line: `[LICENSE ATTRIBUTION]`)

We will investigate and correct promptly.

---

## License Compliance Statement

Virtual IQ AI LLC takes open-source license compliance seriously. The components listed above are used in accordance with their respective license terms. To the best of our knowledge, no AGPL-licensed code is incorporated into the distributed binary.

For any third-party component, the original license text takes precedence over the summary provided in this document.

---

*© 2026 Virtual IQ AI LLC. All rights reserved.*
