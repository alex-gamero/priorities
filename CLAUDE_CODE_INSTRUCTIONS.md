# Task: Turn the Claude Design HTML export into a clean GitHub Pages slide deck

## What this repo is for
This repo hosts ONE presentation as a static site on GitHub Pages. On the day, it gets opened
in a desktop browser and shown full-screen. It will never be used on mobile. There is no backend,
no build step, no framework. Just a single self-contained HTML file served as `index.html`.

## The input file
There is an HTML file in this repo exported from Claude Design (the board priorities deck).
It is a self-contained bundle: all styles, fonts, SVG charts, and 13 slides are inside that one file.
The slide content and styling are FINISHED and CORRECT. Do not redesign slides, do not change
colors, fonts, numbers, text, charts, or animations. Your job is only the wrapper/navigation.

## The two problems to fix
1. **Remove the left-side panel.** The export shows a slide-navigator / thumbnail sidebar (and/or
   a preview chrome) on the left. It must be gone. Only the slide itself should be visible — nothing
   on the left, nothing around it.
2. **Show one slide at a time, full desktop screen.** Each slide should fill the browser window,
   centered, scaled to fit (the slides are designed at a 16:9 ratio — treat the design canvas as
   1600 x 900). No scrollbars. No browser-tab strip workaround needed; pressing the "F" key (see
   below) should give true fullscreen with nothing but the slide.

## How to do it (approach)
The export uses a custom slide component (look for a `<deck-stage>` element or a custom-element
script that draws the sidebar and handles slide switching). That component is what renders the
sidebar. The clean fix:

- Keep ALL the `<style>` blocks, all font `<link>`/`@font-face` rules, and all 13 `<section>` slides
  exactly as they are in the export. These hold the design and the animations.
- Strip out the export's own navigator/sidebar UI and its slide-switching script (the external
  custom-element script and any thumbnail/filmstrip markup).
- Replace it with your OWN tiny vanilla-JS navigation, described below.
- Rename the final result `index.html` at the repo root so GitHub Pages serves it.

## Required navigation behavior (write this yourself in plain JS, no libraries)
- All slides are stacked; show exactly ONE at a time by toggling an `is-active` class on the active
  `<section>`. IMPORTANT: the slides' entrance animations (bars growing, donut drawing, numbers
  counting up) are tied to the `.is-active` class — so when you switch slides you must remove
  `is-active` from the old one and add it to the new one, and force a reflow (e.g. read
  `offsetWidth`) before adding it so the CSS animations re-fire on every visit.
- Scale the active slide to fit the window while preserving the 16:9 ratio: wrap the slides in a
  fixed-size 1600x900 container and apply `transform: translate(-50%,-50%) scale(s)` where
  `s = min(innerWidth/1600, innerHeight/900)`, centered. Recompute on window resize.
- If there are "count up" number animations (elements with a `data-target` attribute), re-run the
  count-up each time a slide becomes active.
- Keyboard:
  - Right arrow / Down arrow / Space / PageDown -> next slide
  - Left arrow / Up arrow / PageUp -> previous slide
  - Home -> first slide, End -> last slide
  - "F" -> toggle real browser fullscreen (`requestFullscreen` / `exitFullscreen`)
  - "H" -> toggle visibility of the little progress indicator (optional)
- Mouse: click on the right ~75% of the screen = next, left ~25% = previous.
- Background behind the slide (the letterbox area on wide screens) should be the deck's dark green
  (#14291F) so it looks intentional, not white.
- A small, subtle slide counter (e.g. "4 / 13") bottom-center is fine but keep it faint and
  unobtrusive. No other chrome.

## Hard rules — do NOT do these
- Do NOT change any slide content, wording, numbers, quotes, ENDS clause text, colors, or fonts.
- Do NOT add any framework, bundler, npm install, or build step. Plain HTML/CSS/JS only.
- Do NOT use localStorage/sessionStorage.
- Do NOT add a mobile layout or responsive breakpoints — desktop 16:9 only.
- Do NOT introduce a left sidebar, thumbnail rail, or any navigator panel. That is the thing we are removing.

## Verify before finishing
- Open index.html in a desktop browser. Confirm: no left panel, one slide fills the screen,
  arrow keys move between all 13 slides, "F" goes fullscreen with only the slide showing,
  and the bar/donut/count-up animations play when each slide first appears.
- Check that the ENDS clause boxes on the priority slides are FULLY visible (text not cut off at
  the bottom edge). If any slide's content runs past the bottom of the 1600x900 canvas, reduce the
  spacing/padding on that slide's content so it fits — do NOT shrink margins so far that elements
  overlap, and do NOT change the wording.

## GitHub Pages setup (do this last)
- Ensure the final file is named `index.html` at the repository root.
- In the repo: Settings -> Pages -> Source = "Deploy from a branch", Branch = main, folder = / (root).
- The site will publish at https://<username>.github.io/<repo-name>/ — share that link.
