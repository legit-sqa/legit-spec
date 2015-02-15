# File Structure
## File Format
Files within the PROPOSAL DIRECTORY shall contain any number of name value pairs, followed by text:

<pre>
file   := *(header) "\r\n" text
header := name ":" value "\r\n"
name   := &lt;Any letter, number or hyphen&gt;
value  := &lt;Any (US-ASCII) CHAR, leading and trailing whitespace is ignored&gt;
text   := &lt;Any (US-ASCII) CHAR)&gt;
</pre>