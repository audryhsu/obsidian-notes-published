---
creation date: 2022-03-17 09:23
aliases: 
tags: 🖥️
---
# Background
Unicode was invented to create a single character set that worked internationally and consider special characters from various languages in such a way that international communication across the internet did not require special "code pages".

Code pages were invented to try and codify what different countries/languges were doing with characters stored at 128 to 255. The context is that when ASCII was created in the US, we were able to represent all unaccented english letters and digits inside 7 bits, or using numbers between 32 and 127, when most computers were using 8-bit-bytes which left one bit left to spare. This is where people got wild and started using the spare  bit for different characters. In ANSI standard, the each country's users had a Code Page which dictated everyone used the same characters under 128, but wrote out what they used differently above 128.

### Unicode
Old way of thinking about characters:
Character --> bits stored in memory
A -> 0100 0001

Unicode myth: unicode is a 16-bit code where each character takes 16 bits of space. 

This is false because Unicode doesn't say anything about *how a character is stored in memory*. Unicode simply maps a letter to a *code point* hexidecimal number. A **code point is a theortical concept, which still needs to be represented on memory or on disk.** 

U+ is Unicode, and the numbers are **hexadecimal**. U+0639 is the Arabic letter Ain. The English letter A would be U+0041. 

"Hello"
which, in Unicode, corresponds to these five code points:
U+0048 U+0065 U+006C U+006C U+006F.

New way of thinking:
Character --> Unicode --> code point (int) --> encoding?? 

#### Battle of encodings (UTF-8)

*Encoding* means to map characters to binary so computers can store them. 
so then the story becomes about how do we encode Unicode and store it in memory? 

At first, they tried storing Unicode in 2 bytes per code point (AKA UTF-16 because it has 16 bits). Some people hated it because of this double the amount of memory it took to store a string (e.g. Americans that didn't need all the extra characters)

Then, they invented **UTF-8, a system for storing string of Unicode code points (U+ numbers) in memory using 8 bit bytes**. Every code point from 0-127 is stored in a single byte, code points 128+ are stored using 2,3, and up to 6 bytes. The side effect is that for English, characters were stored in the same place as in ASCII. 

You can encode Unicode code points in any encoding scheme, but you might get ? or boxes if there is no equivalent for the Unicode code point you're trying to represent in the encoding you're trying to represent it in. 

 > It does not make sense to have a string without knowing what encoding it uses. You can no longer stick your head in the sand and pretend that “plain” text is ASCII.

**There Ain’t No Such Thing As Plain Text.**

If you have a string, in memory, in a file, or in an email message, ==you have to know what encoding it is in or you cannot interpret it or display it to users correctly.==

If you get ? marks in your email, blame the developer. 

#### How to preserve what encoding was used for a string
For an email message, you are expected to have a string in the header of the form

>Content-Type: text/plain; charset="UTF-8"



---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md) 

Reference: https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/
Related: 