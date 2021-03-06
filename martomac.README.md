# [martomac.py](martomac.py) -- Markup to `Macro()` format converter

use: `python martomac.py [options] markdownFile[.md] [macroFile]`

## Available options

Option | Effect
------ | ------
  -t | generate `mtmtestfile.html` through `macro()`  
  -o | open `mtmtestfile.html` in OS X default browser
  -c | do not prefix with built-in styles \(use your own\!\)
  -s | suppress generated blank lines between block elements
  -x | suppress filename report
  -p | output goes to stdout
  -l | strip tailing newlines from list elements
  -d | dump canned styles to file "cannedstyles.txt" for your reference
  -h | wraps content of "cannedstyles.txt" or stdout in basic HTML page
  -m macroFileName | provide your own styles that may override the canned styles

## Some Examples:

### Normal use:

    python martomac.py README.md

Generates:

Output | Contents | Errors
-------|----------|-------
file `README.txt` | file ready for input to macro\(\) | **stderr**
**stdout** | Report of filename\(s\) to be used
**stderr** | Errors

### With addional test file output:

    python martomac.py -t README.md

Generates:

Output | Contents
-------|---------
file `README.txt` | file ready for input to macro\(\)
file `mtmtestfile.html` | output generated by macro\(\)
**stdout** | Report of filename\(s\) to be used
**stderr** | Errors

### Output convenient for pipes:

    python martomac.py -x -p README.md

Generates:

Output | Contents
-------|---------
**stdout** | text stream ready for input to macro\(\)
**stderr** | Errors

### Generate and view output with your browser:

    python martomac.py -t -o -h -x README.md

\(mtm.md is the test file I supply -- I'm still working
on compatability with less masic use of markdown. Mostly
it's order of parsing and the like.\)

Output | Contents
-------|---------
mtmtestfile.html | Opens right in your OS X browser
**stderr** | Errors

### Customizing your output:

You can customize almost anything about the actual conversion
process, because it's managed by styles; styles you can override.

For instance:

In the default set of `macro()` styles that are used to
handle the markdown-to-macro conversion, there's a style
that sets the color of code blocks. This is it:

    [style codewrap [color 080 [b]]]  

Now, perhaps you're prefer your code to be some other color or shade.
Perhaps red. No problem. Using the `-m` option, you can supply a
replacement for this \(or anything else you want to change about how the
conversion is handled.\) Just create a file, I'll call this one
`mycustommd.mac`, and put the replacement style in it:

    [style codewrap [color 800 [b]]]  

Now, when you invoke `martomac.py`, just add this to your command...

    -m mycustommd.mac

...and there you go. Red code.

Of course, you can put far more in there than just color. You could
set up a span with custom inline or cascaded CSS and do virtually
anything you like.

### Better image handling

Markdown doesn't handle width= and height= within an image tag,
because it doesn't know squat about your image other than the
web path that's in the url.

`macro()` can be made to handle this properly, even in the context
of a converted Github or markdown file. The default behavior within
`martomac.py` is to not handle it, just like markdown. The image
style that is in place by default is...

    [style img [split [co],[b]][img [parm 0],[urlencode [parm 1]]]]

...which just catches your URL from the markdown image spec,
encodes it, and shoves it in the image as-is.

So when you say `{img pics/mypic.jpg}`, you get this...

    <img src="pics/mypic.jpg">

...and that's the end of that. And the web surfer's browser will snap and
twitch and rejigger the page layout until it figures out the actual image
size to be used. By reading the image once it downloads it. Yech.
Just like Markdown.

But if you take a moment to prepare a file with some overrides it it, you
can avoid this. Let's say your images are in:

    /usr/www/svr.com/htdocs/pics/

All you have to put in the override file \(see the -m option\) is...

    [lipath /usr/www/svr.com/htdocs/pics/]
	[style img [split [co],[b]][locimg [parm 0],[urlencode [parm 1]]]]

...now the conversion will use the new '{img}' style instead of the default,
which utilizes the smarter `[locimg]` built-in, and it in turn will know
where your images are, because you told it with
the `[lipath]` built-in. So it opens them, checks them out, and viola,
you get something like this:

    <img width=75 height=100 src="pics/mypic.jpg">

### Readability

In order to make this all work as a customizable conversion, I've used
styles much more liberally than would be typical. The end result -- of
the conversion, not the HTML -- is infested, as it were, with these
styles. That's not how I normally write `macro()` source, nor do I
expect it would be how others do so. There are *far* more effective and
less wordy \(and more readable\) ways to accomplish all of this if
you're writing it directly, rather than trying to parse out markdown
text.

So please don't be deterred by how the conversion results come out.
If that was how things *had* to look in general, I wouldn't have bothered
to write `macro()` in the first place.

## On Github's habit of Eating HTML tags

Github eats many useful -- and harmless -- HTML tags. You can't specify
a serif font, or a text color, or a whole raft of other things. The
explanation is that these tags "might harm your documents" \(cough\)

Markdown, however, doesn't do this, assuming that as the
author / maintainer / arbiter of the document, you know what you're doing.
Which - frankly - is the right thing to do. You write your own docs on Github,
and if someone pushes something you didn't actually *check* and you accept it...
well, your fault if your document becomes unreadable or weird looking, eh?

There are a few exceptions -- scripts could be used by you as a blackhat
with Github as the target, so yes, I can see why they'd kill those, but
FFS, sometimes you need other things \(ever try to discern a capital
letter I from a lower case L in Github's monospaced font? That kind of
nonsense is why TT tags normally use a serif font in the first place.\)

So. I **don't** take out your tags. Github processing or not. You should
have control over what tags you have in there because you should *know*
what tags you have in there.

## Note: Or, "Perl before Swine" \(*Oink!*\)

This task would ideally have been accomplished by taking the Perl
markdown-to-html generator and changing it to output a minimal subset of
macro\(\) code, as that would result in a far higher quality conversion;
one that does exactly what markdown does in the same contexts.

But I don't code in Perl any longer. I actually downloaded and looked at
the markdown Perl code, and all that did was profoundly re-affirm my
decision not to code in Perl. It just doesn't suit the way my mind works.

So. If someone else wants to go after this particular bit of fruit,
which, if you're really into Perl, you might even find to be
low-hanging, by all means do so and I'd be delighted to have it join the
rest of this fluffy goodness.

Otherwise you get my converter, which was seat-of-the-pants and only
works properly within the context of precisely correct \(and still
incomplete, see [todo.md](todo.md)\) markdown syntax. The markdown
processor itself is pretty relaxed about unclosed italics and bold,
unescaped special characters and so on. `martomac.py` is not.

The downside of that is that even after I work through everything
remaining on [todo.md](todo.md) some of your `.md` files may not convert
correctly. In fact, if you manage to get unbalanced braces of any flavor
in the .md file outside of a code block or span, or fail to escape them
properly, the resulting `macro()` input will not be fully parsable,
which will leave you with truncated HTML results.

The upside is that it may tell you that you might need to fix your
markdown. :\)

On the *other* hand, if you find a conversion not working that you think
should work, push the markdown file to the `aa_macro` repo and I will
take a look -- or of course you can implement any fixes yourself, if you
are so inclined.

See the supplied [test file](mtm.md) and the other markdown files in the
`aa_macro` repo here for a look at what is known to work.

