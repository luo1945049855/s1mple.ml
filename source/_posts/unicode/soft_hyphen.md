---
title:  "Soft Hyphen"
date:   2020-09-15
categories: Unicode
---

## Unicode Character And Encoding

Unicode: U+00AD

HTML Entity(decimal): &#173
HTML Entity(hex): &#xad
HTML Entity(named): &shy
How to type in Microsoft Windows: Alt+AD; Alt0173

UTF-8: 0xC2 0xAD (c2ad)
UTF-16: 0x00AD
UTF-32 0x000000AD
C/C++/Java source code: "\u00AD"
Python source code: u"\u00AD"

## Text to be formatted by the recipient

The use of SHY characters in text that will be broken into lines by the recipient is the application context considered by the post-1999 HTML and Unicode specifications, as well as some word-processing file formats. In this context, the soft hyphen may also be called a discretionary hyphen or optional hyphen. It serves as an invisible marker used to specify a place in text where a hyphenated break is allowed without forcing a line break in an inconvenient place if the text is re-flowed. It becomes visible only after word wrapping at the end of a line. The soft hyphen's Unicode semantics and HTML implementation are in many ways similar to Unicode's zero-width space, with the exception that the soft hyphen will preserve the kerning of the characters on either side when not visible. The zero-width space, on the other hand, will not, as it is considered a visible character even if not rendered, thus having its own kerning metrics.

To show the effect of a soft hyphen in HTML, the words of the following text[4] have been separated with soft hyphens:

<p>
    Margaret&shy;Are&shy;You&shy;Grieving&shy;Over&shy;Goldengrove&shy;Unleaving&shy;Leaves&shy;Like&shy;The&shy;Things&shy;Of&shy;Man&shy;You&shy;With&shy;Your&shy;Fresh&shy;Thoughts&shy;Care&shy;For&shy;Can&shy;You&shy;Ah&shy;As&shy;The&shy;Heart&shy;Grows&shy;Older&shy;It&shy;Will&shy;Come&shy;To&shy;Such&shy;Sights&shy;Colder&shy;By&shy;And&shy;By&shy;Nor&shy;Spare&shy;A&shy;Sigh&shy;Though&shy;Worlds&shy;Of&shy;Wanwood&shy;Leafmeal&shy;Lie&shy;And&shy;Yet&shy;You&shy;Will&shy;Weep&shy;And&shy;Know&shy;Why&shy;Now&shy;No&shy;Matter&shy;Child&shy;The&shy;Name&shy;Sorrows&shy;Springs&shy;Are&shy;The&shy;Same&shy;Nor&shy;Mouth&shy;Had&shy;No&shy;Nor&shy;Mind&shy;Expressed&shy;What&shy;Heart&shy;Heard&shy;Of&shy;Ghost&shy;Guessed&shy;It&shy;Is&shy;The&shy;Blight&shy;Man&shy;Was&shy;Born&shy;For&shy;It&shy;Is&shy;Margaret&shy;You&shy;Mourn&shy;For
</p>

On HTML browsers supporting soft hyphens, resizing the window will re-break the above text only at word boundaries, and insert a hyphen at the end of each line.

## Text preformatted by the originator

The SHY character is also used in text where paragraphs have already been broken into lines, such as certain plain text files, text sent to VT100-style terminal emulators or printers, or pages represented in page description languages. This is the application context originally considered by the EBCDIC and ISO 8859-1 standards and implemented in many VT100 terminal emulators.[1][2]

Here, SHY is a visible hyphen that is usually visually indistinguishable from a regular hyphen, but has been inserted solely for the purpose of line breaking. The purpose of the soft hyphen here is to distinguish it from any regular hyphen that might have been part of the original spelling of the word. This distinction helps re-use of already formatted text, when line breaks and soft hyphens inserted during word wrapping have to be removed to convert the text back into its unformatted form. For example, the copy or paste function of a terminal emulator can offer to replace line breaks with a space character, and remove any soft hyphens including any immediately following whitespace characters.

An example application that outputs soft hyphens for this reason is the groff text formatter as used on many Unix/Linux systems to display man pages.

## Reference

1. [Soft hyphen](https://en.wikipedia.org/wiki/Soft_hyphen)
2. [Soft hyphen (SHY) – a hard problem?](http://jkorpela.fi/shy.html)
3. [Unicode Character 'SOFT HYPHEN' (U+00AD)](http://www.fileformat.info/info/unicode/char/ad/index.htm)
