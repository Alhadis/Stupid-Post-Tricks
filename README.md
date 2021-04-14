<a href="assets/sim.ps"><img align="left" alt="sim.ps" src="assets/simp.svg" width="200" /></a>

# PostScript snippets that I gone and dun.

Because who doesn't love a good `gs -sDEVICE=txtwrite -sOutputFile=- -q -sBATCH -dNOPAUSE -dNOSAFER *.ps`…?

<br clear="both" />

### [`asciify.ps`](asciify.ps)
An experiment in monkey-patching fixed-pitch typefaces to use ASCII quotes, instead of their directional Unicode counterparts. Doesn't work in `PSNormalizer.framework` (i.e., macOS/`Preview.app`); requires specific placement after a document's prologue (but before any rendered content).

### `inspect.ps` (moved to [`Alhadis/Inspect.ps`][])
A recursive inspection function that prints spiffy-looking colours to stdout. Basically, what `==` would be if it didn't suck at inspecting substructures:

<img alt="
Using ==
-dict-
Using ===
&lt;&lt;
&#x09;/FID =&gt; (FontID)
&#x09;/FontBBox =&gt; {
&#x09;&#x09;-161.0
&#x09;&#x09;-317.0
&#x09;&#x09;761.0
&#x09;&#x09;933.0
&#x09;}
&#x09;/.AGLprocessed~GS &gt; true
&#x09;/PaintType =&gt; 0
&#x09;/FontName =&gt; /Courier
&#x09;/PathLoad =&gt; (/usr/share/ghostscript/9.53.3/Resource/Font/NimbusMonoPS-Regular)
&#x09;/FontInfo =&gt; &lt;&lt;
&#x09;&#x09;/ItalicAngle =&gt; 0.0
&#x09;&#x09;/Notice =&gt; ((URW)++,Copyright 2014 by (URW)++ Design &amp; Development)
&#x09;&#x09;/Weight =&gt; (Regular)
&#x09;&#x09;/UnderlineThickness =&gt; 51
&#x09;&#x09;/FamilyName =&gt; (Nimbus Mono PS)
&#x09;&#x09;/version =&gt; (1.00)
&#x09;&#x09;/FullName =&gt; (Nimbus Mono PS Regular)
&#x09;&#x09;/isFixedPitch =&gt; true
&#x09;&#x09;/UnderlinePosition =&gt; -91
&#x09;&gt;&gt;
&gt;&gt;" src="assets/⩶.png" width="356" />

`inspect.ps` was originally part of this dumb repository, but later moved to a [dedicated repository][`Alhadis/Inspect.ps`]. I've left this entry intact as an explanation<a name="ref-1" href="#fn-1"><sup>[1]</sup></a> for any readers finding their way here from a link I posted to [Groff's mailing list](https://lists.gnu.org/archive/html/groff/2021-01/msg00085.html).

[`Alhadis/Inspect.ps`]: https://github.com/Alhadis/Inspect.ps


<!-- TODO: Find a way to generate the following mess. Preferably using Roff. -->

### [`misc.ps`](misc.ps)
Various other shite that doesn't belong anywhere else. Even in a repository as eclectic as this one.

<!-- =85 -->
<dl><dt><var><samp>string</samp></var>&nbsp;<var><samp>bool</samp></var>&nbsp;<a name="&#x3D;85"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/misc.ps#L3-L23"><dfn><code>=85</code></dfn></a>&nbsp;<var><samp>-</samp></var><br/>
<var><samp>string</samp></var>&nbsp;<dfn><code>=85</code></dfn>&nbsp;<var><samp>-</samp></var></dt>
<dd>Print an Ascii85-encoded representation of a string:

```postscript
(ABCD) =85 % => <~5sdq,~>
```

If the first operand is `true`, the input is treated as an [`/ASCIIHexEncoded`](https://www.adobe.com/content/dam/acom/en/devnet/actionscript/articles/PLRM.pdf#P.130 "ASCIIHexEncode Filter (PostScript Language Reference, 3rd ed., 1999, p. 130)") bytestream, and is decoded as such first:

```postscript
(ABCD)      true =85 % => <~X3C~>
(\000\200)  true =85 % => <~!.Y~>

% Whitespace is always ignored
( AB CD \n) true =85 % => <~X3C~>
< AB CD >   true =85 % => <~X3C~>
```

</dd>

<!-- apply-filter -->
<dt><var><samp>string</samp></var>&nbsp;<var><samp>name</samp></var>&nbsp;<a name="apply-filter"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/misc.ps#L25-L30"><dfn><code>apply-filter</code></dfn></a>&nbsp;<var><samp>string</samp></var></dt>
<dd>Apply an arbitrary decoding filter to a string:

```postscript
(48656C6C6F2C20776F726C642E)
/ASCIIHexDecode apply-filter % => (Hello, world.)
```

Currently, this doesn't work with encoding filters. See the [red book](https://www.adobe.com/content/dam/acom/en/devnet/actionscript/articles/PLRM.pdf#P.127 "PostScript Language Reference, 3rd ed., 1999, pp. 127-128") for a discussion on filter types.

</dd>

<!-- get? -->
<dt><var><samp>dict</samp></var>&nbsp;<var><samp>key</samp></var>&nbsp;<var><samp>default</samp></var>&nbsp;<a name="get&#x3F;"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/misc.ps#L32-L37"><dfn><code>get?</code></dfn></a>&nbsp;<var><samp>any</samp></var></dt>
<dd>Retrieve the value of a dictionary entry, falling back to a default value if no such entry was found:

```postscript
<< /Foo 1 >> /Foo null get? % => 1
<< /Foo 1 >> /Bar null get? % => null
```

</dd></dl>


### [`lists.ps`](lists.ps)
Despite being a stack-based language, PostScript has surprisingly limited functions for manipulating arrays and lists.<a name="ref-2" href="#fn-2"><sup>[2]</sup></a> Those I deem missing will eventually find their way into this file.


##### Array manipulation

<!-- last -->
<dl><dt><var><samp>array</samp></var>&nbsp;<a name="last"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L7-L10"><dfn><code>last</code></dfn></a>&nbsp;<var><samp>any</samp></var></dt>
<dd>Retrieve the last element of an array:

```postscript
[1 2 3]
last % => 1
```

</dd>

<!-- rev -->
<dt><var><samp>array</samp></var>&nbsp;<a name="rev"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L12-L15"><dfn><code>rev</code></dfn></a>&nbsp;<var><samp>array</samp></var></dt>
<dd>Return a copy of an array in reverse order:

```postscript
[1 2 3]
rev % => [3 2 1]
```

</dd>

<!-- append -->
<dt><var><samp>array</samp></var>&nbsp;<var><samp>value</samp></var>&nbsp;<a name="append"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L17-L24"><dfn><code>append</code></dfn></a>&nbsp;<var><samp>array</samp></var></dt>
<dd>Append a value to an array:

```postscript
[ 1 2 3 ]
4 append % => 1 2 3 4
```

</dd>

<!-- prepend -->
<dt><var><samp>array</samp></var>&nbsp;<var><samp>value</samp></var>&nbsp;<a name="prepend"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L26-L33"><dfn><code>prepend</code></dfn></a>&nbsp;<var><samp>array</samp></var></dt>
<dd>Prepend a value to an array:

```postscript
[ 1 2 3 ]
4 prepend % => 4 1 2 3
```

</dd></dt>


##### Operand stack manipulation

<!-- nth -->
<dl><dt><var><samp>any</samp></var>…&nbsp;<var><samp>index</samp></var>&nbsp;<a name="nth"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L41-L45"><dfn><code>nth</code></dfn></a>&nbsp;<var><samp>any</samp></var></dt>
<dd>Return the <var>𝑁<sup>th</sup></var> operand if <var>index</var> is positive, and <var>𝑁<sup>th</sup></var>-last if <var>index</var> is negative:

```postscript
% Positive indices
(A)(B)(C)(D)  0 nth % => (A) (B) (C) (D) (D)
(A)(B)(C)(D)  1 nth % => (A) (B) (C) (D) (C)

% Negative indices
(A)(B)(C)(D) -1 nth % => (A) (B) (C) (D) (A)
(A)(B)(C)(D) -2 nth % => (A) (B) (C) (D) (B)
```

This differs from PostScript’s `index` operator in that indices may be negative (similar to JavaScript’s [`Array.prototype.slice`](https://mdn.io/Array.prototype.slice)). Note that negative offsets are indexed from 1 instead of 0 (which otherwise refers to the first/right-most operand).<a name="ref-3" href="#fn-3"><sup>[3]</sup></a>

Irrespective of which end is being measured from, offsets that fall outside the operand stack will trigger a `rangecheck`.</dd>

<!-- shift -->
<dt><var><samp>any</samp></var>&nbsp;<a name="shift" href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L47-L50"><dfn><code>shift</code></dfn></a>&nbsp;<var><samp>-</samp></var></dt>
<dd>Remove the last (left-most) operand from the stack:

```postscript
1 2 3 4
shift % => 2 3 4
```

Not to be confused with <code>pop</code>, which removes the <em>first</em> operand instead.</dd>

<!-- unshift -->
<dt><var><samp>any</samp></var>&nbsp;<a name="unshift"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/b69ef0dd8a1d6ef4e8b0ee120e1b444966b885a7/lists.ps#L52-L55"><dfn><code>unshift</code></dfn></a>&nbsp;<var><samp>-</samp></var></dt>
<dd>Push an operand onto the end (left-side) of the stack:

```postscript
1 2 3
4 unshift % => 4 1 2 3
```

</dd>
</dl>


<footer><aside>

##### Totes unserious footnotes
1.&nbsp;<a name="fn-1" href="#ref-1">^</a> Also because I don't have the heart to delete that preview image's `alt` text.

2.&nbsp;<a name="fn-2" href="#ref-2">^</a> Yeah, okay, PostScript arrays are fixed-length records, but that still doesn't excuse the lack of operand helpers. Where's GhostScript's [`subr.el`](https://github.com/emacs-mirror/emacs/blob/50512e36c/lisp/subr.el) library, danggit?

3.&nbsp;<a name="fn-3" href="#ref-3">^</a> Confusing, I know. [Signed zeroes](https://en.wikipedia.org/wiki/Signed_zero) would’ve been handy here, had PostScript implemented [IEEE&nbsp;754](https://en.wikipedia.org/wiki/IEEE_754) like JavaScript did.

</aside></footer>
