ProcessStream
===================

Create and pipe between processes easily using node streams

# What is this?

Working with processes in node should be easy, at least as easy as:

```bash
cat manifest.json | ./strip-comments.pl | node get-authors | jsonpp
```

This module hopes to make that as easy as:

```node
var ps = require('process-stream');
fs.createReadStream('manifest.json')
    .pipe(ps('./strip-comments.pl'))
    .pipeTo('node get-authors')
    .pipeOut('jsonpp');
```

And maybe even improve upon the classic shell pipe:

```node
var ps = require('process-stream');
var interestingStream = fs.createReadStream('manifest.json')
    .pipe(ps('./strip-comments.pl'))
    .pipeTo('node get-authors');
interestingStream.pipeOut('jsonpp');
interestingstream.pipe(fs.createWriteStream('authors.json'));
```

# Why?

Treating processes as streams in node is not as easy as you'd think.  The main issue is exit codes.  You'd expect an
unclean exit code to cause an 'error' event to be emitted from a process-related stream, but I haven't found a library
that does this yet.  This library is my attempt to rectify the situation.

This causes real issues.  The one I came across was in using the Graphics Magick node wrapper.  It offers a
`gm().operation().stream()` interface, but silently fails, leaving empty files around, if Graphics Magick couldn't
recognise the given file.  This leaves streams as 2nd-class citizens, since `gm().operation().write()` has no such
issue.
