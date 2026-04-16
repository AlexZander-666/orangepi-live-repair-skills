# Serial Shell Recovery Patterns

## Minimal recovery sequence

1. Open the serial session.
2. Send a newline a few times to observe the shell state.
3. If repeated bare `>` prompts appear, send `Ctrl+C`.
4. Wait for a normal prompt.
5. Reset or clear the local receive buffer used for parsing.
6. Re-run a ready handshake that expects `marker:exitcode`.

## Ready-handshake rules

Use a ready handshake that:

1. is a single line
2. prints a marker plus an explicit exit code
3. does not rely on multiline single-quoted content

## Command-wrapper rules

Prefer:

1. one command
2. one explicit completion marker
3. one exit code

Avoid:

1. multiline wrappers in fragile sessions
2. heredocs before shell health is proven
3. quote-heavy nested wrappers when a simpler form exists
