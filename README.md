
# Preprint+ – A generic LaTeX template

A clean, general purpose preprint template that doesn’t get in the way.

This was forked from the [`preprint-template.tex`](https://github.com/brenhinkeller/preprint-template.tex/blob/master/preprint.sty) (itself a fork of the [`arxiv-style`](https://github.com/kourgeorge/arxiv-style) template) and extended to better support manuscripts that will be submitted to journals. This lead to the following user-facing changes:

- A rich collection of option flags making every feature optional.
  This allows authors to turn off features that may clash with the journal’s template.
- Additional options to set fonts, title style, page dimensions, running header….
  + These are all features that are hard-coded in the original template.[^not-a-diss]
  + This is not, however, an all-you-can-style template.
    The idea is to provide a few useful sets of configurations, so that authors who’d rather think of other things than LaTeX styles nevertheless have a few options beyond Times New Roman font or letter-sized paper.
  + Authors who _do_ want to think about a particular aspect of styling — be it the font style, title format, page geometry, etc. — can turn that feature off, and still benefit from not having to think about the other aspects.
- A revised interface for specifying abstract and keywords in the preamble, and have them typeset as part of `\maketitle`. This is more in line with how one would prepare a manuscript using e.g. Springer-Nature’s or PNAS’ templates.
- A new `\supplementary` command for typesetting Supplementary material as part of the PDF. This allows cross-references between the main text and supplementary, while still making it easy to submit the latter to the journal as a separate file.
  + This complements the `\appendix` command, which in standard classes will typeset subsequent material as part of the text, just restarted numbering.
  + Most journals/conferences will have _either_ a supplementary or an appendix, so their templates typically define `\appendix` (or the `appendix` package) to work with their style. Having both `\appendix` and `\supplementary` commands allows us to provide both layout formats; it also allows you to use both, for those cases where it would make sense.[^why-both]

Under the hood, the style file has been completely rewritten in LaTeX3 syntax,[^modern-latex] which is how we are able to provide all of these options without it becoming an unmaintainable mess.
In fact, the organization into separate files arguably makes it _easier_ to maintain, despite the variety of options.
For example, font options are easily added to `preprint+fonts.sty` by emulating one of the code blocks already there, and then adding a corresponding option under `font .choice:` in `preprint.sty`.

![](side-by-side_thumbnails.png)

To get almost identical output to the original `preprint-template.tex`, use the following to import the package:

```latex
\documentclass[twocolumn]{article}
\usepackage[font=times,
            status=preprint,
            geom=twocolumn,
            floatplacement=medium,
            headingstyle=smallcaps,
            marginticks
            ]{preprint+}
```

this will produce the output on the right. Note the use of the `times` font, typical of computer science conferences, which produces quite compact output. It’s also on the heavier side, with very thick serifs.

In contrast, the letter glyphs of the [`erewhon`](https://tug.org/FontCatalogue/erewhon/) are similar to `times` but with notably lighter serifs.[^lighter-serifs]
This option also selects a math font closer to what one finds in journals. The result, shown on the left, is a layout not quite as compact, but with a bit more space to breath.
(This example happens to exaggerate the size differences, because many lines in the default happen to have only one word.[^can-scale-erewhon])

If you just `\usepackage{preprint+}` with default options, this is equivalent to

```latex
\documentclass[twocolumn]{article}
\usepackage[font=erewhon,
            geom=twocolumn,
            floatplacement=tight,
            suppfloatplacement=extratight,
            headingstyle=bold,
            ]{preprint+}
```


[^not-a-diss]: This is not to disparage the authors of the original templates. Before LaTeX3, providing multiple package options was a notoriously masochistic exercise, so it’s commonplace for style files to hard-code everything; users who want to modify anything are expected to do directly in the style file. If you’re thinking “but the whole point of using a `.sty` file is that I don’t _want_ to think about styling LaTeX documents”, that’s exactly what lead to `preprint+.sty`.

[^why-both]: Physical Review is one example of a journal which allows for both, with Supplemental Material reserved for things like extensive data tables.

[^modern-latex]: Native LaTeX3 support was added to the standard LaTeX kernel in June 2022. This means you will need a reasonably up to date version of LaTeX. If your version is older than this, you can still use the style files, but must add `\usepackage{expl3}` at the top of `preprint+.sty`. Notably, this will be the case if you are on Ubuntu LTS 22.04.

[^lighter-serifs]: For example, the stem of the y is straighter, and the s loses its blocky serifs. The associated Utopia math font has much simpler shapes, notable e.g. with the uppercase Q and black board fonts.
  This `erewhon` font option also selects the sans serif typewriter font [`inconsolata`](https://tug.org/FontCatalogue/inconsolata/). All of these choices are suggested by Erewhon’s documentation.

[^can-scale-erewhon]: If you want to match the text capacity of the times option, you can change the scale of the Erewhon font; in our style file, it is loaded with `\usepackage[p,scaled=.98,space]{erewhon}`.

## Package options

| Option     | Value     | Description |
|------------|-----------|-------------|
| __geom__   | twocolumn | Page dimensions of `preprint-template.tex`, designed for two column output. Default option for `\documentclass[twocolumn]`. |
|            | onecolum  | Page dimensions of the `arxiv-style`, designed for one column output. Default option for `\documentclass[onecolumn]`. |
|            | natcomm   | A text area approximating that of the Nature Communications layout. A good choice if you want minimal margins. |
|            | none      | Don’t set the page size; don’t even load the `geometry` package. |
| __headingstyle__| bold | Use bold face to demarcate headings. (default) |
|            | smallcaps | Use small caps to demarcate headings. |
|            | none      | Don’t style headings; let the class do so according to its defaults. |
| __status__ | \<str\>   | Arbitrary string which can be used to print a status marker below the header on the first page, and as part of the running header on subsequent pages. Typical values might be “Preprint” or “Under review” |
| __venue__  | \<str\>   | Similar to `status`, but only printed below the title on the first page. Typical values might be "Accepted for publication in ___" or "Published in ___". Can also be used to display a status string on the first page without including it in the running header. |
| __supplementarytitle__   | \<str\> | Arbitrary string. Printed below the article title at the start of the supplementary. Default: "Supplementary Information". |
| __supplementaryprefix__  | \<str\> | Arbitrary string which is printed before "figure" or "table" for floats in the supplementary. Default: "Supplementary". |
| __floatplacement__<br>/__suppfloatplacement__       | loose | Close to the default float placement values for the `article` class (although still a bit more permissive). Makes it difficult to place text alongside floats, so they are more likely to be placed by themselves on a float page. |
|  | medium | More permissive; floats are less likely to be placed on their own page. |
|                          | tight | Still more permissive; default for `floatplacement`, which is the setting for the main text and appendix. |
|                          | extratight | Allows to place figures alongside text, even if there is very little text. Default for `suppfloatplacement`, which is the setting for the supplementary. |
| font   | erewhon  | (default) A modern font which looks very similar to times but with simpler shapes, resulting in overall lighter text. This is a full font set, using the recommended combination of `erewhon` (roman), `cabin` (sans), `inconsolata` (typewriter) and `utopia` (math). |
|        | times    | A Times New Roman font used by both `preprint-template.tex` and `arxiv-style`. Will use `txfonts` if available, providing consistent math fonts. Note that these math fonts are horizontally much more compact than others, which can affect how well equations fit into the column. |
|        | cm       | Does not load any fonts, and so uses the Computer Modern defaults. Just sets `fontenc` and loads `amssymb` for consistency with other font options. |
|        | venturis | This is the closest available TeX font I found to Nature’s sans-serif, although my search was far from exhaustive. Note that this is an incomplete font set: roman & math fonts are left unchanged. |
|        | none     | Don’t do anything with regards to fonts, not even setting the `fontenc`. |
| marginticks | N/A | If option `marginticks` is given, will print tick marks indicating the margins. Currently only works with `geom=twocolumn` because the dimensions are hard-coded. The default is not to print margin ticks. |


### Deactivation flags

(Bold value indicates default)

| Option   | Value     | Description |
|----------|-----------|-------------|
| __orcid__| __true__  | Provide an `\orcid` macro for creating ORCiD icons in affiliations. Will load `tikz` |
|          | false     | Don’t provide the macro.
| __fancyhdr__ | __true__ | The `fancyhdr` package is used to produce the header & footer on each page. |
|              | false | `fancyhdr` is not loaded and header & footer are styled according to class defaults. |
| __titlesec__ | __true__ | The `titlesec` package is used to style the section headers, as well as the title of the Supplementary Information (which is styled as a "part"). |
|              | false | `titlesec` is not loaded. Section and part titles keep their class defaults. |

### Other features:

- `\usepackage{microtype}` is loaded by default. To turn it off, use `\documentclass[draft]`
- In the Supplementary, where the figure/text ratio tends to be higher, figures are given the `[h]` option by default to encourage them not to float too far.


## Usage

__Already have a TeX project?__:  Download `preprint+_singlefile.sty` into the directory containing your TeX source, and rename it to `preprint+.sty`.[^GHA] Then `\usepackage{preprint+}` as usual.

__Starting from scratch?__: Click the _Use this template_ at the top to create a new repository, rename _template.tex_ and start writing that file. This nets you:
  - The `preprint+.sty` style itself.
  - A useful `.gitignore` for keeping your project clean.
  - A minimal `template.tex` to get you started.
  - As a bonus, the organisation of the style into separate `preprint+*.sty` files makes it easy to adapt it to your needs.
    - If you don’t need to modify the style, you can keep things simpler by renaming `preprint+_singlefile.sty` to `preprint+.sty`, and deleting the other `preprint+*.sty` files.
    - To create your own combined style file, run `latexpand --keep-comments preprint+.sty > preprint+_singlefile.sty`.


[^GHA]: Eventually I may have the combined file automatically produced with a GitHub Action, but for now renaming is the way to go.
