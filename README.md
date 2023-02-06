<a href="assets/sim.ps"><!--
	FIXME: Safari 16 doesn't support media queries embedded in SVGs, whereas
	other modern browsers do (hence why we aren't using a single image source
	and leaving dark-mode to embedded CSS).
--><picture>
	<source media="(prefers-color-scheme: dark)" srcset="assets/simp.svg#dark" />
	<img align="left" alt="sim.ps" src="assets/simp.svg" width="190" />
</picture></a>

# PostScript snippets that I gone and dun.

Because who doesn't love a good `gs -sDEVICE=txtwrite -sOutputFile=- -q -sBATCH -dNOPAUSE -dNOSAFER *.ps`?

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

`inspect.ps` was originally part of this dumb repository, but later moved to a [dedicated repository][`Alhadis/Inspect.ps`]. I've left this entry intact as an explanation<sup class="footnote-ref"><a href="#fn-1" id="fnref-1" data-footnote-ref>1</a></sup> for any readers finding their way here from a link I posted to [Groff's mailing list](https://lists.gnu.org/archive/html/groff/2021-01/msg00085.html).

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

</dd>

<!-- numeric? -->
<dt><var><samp>any</samp></var>&nbsp;<a name="numeric&#x3F;"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/misc.ps#L39-L44"><dfn><code>numeric?</code></dfn></a>&nbsp;<var><samp>bool</samp></var></dt>
<dd>Return a boolean indicating whether argument is an integer or real:

```postscript
256 numeric? % => true
1.5 numeric? % => true
(1) numeric? % => false
```

</dd>

<!-- pagesize -->
<dt><var><samp>-</samp></var>&nbsp;<a name="pagesize"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/misc.ps#L46-L52"><dfn><code>pagesize</code></dfn></a>&nbsp;<var><samp>width</samp></var>&nbsp;<var><samp>height</samp></var></dt>
<dd>Ascertain the physical dimensions of the device's printing area:

```postscript
% When printing A4-sized pages:
pagesize % => 595.0 842.0
```

</dd></dl>


### [`geom.ps`](geom.ps)
Procedures for plotting simple geometry.

<!-- rect -->
<dl><dt><var><samp>width</samp></var>&nbsp;<var><samp>height</samp></var>&nbsp;<a name="rect"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/geom.ps#L3-L25"><dfn><code>rect</code></dfn></a>&nbsp;<var><samp>-</samp></var></dt>
<dd>Draw a rectangle centred at the current point:

```postscript
100 100 moveto
50 80 rect
stroke
```

</dd>

<!-- circle -->
<dt><var><samp>radius</samp></var>&nbsp;<a name="circle"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/geom.ps#L27-L35"><dfn><code>circle</code></dfn></a>&nbsp;<var><samp>-</samp></var></dt>
<dd>Draw a circle centred at the current point:

```postscript
100 100 moveto
10 circle
fill
```

</dd>

<!-- polygon -->
<dt>[<var><samp>xRadius</samp></var>&nbsp;<var><samp>YRadius</samp></var>]&nbsp;<var><samp>sides</samp></var>&nbsp;<a name="polygon"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/geom.ps#L37-L62"><dfn><code>polygon</code></dfn></a>&nbsp;<var><samp>-</samp></var><br/>
<var><samp>radius</samp></var>&nbsp;<var><samp>sides</samp></var>&nbsp;<dfn><code>polygon</code></dfn>&nbsp;<var><samp>-</samp></var></dt>
<dd>Draw an <var>𝑁</var>-sided polygon centred at the current point:
	
```postscript
% Draw a 100×60-sized hexagon
100 100 moveto
[100 60] 6 polygon
stroke

% Draw a solid-filled triangle
100 100 moveto
50 3 polygon
fill
```

</dd></dl>


### [`lists.ps`](lists.ps)
Despite being a stack-based language, PostScript has surprisingly limited functions for manipulating arrays and lists.<sup class="footnote-ref"><a href="#fn-2" id="fnref-2" data-footnote-ref>2</a></sup> Those I deem missing will eventually find their way into this file.


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

This differs from PostScript’s `index` operator in that indices may be negative (similar to JavaScript’s [`Array.prototype.slice`](https://mdn.io/Array.prototype.slice)). Note that negative offsets are indexed from 1 instead of 0 (which otherwise refers to the first/right-most operand).<sup class="footnote-ref"><a href="#fn-3" id="fnref-3" data-footnote-ref>3</a></sup>

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


### [`strings.ps`](strings.ps)
Procedures for string conversion and manipulation.

<!-- join -->
<dl><dt><var><samp>array</samp></var>&nbsp;<a name="join"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/strings.ps#L3-L16"><dfn><code>join</code></dfn></a>&nbsp;<var><samp>string</samp></var><br/>
<var><samp>string</samp></var>&nbsp;<var><samp>string</samp></var>&nbsp;<dfn><code>join</code></dfn>&nbsp;<var><samp>string</samp></var></dt>
<dd>Concatenate a list of strings:

```postscript
[ (Foo) (Bar) ] join % => (FooBar)
[ (A) (B) (C) ] join % => (ABC)
```

If only two strings are to be concatenated, they can be passed directly as operands:

```postscript
(Foo) (Bar) join % => (FooBar)
```

<!-- joind -->
<dt><var><samp>array</samp></var>&nbsp;<var><samp>delimiter</samp></var>&nbsp;<a name="joind"
href="https://github.com/Alhadis/Stupid-Post-Tricks/blob/master/strings.ps#L18-L27"><dfn><code>joind</code></dfn></a>&nbsp;<var><samp>string</samp></var><br/>
<var><samp>string</samp></var>&nbsp;<var><samp>string</samp></var>&nbsp;<var><samp>delimiter</samp></var>&nbsp;<dfn><code>joind</code></dfn>&nbsp;<var><samp>string</samp></var></dt>
<dd>Wrapper for <a href="#join"><code>join</code></a> that inserts <var><samp>delimiter</samp></var> between each pair of strings:

```postscript
[ (A) (B) (C) ]    (, ) joind % => (A, B, C)
[ (X) (Y) (Z) () ] (;)  joind % => (X;Y;Z;)
(Foo) (Bar)        (+)  joind % => (Foo+Bar)
```

</dd></dl>


<footer><aside>

##### Totes unserious footnotes
<section class="footnotes" data-footnotes>
<ol>
	<li id="fn-1">Also because I don't have the heart to delete that preview image's <code>alt</code> text. <a href="#fnref-1" class="footnote-backref" data-footnote-backref>↩</a></li>
	<li id="fn-2">Yeah, okay, PostScript arrays are fixed-length records, but that still doesn't excuse the lack of operand helpers. Where's GhostScript's <a href="https://github.com/emacs-mirror/emacs/blob/50512e36c/lisp/subr.el"><code>subr.el</code></a> library, danggit? <a href="#fnref-2" class="footnote-backref" data-footnote-backref>↩</a></li>
	<li id="fn-3">Confusing, I know. <a href="https://en.wikipedia.org/wiki/Signed_zero">Signed zeroes</a> would’ve been handy here, had PostScript implemented <a href="https://en.wikipedia.org/wiki/IEEE_754">IEEE&nbsp;754</a> like JavaScript did. <a href="#fnref-3" class="footnote-backref" data-footnote-backref>↩</a></li>
</ol>
</section>

</aside></footer>
