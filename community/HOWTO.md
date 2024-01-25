# Custom Puzzles

This is a guide to making, editing, and fixing custom puzzles for Silicon Zeroes. If you aren't interested in any of those things, you're in the wrong place!

## Overview

Silicon Zeroes puzzles are composed of a directory containing several different kinds of files. On launch, the game non-recursively scans for puzzles in community/ (<INSTALLDIR>/community/ on Windows and Linux, ~/Library/Application Support/Silicon Zeroes/community/ on OS X).

To create a puzzle, just make a directory inside community/ with whatever name you like - say, 'foo'. Start up the game, click 'Community', and 'COMM-foo' should appear!

## Metadata

By default, a puzzle's in-game name will be "COMM-foo", where 'foo' is the name of the directory the puzzle is in. If you want to change this, you need a metadata file.

Go into your puzzle's directory and create a new text file, 'metadata.mmeta'. At the top, you'll want to add two lines:

```METADATA```
This tells the game this is indeed a metadata file, and not some other misnamed file.

```0.01```
Indicates the metadata format version you're using.

Then you're ready to start specifying how your puzzle works! The following elements are all optional, and can be in any order.

### Display name

```NAME: Eniac```
Sets your puzzle's displayed in-game name.

Note that saved puzzles, records, and Steam histograms use the internal name, `COMM-<directory name>`. Changing the displayed name will not affect those, for better or worse.

### Modules

```MODULES: Adder, Reader, Instruction Decoder```
Lists the types of modules that users can place in your puzzle. If you pre-place modules of other types in your puzzle, they will appear grey and users will not be allowed to delete them.

Recognized module types:
`Number`, `Adder`, `Latch`, `Writer`, `Reader`, `Instruction Decoder`, `Input Selector`, `Output Selector`, `Equals`, `Subtract`, `Op Selector`, `Register`, `Multiplier`

### Tick Limit

```TICKS: Five ticks per instruction, plus five more.```
A description for players of the number of ticks allowed per test. Is NOT enforced mechanically. Generally not recommended for non-delay puzzles, since it tends to be more of a distraction than anything relevant there.

### Module & Tick Goals

To set the 'goals' for a puzzle (optimization targets for players), use this syntax:

`MODULEGOAL: 5`
`TICKGOAL: 28`

### Misc. Flags

```NO_CONFIG```
Modules (Numbers, Input/Output Selectors, Registers) may not be configured in this puzzle.

```DELAY```
Modules take time to stabilize after their inputs change.

```AUTOCLOCK```
The clock is set automatically to allow all modules to stabilize before writing.

### Comments

Lines beginning with a `#` are treated as comments and ignored.

## Descriptions

Puzzles are generally more playable with descriptions for what the player is supposed to be doing. This is accomplished with another file, `description.mdesc`.

As with .mdata files, .mdesc files start with two special lines:

```PUZZDESC```
Tells the game this is indeed a description file.

```0.01```
Description format version.

The next line should be the name of a character from the game:
```Carol```

This indicates who's talking. The allowed characters are:

`You`, `Alicia`, `Carol`, `Fred`, `Laszlo`, `Jack`, `Shadow Jack`

'You' is a special case, and will not display a portrait or the character's name ('-- Alicia') after the description.

All subsequent text will be displayed in-game as the description.

### Text Coloring

Two text colors are available.

Text \~surrounded by tildes\~ will appear red in-game. In the main campaign, red text is used for mechanical instructions: e.g. \~set each slot to 1\~, or \~you cannot configure modules\~.

Text \`surrounded by backticks\` will appear green in-game. This is used to emphasize mechanics, concepts, and hints that are not part of the formal ruleset of the puzzle.

### Victory Text

The text that appears after beating a puzzle can also be configured. The game will attempt to load it from `victory.mdesc`. The format is identical to the main puzzle description, with the exception that text coloring is not displayed, for technical reasons.

## Tests

Players beat puzzles by beating all of the tests for that puzzle. A test is composed of an initial memory state, a goal memory state, and a tick (time) limit: the test is beaten if memory reaches the goal state before it reaches the tick limit.

To add tests to your puzzle, create a 'tests.mtest' file. This will begin with a two-line header:

```
PUZZTEST
0.01
```

Followed by a series of tests. Tests will appear in-game in the order they appear in the file.

### Test Format

Each test begins with this line:

```START```

And ends with this:

```END```

In between, the initial memory state, goal memory state, and tick limit are specified.

### Initial Memory

Initial memory is specified by a series of numbered slots. (They need not be in order, though keeping them ordered may be simpler to read.) Example:

```5: 10```
This specifies that slot 5 initially contains the literal value '10'.

Several other types of values can be specified:

```6: ?```
The 'unknown/unstable' value.

```7: -```
The 'nil/unpowered' value. Will not appear in initial memory (all slots are considered 'unpowered' by default), but possibly useful for something.

```8: 'A'```
Letter values, enclosed in single-quotes. 'A'-'Z' and ' ' are all supported.

```9: MSET  1  - 10```
Instructions, specified as the opcode, the source, the target, and the destination, in that order. '-'s are optional and ignored (the parser only looks for the values used by a specific instruction), but make tests more readable.

Only slots >= 0 are supported. (You can't have, e.g., '-1: 5'.)

### Expected Memory

To set the expected/goal memory, insert this line after the end of initial memory within a test:

```EXPECTED```

Then, continue writing lines, just as with initial memory.

*IMPORTANT*: Expected memory inherits from initial memory; all slots are considered to be the same as initial memory *unless specified otherwise!*

### Tick Limit

To specify the allowed number of ticks for a test, put this line anywhere between the `START` and `END` of a test:

```LIMIT: 10```
(Or whatever number of ticks are appropriate for the test.)

This limit should line up with the 'tick limit description', mentioned above (in the 'metadata file'). Otherwise, your players will be quite confused!

### Comments

Lines beginning with `#` are treated as comments and ignored.

### More Tests

If you only have one or a few tests, players may be able to exploit this by only producing the outputs those tests expect, rather than solving a broader problem.

Creating large number of tests, perhaps via a script, is sometimes a better approach.

## Initial Modules

If you want to have puzzles start with some modules pre-placed (perhaps so that players only have a limited number of them available), you'll want to add an `initial.mftsv` file.

It's possible to edit such a file manually, but the format is the same as normal save files, so my recommendation is instead to set up your initial desired configuration in-game (allowing full placement of whatever modules are needed), and then to copy and rename the save file into your puzzle's folder.

Note that any modules of a type not available for placement in a puzzle are also undeleteable.

## Debugging

If something you set in one of the files isn't going into the game as you expect, try checking the logs for errors. On OS X, the logs are in ~/Documents/SZ_log.txt ; on Windows and Linux, they're in <INSTALLDIR>/log.txt .

## Document Info

This is version 0.03 of the editing guide. For feedback, please feel free to message pleasingfung@gmail.com with questions, suggestions, etc. Thanks!
