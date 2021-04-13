<a href="assets/sim.ps"><img align="left" alt="sim.ps" src="assets/simp.svg" width="200" /></a>

# PostScript snippets that I gone and dun.

Because who doesn't love a good `gs -sDEVICE=txtwrite -sOutputFile=- -q -sBATCH -dNOPAUSE -dNOSAFER *.ps`…?

<br clear="both" />

### [`asciify.ps`](asciify.ps)
An experiment in monkey-patching fixed-pitch typefaces to use ASCII quotes, instead of their directional Unicode counterparts. Doesn't work in `PSNormalizer.framework` (i.e., macOS/`Preview.app`); requires specific placement after a document's prologue (but before any rendered content).

### [`inspect.ps`](inspect.ps)
A recursive inspection function that prints spiffy-looking colours to stdout. Basically, what `==` would be if it didn't suck at inspecting substructures:

<img alt="
Using ==
-dict-
Using ===
&lt;&lt;
	/FID =&gt; (FontID)
	/FontBBox =&gt; {
		-161.0
		-317.0
		761.0
		933.0
	}
	/.AGLprocessed~GS &gt; true
	/PaintType =&gt; 0
	/FontName =&gt; /Courier
	/PathLoad =&gt; (/usr/share/ghostscript/9.53.3/Resource/Font/NimbusMonoPS-Regular)
	/FontInfo =&gt; &lt;&lt;
		/ItalicAngle =&gt; 0.0
		/Notice =&gt; ((URW)++,Copyright 2014 by (URW)++ Design &amp; Development)
		/Weight =&gt; (Regular)
		/UnderlineThickness =&gt; 51
		/FamilyName =&gt; (Nimbus Mono PS)
		/version =&gt; (1.00)
		/FullName =&gt; (Nimbus Mono PS Regular)
		/isFixedPitch =&gt; true
		/UnderlinePosition =&gt; -91
	&gt;&gt;
&gt;&gt;" src="assets/⩶.png" width="356" />

### [`lists.ps`](lists.ps)
Despite being a stack-based language, PostScript has surprisingly limited functions for manipulating arrays and lists.<a name="ref-1" href="#fn-1"><sup>[1]</sup></a> Those I deem missing will eventually find their way into this file.


##### Array manipulation

<dl><dt><a name="last"><var><samp>array</samp></var>&nbsp;<dfn><code>last</code></dfn>&nbsp;<var><samp>any</samp></var></a></dt>
<dd>Retrieve the last element of an array:

```postscript
[1 2 3]
last % => 1
```

</dd>

<dt><a name="rev"><var><samp>array</samp></var>&nbsp;<dfn><code>rev</code></dfn>&nbsp;<var><samp>array</samp></var></dt>
<dd>Return a copy of an array in reverse order:

```postscript
[1 2 3]
rev % => [3 2 1]
```

</dd>

<dt><a name="prepend"><var><samp>array</samp></var>&nbsp;<var><samp>value</samp></var>&nbsp;<dfn><code>prepend</code></dfn>&nbsp;<var><samp>array</samp></var></dt>
<dd>Prepend a value to an array:

```postscript
[ 1 2 3 ]
4 prepend % => 4 1 2 3
```

</dd>

<dt><a name="append"><var><samp>array</samp></var>&nbsp;<var><samp>value</samp></var>&nbsp;<dfn><code>append</code></dfn>&nbsp;<var><samp>array</samp></var></dt>
<dd>Append a value to an array:

```postscript
[ 1 2 3 ]
4 append % => 1 2 3 4
```

</dd></dt>

##### Operand stack manipulation

<dl><dt><a name="nth"><var><samp>any</samp></var>…&nbsp;<var><samp>index</samp></var>&nbsp;<dfn><code>nth</code></dfn> <var><samp>any</samp></var></dt>
<dd>Return the <var>𝑁<sup>th</sup></var> operand if <var>index</var> is positive, and <var>𝑁<sup>th</sup></var>-last if <var>index</var> is negative:

```postscript
% Positive indices
(A)(B)(C)(D)  0 nth % => (A) (B) (C) (D) (D)
(A)(B)(C)(D)  1 nth % => (A) (B) (C) (D) (C)

% Negative indices
(A)(B)(C)(D) -1 nth % => (A) (B) (C) (D) (A)
(A)(B)(C)(D) -2 nth % => (A) (B) (C) (D) (B)
```

This differs from PostScript’s `index` operator in that indices may be negative (similar to JavaScript’s [`Array.prototype.slice`](https://mdn.io/Array.prototype.slice)). Note that negative offsets are indexed from 1 instead of 0 (which otherwise refers to the first/right-most operand).<a name="ref-2" href="#fn-2"><sup>[2]</sup></a>

Irrespective of which end is being measured from, offsets that fall outside the operand stack will trigger a `rangecheck`.</dd>

<dt><a name="shift"><var><samp>any</samp></var>&nbsp;<dfn><code>shift</code></dfn>&nbsp;<var>–</any></a></dt>
<dd>Remove the last (left-most) operand from the stack:

```postscript
1 2 3 4
shift % => 2 3 4
```

Not to be confused with <code>pop</code>, which removes the <em>first</em> operand instead.</dd>

<dt><a name="unshift"><var><samp>any</samp></var>&nbsp;<dfn><code>unshift</code></dfn>&nbsp;<var>–</var></a></dt>
<dd>Push an operand onto the end (left-side) of the stack:

```postscript
1 2 3
4 unshift % => 4 1 2 3
```

</dd>
</dl>


<footer><aside>

##### Totes unserious footnotes
1.&nbsp;<a name="fn-1" href="ref-1">^</a> Yeah, okay, PostScript arrays are fixed-length records, but that still doesn't excuse the lack of operand helpers. Where's GhostScript's [`subr.el`](https://github.com/emacs-mirror/emacs/blob/50512e36c/lisp/subr.el) library, danggit?

2.&nbsp;<a name="fn-2" href="ref-2">^</a> Yes, this is confusing, I know. [Signed zeroes](https://en.wikipedia.org/wiki/Signed_zero) would’ve been handy here, had PostScript implemented [IEEE&nbsp;754](https://en.wikipedia.org/wiki/IEEE_754) like JavaScript did.

</aside></footer>
