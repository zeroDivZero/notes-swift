# ARGUMENT

On Command line, everything after program name is passed as arg to program. Can be separated by whitespace. 2 types: _option_ and _string_.

## Option

Starts with dash followed by character, or 2 dashes followed by word. E.g., typically `-h` or `--help` (first shortcut for second) to show help.

## Add Args in Xcode

During dev or debugging, in Xcode, edit scheme. Go to **Run** > **Arguments** > **Arguments Passed On Launch**, add args 1 by 1. Remember to include dash(es).
