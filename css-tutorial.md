# CSS Tutorial: `::before`, `::after` & `@keyframes`

---

## 1. `::before` and `::after` – Pseudo-elements

### What are they?

`::before` and `::after` are **pseudo-elements**. They let you insert a virtual element
*inside* an existing HTML element – without writing any extra HTML.

- `::before` inserts a child **before** all real content inside the element.
- `::after` inserts a child **after** all real content inside the element.

Think of them as "invisible HTML elements" that CSS conjures up for you.

### The one rule you must always follow

Every pseudo-element **must** have a `content` property, or it won't appear at all.

```css
.my-element::before {
  content: '';   /* empty string = no text, but the element exists */
}
```

Even when you only want a visual shape (not text), you still need `content: ''`.

---

### Example 1 – Adding a decorative symbol in CSS

```html
<p class="tip">Remember to save your file</p>
```

```css
.tip::before {
  content: '💡 ';   /* the text (or icon) to insert */
}
```

**Result:**  💡 Remember to save your file

The `💡` is never in the HTML – CSS creates it. This is useful for icons,
quotes, decorative characters, or labels.

---

### Example 2 – Drawing shapes with pseudo-elements

You can style `::before` and `::after` like any other element: give them a
size, colour, and `position: absolute` to place them precisely.

```css
/* The parent must be position: relative so the pseudo-element
   can anchor to it with position: absolute */
.box {
  position: relative;
  width: 100px;
  height: 100px;
  background: navy;
}

/* A horizontal bar across the top of .box */
.box::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;      /* full width of .box */
  height: 3px;      /* just 3 px tall */
  background: cyan;
}

/* A vertical bar down the left side of .box */
.box::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 3px;       /* just 3 px wide */
  height: 100%;     /* full height of .box */
  background: cyan;
}
```

**This is exactly how the corner brackets work in 404.html.**
Each `.corner` div has no visible content of its own. Its `::before` is the
horizontal bar and its `::after` is the vertical bar. By flipping the div
with `transform: scaleX(-1)` etc., the same two bars are reused in four
orientations.

---

### Example 3 – Ghost text layers (the 404 glitch effect)

```css
.error-code {
  position: relative;
  color: hotpink;
}

/* A second copy of the text, coloured cyan, clipped to a horizontal band */
.error-code::before {
  content: '404';          /* must match the element's text */
  position: absolute;
  inset: 0;                /* same position as the parent */
  color: cyan;
  clip-path: polygon(0 20%, 100% 20%, 100% 40%, 0 40%);
  opacity: 0;              /* hidden until the animation shows it */
}
```

`clip-path: polygon(...)` acts like a cookie-cutter: only the part of the
element inside the polygon is visible. Here it reveals a horizontal strip
between 20 % and 40 % of the element's height – making it look like only a
slice of the text is visible, displaced sideways.

---

### `::before` vs `:before` – single vs double colon

Both work. The double-colon `::` is the modern CSS3 standard and makes it
clear you're selecting a pseudo-*element* (not a pseudo-*class* like `:hover`).
Use `::` for new code.

---

## 2. `@keyframes` – Defining Animations

### What does it do?

`@keyframes` defines a **named animation sequence** – a list of CSS snapshots
at specific points in time.

You give the animation a name, then reference that name in the `animation`
property on any element.

### Basic syntax

```css
@keyframes my-animation {
  0%   { /* CSS at the very start */ }
  50%  { /* CSS halfway through  */ }
  100% { /* CSS at the very end  */ }
}
```

- Percentages represent **how far through the animation duration** you are.
- The browser automatically interpolates (smoothly transitions) between
  keyframe snapshots.
- You can use `from` and `to` instead of `0%` and `100%` for simple two-step
  animations.

---

### Attaching an animation to an element

```css
.my-element {
  animation: my-animation 3s infinite;
  /*         ^^^^^^^^^^^^ name         */
  /*                      ^^ duration  */
  /*                         ^^^^^^^^ repeat forever */
}
```

Common `animation` sub-properties:

| Property | What it controls |
|---|---|
| `animation-name` | Which `@keyframes` to use |
| `animation-duration` | How long one cycle takes (`2s`, `500ms`) |
| `animation-iteration-count` | How many times to repeat (`1`, `3`, `infinite`) |
| `animation-timing-function` | Easing curve (`ease`, `linear`, `ease-in-out`) |
| `animation-delay` | Wait before starting (`1s`) |
| `animation-direction` | `normal`, `reverse`, `alternate` |
| `animation-fill-mode` | What state to hold before/after (`forwards`, `backwards`) |

---

### Example 1 – Fade in

```css
@keyframes fade-in {
  from { opacity: 0; }
  to   { opacity: 1; }
}

.card {
  animation: fade-in 0.6s ease-out forwards;
  /* forwards = stay at opacity:1 after the animation ends */
}
```

---

### Example 2 – Infinite pulse glow

```css
@keyframes pulse {
  0%,  100% { box-shadow: 0 0 5px cyan; }
  50%        { box-shadow: 0 0 25px cyan, 0 0 50px cyan; }
}

.glowing-button {
  animation: pulse 2s ease-in-out infinite;
}
```

The element starts with a small glow, grows to a large glow at 50 %,
then shrinks back. Because it's `infinite`, it loops forever.

---

### Example 3 – The flicker in 404.html explained

```css
@keyframes flicker {
  0%,  89%, 91%, 93%, 100% { opacity: 1; }
  90% { opacity: 0.75; }
  92% { opacity: 0.9; }
}
```

Reading this step by step:

- **0 % → 89 %** (the first 89 % of the 7 s cycle): fully visible, nothing happens.
- **89 % → 90 %**: opacity quickly drops to 0.75 – a brief dim.
- **90 % → 91 %**: partial recovery to 0.9.
- **91 % → 93 %**: back to full opacity.
- **93 % → 100 %**: fully on until the cycle restarts.

This gives the impression of a neon tube flickering for a split second every
~6 seconds.

---

### Example 4 – The glitch slide in 404.html explained

```css
@keyframes glitch-top {
  0%,  88%, 100% { opacity: 0; transform: none; }
  89% { opacity: 0.9; transform: translateX(-4px); }
  90% { opacity: 0.7; transform: translateX(4px); }
  91% { opacity: 0; transform: none; }
}
```

- The ghost slice is invisible for 88 % of the 4 s cycle.
- At 89 % it flashes in, shifted 4 px to the left.
- At 90 % it shifts 4 px to the right.
- At 91 % it vanishes again.

The whole glitch lasts `4s × (91% - 89%) = 0.08 s` – 80 milliseconds.
So fast you barely see it, which is exactly what makes it feel like a glitch.

---

## 3. Putting it all together

| CSS feature | What it does in 404.html |
|---|---|
| `::before` on `.corner` | Draws the horizontal bar of the L-bracket |
| `::after` on `.corner` | Draws the vertical bar of the L-bracket |
| `::before` on `.error-code` | Cyan ghost slice of "404" for glitch top |
| `::after` on `.error-code` | Pink ghost slice of "404" for glitch bottom |
| `@keyframes flicker` | Makes the whole 404 text briefly dim |
| `@keyframes glitch-top` | Slides the cyan top slice left/right for 80 ms |
| `@keyframes glitch-bot` | Slides the pink bottom slice right/left for 80 ms |
| `<canvas>` + JavaScript | Draws the falling matrix characters (not CSS) |

---

## Quick Reference

```css
/* Pseudo-elements */
.el::before { content: 'text or empty'; /* required! */ }
.el::after  { content: ''; }

/* Keyframes */
@keyframes name {
  0%   { property: value; }
  100% { property: value; }
}

/* Applying an animation */
.el {
  animation: name duration timing-function iteration-count;
  /* e.g. */
  animation: pulse 2s ease-in-out infinite;
}
```
