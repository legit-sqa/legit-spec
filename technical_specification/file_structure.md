# File Structure
A folder named `.tracking` should be placed in the root of the directory, within an orphan branch, named `.tracking`. The structure is initiated as follows:

<pre>
.tracking/
   |- proposals/
   |  |- open
   |  |- pending
   |- users/
      |- (empty)
</pre>

## File Format
All files within this directory shall, unless otherwise stated, consist of any number of name value pair headers, followed by body text:

<pre>
file   := *(header) "\r\n" body
header := name ":" value "\r\n"
name   := &lt;Any letter, number or hyphen, leading and trailing whitespace is ignored&gt;
value  := &lt;Any (US-ASCII) CHAR, other than "\r" or "\n", leading and trailing whitespace is ignored&gt;
body   := &lt;Any (US-ASCII) CHAR)&gt;
</pre>