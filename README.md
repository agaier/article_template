# Blog / Article Template

## Overview

### If all you want to do is view the page locally

Run `python -m http.server` to serve on the base directory to view `index.html` in a local browser.

### Article

`demo.html` - Google analytics tracking, SEO, Javascript demos

`draft.md` - main text of the article, in markdown.

`draft_appendix.md` - appendix, in markdown.

`draft_bib.html` - the citations, in bibtex.

`draft_header.html` - start of the document

`index.html` - generated, don't edit this file.

### Instructions to Build and Test
```bash
git clone https://github.com/weightagnostic/weightagnostic.github.io.git
cd weightagnostic.github.io
npm install
```
Note: you might not be able to use npm when behind the proxy (you can suspend Pulse Secure, or whatever to install, then resume)

Modify text by editing `draft.md` -- this is where all of the content exists.

Appendix content goes in `draft_appendix.md`. Add bib entries to `draft_bib.html`.

Run `./bin/make` to build document into `index.html` (which are identical).
Run `python -m http.server` to serve on the base directory to view `index.html` in a local browser for debugging.

To watch all markdown files for changes and then compile them, you can run the following
```
brew install fswatch
./bin/watch
```
Note that to update the html files (the header, etc.) you will have to run `./bin/make` again.

--- 
## Checklist when using this Template

1) Update title and authors (`draft_header.html`: l186)
2) Page Title, cover image, and caption (`draft_header.html`: l171, l175)
3) Replace text (`draft.md`)
  a) upload all images and videos to asset folders 
  b) place all references in `draft_bib.html`
  c) javascript demo functions defined in `lib/controller.js`
4) Replace Appendix (`draft_appendix.md`)
  a) Acknowledgements 
  b) Feedback link (can use the current repo's issues)
  c) Citation for article
  d) Code link
  e) License of text/images
  f) Supplementary Materials
5) Web
  a) Title of page (`draft_header.html`: l171)
  a) Twitter image and card data (`draft_header.html`: l146)
  b) SEO description (`draft_header.html`: l146)
  c) (optional) Google analytics (`draft_header.html`: l134)
6) Final touches
  a) Update date (`draft_header.html`: l204)
  b) Pdf link if applicable, (`draft_header.html`: l209)

---
## Syntax Reference

math:
  * normal latex syntax can be used (e.g. $\mathcal{U}(-2,2)$), though I haven't seen how far this can be pushed

citatation: 
  * cite in draft.md with: `<dt-cite key="some_paper"></dt-cite>`
  * place bibtex in draft_bib.html

images: 
[markdown]
`
  <div style="text-align: center;">
    <br/>
    <img class="b-lazy" src="assets/img/MY_IMG.png" style="width: 75%;"/>
    <br/>
    <figcaption style="text-align: left;">
      <b>Title of figure</b>
      <br/>
      This is the caption of the figure.
    </figcaption>
  </div>
`
[end]

videos:
[markdown]
`
  <div style="text-align: center;">
    <video class="b-lazy" src="assets/mp4/MY_VID.mp4" type="video/mp4" autoplay muted playsinline loop style="width:50%;"></video>
    <br/>
    <figcaption style="text-align: left;">
      <b>Title of figure</b>
      <br/>
      This is the caption of the figure.
    </figcaption>
  </div>
`
[end]

javascript:
Live demos are in javascript, inserted in the draft.md file as divs.

[markdown]
`
  <div style="text-align: center;">
  <br/>
  <div id="MY_DEMO" class="unselectable" style="text-align: center;"></div>
  <br/>
  <figcaption style="color:#FF6C00;">Interactive Demo</figcaption>
  <figcaption style="text-align: left;">
    This is the caption for the interactive demo.
  </figcaption>
  </div>
`
[end]

With the javascript functions defined `lib/controller.js`, e.g.

[javascript]
`
var demo_0008_settings = {
  divName: 'MY_DEMO',
  smallMode: true,
  timeLimit: 1000,
  useFineTune: false,
  drawBar: true,
  showInstructions: false,
  hardMode: false,
  aVec: [1, 1, 1, 1, 1, 1, 6, 7, 7, 1],
  wKey: [ 9, 18, 19, 27, 36, 46, 49, 58, 69, 79, 89],
  weights: null,
  wBias: -0.25,
  displayTitle: true,
};
var MY_DEMO = new p5(cartpole_demo(MY_DEMO_settings), 'MY_DEMO');
`
[end]
