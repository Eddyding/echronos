lupHw1: All project (i.e., non-third-party) Python files shall be PEP8 compliant.
Rationale: consistent code style and improved readability.

u1wSS9: The command 'x.py check-pep8' shall check for compliance of all project Python files.
Rationale: an automated check allows to detect and resolve non-compliance efficiently.

TZb0Uv: The maximum line length in project Python files is 118 characters.
Rationale: a maximum line length allows for convenient source code handling with standard text management tools on the command line.
Since an 80-character limit is considered overly restrictive, 120 characters are a viable compromise on modern displays.
A reduction by 2 to 118 characters allows to manage indented source code (e.g., by diff) on 120-character wide displays.

tGT1n0: In docstrings and comments of project Python files, each sentence shall start on a new line.
Rationale: simplifies file handling with line-oriented tools, such as diff (same rationale as for plain text files).

g0O2QN: RTOS changes visible to application developers, in particular changes to RTOS concepts, APIs, or configuration items, always need to be accompanied by the corresponding updates to their documentation.
Rationale: helps to keep the documentation up-to-date with the implementation.

eIhEVe: All text in the repository shall use American English spelling and grammar (as opposed to British or Australian spelling or grammar).

29g3DU: The following convention should be used for the naming of symbols in component-specific RTOS code:
1. Symbols in component-specific code implemented in the component itself: <component-name>_<functionality>.
2. Symbols in component-specific code required by the component and implemented in the variant: <component-name>_core_<functionality>.

2g8PAE: Specific API and internal assertions are not considered API features.
Instead, they are considered implementation details that may change any time.
Specific API assertions and internal assertions are therefore not to be documented in the RTOS manual.

SKcASp: All documentation uses present tense.
Other tenses are only acceptable where they are necessary grammatically for clarity.