This is a simple builder script that attempts to take a series of python files starting with a main.py into a single file. Most useful for scripts or cmd line utilities.

Assumptions:
- The program spawns from a singular main.py file
- All internal imports are in the same tree of directories as the main.py file
- Internal imports are wrapped by comment headers and footers `# begin internal import` and `# end internal import`

Limitations:
- Does not adjust formatting or import order
- Does not de-conflict `as` imports with different names

`builder.py`:
```python
#!/usr/bin/env python3
import logging as log
from pathlib import Path

from helper import PythonFileDetails, find_file, get_import_lines_and_program_lines, prepare_output

SHEBANG = "#!/usr/bin/env python3\n" 

FORMAT = "%(levelname)-5s - %(message)s"
log.basicConfig(format=FORMAT, level=log.DEBUG)

output_dir_path = Path("..") / "<output-dir-location>"
output_file_path = Path(output_dir_path) / "<output_file_name>.py"

prepare_output(output_dir_path, output_file_path)

source_dir_path = Path("..") / "<source-dir-location>"

# Keep track of all the non-internal imports to minimize duplication
all_imports: set[str] = set()
# Queue with the internal files found while reading the last file
files_to_read: list[str] = []
# Keep track of the files read, so they are not read again and get a circular dependency
files_read: set[str] = set()

# Put all the files read on a stack so that they can be expanded in the output in the right order
file_stack: list[PythonFileDetails] = []

log.info("Starting build...")
# Always start with the main.py file
source_main_path = Path(source_dir_path) / "main.py"
main_file: PythonFileDetails = get_import_lines_and_program_lines(source_main_path)

all_imports.update(main_file.import_lines)
files_to_read.extend(main_file.other_file_names)
file_stack.append(main_file)

while len(files_to_read) != 0:
    file_short_name: str = files_to_read.pop(0)
    if file_short_name in files_read:
        # Skip files that have been read
        continue
    files_read.add(file_short_name)

    current_file = get_import_lines_and_program_lines(find_file(file_short_name + ".py", source_dir_path))

    file_stack.append(current_file)
    all_imports.update(current_file.import_lines)
    files_to_read.extend(current_file.other_file_names)

log.info("Writing output script: %s", output_file_path)
with Path.open(output_file_path, "w") as out_file:
    out_file.writelines([SHEBANG])
    out_file.writelines(all_imports)
    while len(file_stack) != 0:
        out_file.writelines(file_stack.pop().program_lines)

log.info("Build Finished")

```

`helper.py`:
```python
import logging as log
import os
import re
import sys
from pathlib import Path
from typing import List, Self

COMMENT_START = "#"
INTERNAL_IMPORT = "internal import"
INTERNAL_IMPORT_HEADER = f"{COMMENT_START} begin {INTERNAL_IMPORT}"
INTERNAL_IMPORT_FOOTER = f"{COMMENT_START} end {INTERNAL_IMPORT}"
BUILD_FAILURE = "Build Failure"


class PythonFileDetails:
    import_lines: frozenset[str] = frozenset()
    program_lines: List[str] = []
    other_file_names: frozenset[str] = frozenset()

    def __init__(self: Self, import_lines: frozenset[str], program_lines: List[str], other_file_names: frozenset[str]) -> None:
        self.import_lines = import_lines
        self.program_lines = program_lines
        self.other_file_names = other_file_names


def is_import_line(line_str: str) -> bool:
    if line_str.startswith("import") or line_str.startswith("from"):
        return True
    return False


def prepare_output(output_dir_path: Path, output_file_path: Path) -> None:
    log.info("Preparing output location")
    if Path.exists(output_file_path):
        Path.unlink(output_file_path)
        log.info("Deleted %s", output_file_path)
    elif not Path.exists(output_dir_path):
        Path.mkdir(output_dir_path)
    else:
        log.info("Nothing to do. Proceeding")


def find_file(name: str, path: Path) -> Path:
    result = []
    for root, _, files in os.walk(path):
        if name in files:
            result.append(Path(root) / name)
    if len(result) == 0:
        log.error("Internal import %s could not be found. Exiting.", name)
        sys.exit(BUILD_FAILURE)
    elif len(result) != 1:
        log.error("Multiple copies of internal import %s found.", name)
        log.error("Found in %s", result)
        log.error("Ensure only one copy is present in %s. Exiting.", path)
        sys.exit(BUILD_FAILURE)
    return result.pop()


def get_import_lines_and_program_lines(file_path: Path) -> PythonFileDetails:
    log.info("Reading file: %s", file_path)
    file = Path.open(file_path)
    file_lines = file.readlines()

    internal_imports = False

    import_lines: set[str] = set()
    other_file_names: set[str] = set()
    file_line_out: List[str] = []

    for line in file_lines:
        if line.startswith(INTERNAL_IMPORT_HEADER):
            internal_imports = True
            continue
        if internal_imports:
            if is_import_line(line):
                regex_result = re.search(r"^from\s*(\S*)\s*import", line)
                if regex_result is not None:
                    fully_qualified_name = regex_result.group(1)
                    other_file_names.add(fully_qualified_name.split(".")[-1])
            elif line.startswith(INTERNAL_IMPORT_FOOTER):
                internal_imports = False
                continue
        elif is_import_line(line):
            import_lines.add(line)
        elif not line.strip().startswith("#!"):
            file_line_out.append(line)
    file.close()

    if internal_imports:
        log.error(
            "Internal imports header(%s) detected but the matching footer(%s) was not found. "
            "No lines after the header were included in final build. Please add footer. Exiting.",
            INTERNAL_IMPORT_HEADER,
            INTERNAL_IMPORT_FOOTER,
        )
        sys.exit(BUILD_FAILURE)

    return PythonFileDetails(frozenset(import_lines), file_line_out, frozenset(other_file_names))

```