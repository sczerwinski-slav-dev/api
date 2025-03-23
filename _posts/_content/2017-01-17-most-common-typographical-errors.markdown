###### Disclaimer

This post was first published on [JAVEO] blog, which is no longer available.

For everyone who may find it useful, I’ve made the article available here.

---

> The difference between something good and something great
> is attention to detail.
>
> —Chuck Swindoll

As software developers, we spend most of our time writing code.
Fully functional and bugless software is our primary objective.
Nice and pretty user interface is the responsibility of a&nbsp;graphic
designer or a&nbsp;framework, such as *Bootstrap* or *Polymer*.
We only provide the content, and here it is—arranged according
to the latest layout trends, enriched with elegant images,
glamorously shining with colour. If the content is bad, however,
not even the greatest framework can make it look good.

Written text is our greatest enemy. Many of us suffer from
dyslexia, which causes serious damage in our school essays,
cover letters, and even emails. But since we have some
spell-checking tools (and colleagues who review our work),
we can easily fix our errors. However, no tool and hardly
any reviewer can help us with our greatest obstacle.

Typography is not about spelling or grammar—it is about
the beauty and legibility of written text.
A graphic designer provides us with fonts, but usually,
it is our responsibility to use proper characters.
And no school prepares us for&nbsp;it.

## History

> We are not makers of history. We are made by history.
> 
> —Martin Luther King,&nbsp;Jr.

Once upon a time, the most common form of text was handwriting.
However, some people wanted to obtain text quality similar
to the printing press at home. It became possible with the
invention of the typewriter, which—due to size limitations—provided
at most several dozens of characters.

When the era of personal computers began, the first word processing
software came along. But hardware resources were extremely limited
back then. In 1963, the American Standards Association created a
character-encoding standard called ASCII, with only 95 printable
characters and 33&nbsp;control characters (a total of 128&nbsp;characters)
that could be represented by 7&nbsp;bits. A standard PC keyboard
(highly based on a typewriter keyboard) also uses the same narrow
set of numbers, letters and—most of all—punctuation symbols.
Because of those limitations, we have learnt to misuse some
characters, whenever the required symbol is unavailable.
‘If two or more characters look similar, we should use one
to represent them all’—apparently, this was the main idea
behind the ASCII standard. Fortunately, since UTF&#8209;8
is now the most popular standard on the Internet, we are
not restricted to 7&nbsp;bits anymore.

Honestly, we do not read as many books as our parents
and grandparents did. Web pages, on the other hand, are
usually created by people who do not know much about typesetting.
And since there is virtually no distinction between
similar punctuation marks in handwritten text,
we simply do not recognise the differences anymore.

## Dashes

> I was built for the long run, not for the short dash, I guess.
> 
> —William Shatner

There are no dashes on a computer keyboard. There is no minus either.
The only character we may type is a hyphen. Many text editors provide
an automatic conversion of space-hyphen-space into space-dash-space—which
is more or less correct—but that is not good enough.

### Hyphen

A **hyphen**&nbsp;(-) is usually used to group words into compound
modifiers, like in *over-the-counter drugs*, *mass-produced computer*,
*one-way ticket*, or *well-read person*. It might also be used to
indicate a prefix or a suffix, such as *pre-*, *post-*, *-ing*, *-ish*.
Another use case for hyphens would be hyphenation in a justified text.
The latter, however, is not practised in software user interface—it
is mostly used in printed text.

### En dash, em dash

When it comes to ranges of values, an **en dash**&nbsp;(–) should be used,
which is slightly longer and thinner than a hyphen. In most cases,
no space should be placed before or after the en dash, e.g. our
Android application has been downloaded 100–200 times. But there is
an exception to this rule—if both start and end of the range of dates
consist of more than one word. So one should write: *September–November 2010*,
*June 21–23*, but *April&nbsp;17 – May&nbsp;9*.

An **em dash**&nbsp;(—), on the other hand, is mostly used to indicate
a fragment of text, specifying the main subject. Its meaning is often
compared to parentheses, but there is a subtle semantic difference.
Parentheses indicate a by-the-way information, whereas dashes are used
with a core subject,&nbsp;e.g.:

* *The next Scalania will take place at the National Stadium—where
  JAVEO is located.*
* *This year’s Warsaw Book Fair will take place at the National
  Stadium (where JAVEO is located).*

In the first example, information about the location of JAVEO is crucial,
since we hold Scalania meetings. In the latter sentence it is not important
at all. It might mean: ‘you know, the stadium where JAVEO is located’,
or: ‘and by the way, JAVEO is located at the National Stadium’.

Em dashes might also be used to print dialogues,&nbsp;e.g.:

*—This code is a mess!—shouted Mike—Who wrote it?*

*—Try ‘git-blame’ to find it out.—John replied.*

It is not a typical way to represent a dialogue in English literature,
but it is common in some other languages.

In English there is typically no space right before or after the em dash.
In some other languages, however, spaces are commonly used. Some publishers
even use an en dash with spaces instead. The latter is often seen in Polish
publications.

### Minus

Dashes are designed to look nice in text—usually between two lower-case
letters. Numbers, on the contrary, look better with a **minus**&nbsp;(−)
character, which resembles an en dash, but is slightly shifted up.
Plus and minus signs should always be designed to match each other.
Actually, a minus should look like a horizontal bar from a plus character.

Note that in some cases a dash could be mistaken for a minus. You should
not stick to the rules, if they are likely to cause any confusion,
e.g. instead of: −10–10°C, you might want to write: −10&nbsp;–&nbsp;10°C,
or even&nbsp;−10—10°C.

## Quotation marks

> Quotation, n: The act of repeating erroneously the words of another.
> 
> —Ambrose Bierce

There are only two characters representing **quotation marks** in the ASCII
table—the single quotation mark (aka apostrophe) and the double quotation
mark—and neither of them is really correct. These are ambidextrous characters,
previously introduced to limit the number of the keys needed on a typewriter’s
keyboard. Properly, opening (left) and closing (right) quotation marks should
differ. In many fonts, they resemble respectively sixes and nines in superscript.

In American English, double quotation marks&nbsp;(“…”) are most commonly used.
Single quotation marks&nbsp;(‘…’) indicate a nested quotation, e.g. in this
excerpt from “Breaking Dawn” by Stephenie Meyer: *“Did you know that ‘I told
you so’ has a brother, Jacob?” she asked cutting me off. “His name is
‘Shut the hell&nbsp;up’.”*

In British English, it is usually just the other way about—single quotation
marks are primarily used, with nested double quotation marks.

In many languages, different forms of quotation marks are used. In Polish,
for instance, a low nine-shaped double quotation mark&nbsp;(„…”) opens
a quotation. Instead of nested quotation marks, on the other hand, one places
inverted guillemets&nbsp;(»…«), i.e. *this is „a&nbsp;»quote« inside
a&nbsp;quote”*.

### Apostrophe

As in the case of quotation marks, there is also no proper
**apostrophe**&nbsp;(’) on the keyboard. Instead of the default typewriter
character, a single closing quotation mark should be used, e.g. *YAML
Ain’t Markup Language*.

Some modern text editors automatically convert ASCII characters to proper
opening or closing quotation marks. This conversion is usually correct,
but there is one pitfall, you should beware of. If a year is abbreviated
to the last two digits, it should be preceded with an apostrophe.
So you can write, *I spent the New Year’s Eve of ’99 at home* (apostrophe),
but *there is a song called ‘99 Red Balloons’* (single quotation marks).
Most editors will convert both cases as the latter.

### Feet and inches

So finally, which characters should be used to indicate units of
measurement—such as feet and inches, or minutes and seconds—typewriter
quotation marks or proper closing quotation marks? The answer is, neither
of them. There are two other characters: **prime**&nbsp;(′) and
**double prime**&nbsp;(″),&nbsp;e.g.:

* *I am 5′&nbsp;10″ tall.*
* *The latitude and longitude of the JAVEO office are:
  52°&nbsp;14′&nbsp;20″&nbsp;N, 21°&nbsp;02′&nbsp;50″&nbsp;E.*

## Ellipses

> We danced on the beach, kissed on the beach and dot,&nbsp;dot,&nbsp;dot.
> 
> —Sophie reading Donna’s diary, “Mamma Mia!”, 2008

An **ellipsis**&nbsp;(…) or, in other words, ‘dot, dot, dot’,
has no single meaning. It might indicate a pause in speech,
but in a quotation, it is usually put instead of an omitted
fragment of text.

There is a common misconception that this punctuation mark is nothing
more than three full stops. However, since correct spacing is important
for the readability of ellipses, three successive periods would not be
typographically correct. So instead, a single Unicode ellipsis character
should be used.

## Spaces

> Space: the final frontier.
> 
> —Captain James T. Kirk, “Star Trek: The Original Series”

“How can I make any error in a space?”, you may ask.

Well… you can.

In general, spaces are used to separate words. Problems start when they
should be next to a punctuation mark. It is always good to remember that
spaces indicate places, where the text can be broken into lines. So you
should put a space after a punctuation mark related to the preceding contents,
and before a punctuation mark related to the following contents.

A full stop, for instance, indicates the end of a sentence. Thus, it should
not be preceded by a space. A similar rule applies to question marks,
exclamation marks, commas, colons, semicolons and ellipses.

Brackets and quotation marks, on the other hand, should be kept with
the text surrounded by them. So the space should be placed before the
opening mark, and after the closing mark.

### Units of measurement

When a number is followed by a unit symbol starting with a letter,
a space should be inserted in between, e.g. 1&nbsp;kg, 10&nbsp;m, 2&nbsp;h.
However, there are no spaces before non-alphabetic symbols like percent,
feet and inches, or degrees: 45°, 10%, 6′&nbsp;2″.

### Non-breaking spaces

In some cases, however, a non-breaking space is required, so that a line
break will be avoided. Measurement units are a good example—you probably
want to keep them right next to the number. It would look strange, if
the ‘kilogram’ from 1&nbsp;kg was suddenly moved to another line.

Not only unit symbols, but also any word describing a number should
be separated by a non-breaking space, e.g. a date: May&nbsp;9, or
a reference to another part of the document: chapter&nbsp;5,
section&nbsp;2.1, page&nbsp;104.

In some languages, there is a rule to never end a line of text with
a very short word. In Polish, for example, one-letter words should
always be followed by non-breaking spaces. On the other hand, in English,
it is perfectly fine to leave words like ‘I’ or ‘a’ at the end of the line
(unless it is the last word of the paragraph—then it looks peculiar).

---

These are just several errors that I have frequently seen in software
and documentation. However, there are many other rules that you should follow.

Also, as you may have noticed, some of the principles vary between languages.
It is always a good idea to consult the style guide before publishing the text.

## Cheat sheet for programmers

| Character                          | HTML       | Java String | Android XML | LaTeX                 |
| ---------------------------------- | ---------- | ----------- | ----------- | --------------------- |
| hyphen (-)                         | `-`        | `-`         | `-`         | `-`                   |
| en dash (–)                        | `&ndash;`  | `\u2013`    | `&#8211;`   | `--`                  |
| em dash (—)                        | `&mdash;`  | `\u2014`    | `&#8212;`   | `---`                 |
| minus (−)                          | `&minus;`  | `\u2212`    | `&#8722;`   | `$-$`                 |
| apostrophe (’)                     | `&rsquo;`  | `\u2019`    | `&#8217;`   | `'`                   |
| left single quotation mark (‘)     | `&lsquo;`  | `\u2018`    | `&#8216;`   | `` ` ``               |
| right single quotation mark (’)    | `&rsquo;`  | `\u2019`    | `&#8217;`   | `'`                   |
| left double quotation mark (“)     | `&ldquo;`  | `\u201C`    | `&#8220;`   | `` ` ` ``             |
| right double quotation mark (”)    | `&rdquo;`  | `\u201D`    | `&#8221;`   | `''`                  |
| double low-9 quotation mark („)    | `&bdquo;`  | `\u201E`    | `&#8222;`   | `,,`                  |
| left pointing guillemet («)        | `&laquo;`  | `\u00AB`    | `&#171;`    | `<<`                  |
| right pointing guillemet (»)       | `&raquo;`  | `\u00BB`    | `&#187;`    | `>>`                  |
| prime (′)                          | `&prime;`  | `\u2032`    | `&#8242;`   | `$'$` or `$\prime$`   |
| double prime (″)                   | `&Prime;`  | `\u2033`    | `&#8243;`   | `$''$` or `$\dprime$` |
| ellipsis (…)                       | `&hellip;` | `\u2026`    | `&#8230;`   | `\ldots`              |
| non-breaking space                 | `&nbsp;`   | `\u00A0`    | `&#160;`    | `~`                   |

## Bibliography

* [*ASCII*, Wikipedia][wiki:ascii]
* [*UTF-8*, Wikipedia][wiki:utf8]
* [*Dash*, Wikipedia][wiki:dash]
* [*Hyphen*, Wikipedia][wiki:hyphen]
* [*Plus and minus signs*, Wikipedia][wiki:plus_minus]
* [*Quotation mark*, Wikipedia][wiki:quot_mark]
* [*Apostrophe*, Wikipedia][wiki:apostrophe]
* [*Prime (symbol)*, Wikipedia][wiki:prime_sym]
* [*Ellipsis*, Wikipedia][wiki:ellipsis]
* [*Space (punctuation)*, Wikipedia][wiki:space]
* [*Non-breaking space*, Wikipedia][wiki:nbsp]
* [*Unicode Character Search*, FileFormat.Info][fileformat:search]
* [BrainyQuote]

---

## Credits

- [Image][pixabay:705674]
  by [Willi Heidelbach][pixabay:wilhei-883152:ref-705674]
  from [Pixabay][pixabay:ref-705674]


[wiki:ascii]: https://en.wikipedia.org/wiki/ASCII
[wiki:utf8]: https://en.wikipedia.org/wiki/UTF-8
[wiki:dash]: https://en.wikipedia.org/wiki/Dash
[wiki:hyphen]: https://en.wikipedia.org/wiki/Hyphen
[wiki:plus_minus]: https://en.wikipedia.org/wiki/Plus_and_minus_signs
[wiki:quot_mark]: https://en.wikipedia.org/wiki/Quotation_mark
[wiki:apostrophe]: https://en.wikipedia.org/wiki/Apostrophe
[wiki:prime_sym]: https://en.wikipedia.org/wiki/Prime_(symbol)
[wiki:ellipsis]: https://en.wikipedia.org/wiki/Ellipsis
[wiki:space]: https://en.wikipedia.org/wiki/Space_(punctuation)
[wiki:nbsp]: https://en.wikipedia.org/wiki/Non-breaking_space

[fileformat:search]: http://www.fileformat.info/info/unicode/char/search.htm

[BrainyQuote]: http://www.brainyquote.com/

[JAVEO]: http://www.javeo.eu

[pixabay:705674]: https://pixabay.com/photos/case-angle-hook-letterpress-brief-705674/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=705674
[pixabay:wilhei-883152:ref-705674]: https://pixabay.com/users/wilhei-883152/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=705674
[pixabay:ref-705674]: https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=705674
