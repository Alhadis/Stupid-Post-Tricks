<a href="assets/simp.ps"><img align="left" alt="simp.ps" src="assets/simp.svg" width="200" /></a>

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
Despite being a stack-based language, PostScript has surprisingly limited functions for manipulating arrays and lists. Those I deem missing will eventually find their way into this file.
