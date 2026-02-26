# 📬 Contact Form — Beginner's Guide

A step-by-step walkthrough of how `contact-form.html` was built, what each part does, and how to customize it for your own project.

---

## Prerequisites

You only need:
- A text editor (VS Code is recommended — it's free)
- A web browser (Chrome, Firefox, Edge, etc.)

No installation, no server, no frameworks to install. Open the file and it works.

---

## Step 1 — Understanding the file structure

Every HTML page has the same skeleton:

```html
<!DOCTYPE html>        <!-- Tells the browser this is HTML5 -->
<html lang="en">       <!-- Root element; lang="en" means English -->
  <head>               <!-- Invisible settings & resources -->
    ...
  </head>
  <body>               <!-- Everything visible on screen -->
    ...
  </body>
</html>
```

Think of `<head>` as the backstage area and `<body>` as the stage.

---

## Step 2 — Loading external libraries (inside `<head>`)

We use two libraries loaded from the internet — no download needed.

### Google Fonts
```html
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display..." rel="stylesheet" />
```
This loads two beautiful fonts:
- **Playfair Display** — the elegant heading font
- **DM Sans** — the clean body font

> 💡 Want different fonts? Go to [fonts.google.com](https://fonts.google.com), pick any font, click "Get font", then copy the `<link>` tag it gives you.

### DaisyUI + Tailwind CSS
```html
<link href="https://cdn.jsdelivr.net/npm/daisyui@4.10.1/dist/full.min.css" rel="stylesheet" />
<script src="https://cdn.tailwindcss.com"></script>
```

- **Tailwind CSS** — a utility-first CSS framework. Instead of writing `color: red`, you add a class like `text-red-500` directly to your HTML element.
- **DaisyUI** — a component library built on top of Tailwind. It gives you pre-styled components like `input`, `btn`, `checkbox` with just a class name.

---

## Step 3 — The two-panel card layout

The card uses CSS Grid to split into two columns: an info panel (left) and the form (right).

```html
<div class="contact-card">   <!-- Grid container -->
  <div class="card-left">   <!-- Info panel -->
    ...
  </div>
  <div class="card-right">  <!-- Form panel -->
    ...
  </div>
</div>
```

In the `<style>` section, this is what creates the two columns:

```css
.contact-card {
  display: grid;
  grid-template-columns: 1fr 1.5fr;  /* left gets 1 part, right gets 1.5 parts */
}
```

> 💡 On small screens (mobile), the left panel is hidden (`display: none`) and the form takes the full width. This is called **responsive design**.

---

## Step 4 — Form fields (DaisyUI components)

Each input follows the same pattern:

```html
<div class="form-control">               <!-- Wrapper div -->
  <label class="label pb-1">            <!-- Label row -->
    <span class="label-text-custom">Name</span>
  </label>
  <input type="text" class="input input-md w-full" placeholder="Jane Smith" required />
  <span class="hidden" id="err-name">Please enter your name.</span>  <!-- Error message -->
</div>
```

Key DaisyUI classes:
| Class | What it does |
|---|---|
| `form-control` | Wrapper that stacks label + input vertically |
| `input` | Styles a text field |
| `textarea` | Styles a multi-line text area |
| `select` | Styles a dropdown |
| `btn` | Styles a button |
| `checkbox` | Styles a checkbox |
| `label` | Wrapper for label text |

The `required` attribute on inputs tells the browser the field must be filled, but we also do our own validation in JavaScript for full control over error messages.

---

## Step 5 — The dropdown (select)

```html
<select id="field-subject" class="select select-md w-full" required>
  <option value="">Choose a topic…</option>
  <option>General Inquiry</option>
  <option>Project Collaboration</option>
  <option>Support</option>
</select>
```

Each `<option>` is one item in the dropdown list. The first option has `value=""` — this is the placeholder. Our validation checks that the user picked something other than this empty option.

> 💡 To add more topics, just add more `<option>` lines before the closing `</select>`.

---

## Step 6 — The character counter (JavaScript)

```javascript
const msgField = document.getElementById('field-message');
const charCount = document.getElementById('char-count');

msgField.addEventListener('input', () => {
  const len = msgField.value.length;   // How many characters typed
  charCount.textContent = len;          // Update the number on screen
  charCount.style.color = len > 450 ? '#e07676' : '#6b5d3f'; // Red if near limit
});
```

`addEventListener('input', ...)` means: "every time the user types something, run this function."

---

## Step 7 — Form validation (JavaScript)

Before submitting, we check each field is correctly filled:

```javascript
form.addEventListener('submit', (e) => {
  e.preventDefault(); // Stop the page from reloading

  const name = validateField('field-name', 'err-name', f => f.value.trim().length > 0);
  // ...more fields...

  if (name && email && subject && msg && consent) {
    // All valid — simulate sending
  }
});
```

`e.preventDefault()` stops the default browser behavior (which would reload the page). Instead, we take over and do our own checks.

The `validateField` helper function:
1. Finds the field by ID
2. Runs a test function (the arrow `f => ...` part)
3. Shows/hides the error message
4. Highlights the field border in red if invalid

---

## Step 8 — The loading state & success toast

When the form is submitted successfully, we:
1. Disable the button and show a spinner (so the user knows something is happening)
2. Wait 1.5 seconds (simulating a server request)
3. Reset the form
4. Show a toast notification at the top-right

```javascript
btn.innerHTML = '<span class="loading loading-spinner loading-sm"></span> Sending…';

setTimeout(() => {
  // After 1500ms (1.5 seconds):
  form.reset();
  // Show toast
  const toast = document.getElementById('toast');
  toast.classList.remove('hidden');    // Make it visible
  setTimeout(() => toast.classList.add('hidden'), 4000); // Hide after 4s
}, 1500);
```

> ⚠️ **Important:** This form only *simulates* sending. To actually send emails, you need a backend service. Free options include [Formspree](https://formspree.io), [Web3Forms](https://web3forms.com), or [EmailJS](https://www.emailjs.com).

---

## How to Customize

### Change colors
The gold accent color is defined as a CSS variable at the top of `<style>`:
```css
:root {
  --accent-gold: #c9a84c;
}
```
Replace `#c9a84c` with any color. Use [coolors.co](https://coolors.co) to find nice hex codes.

### Change the background color
```css
body {
  background: #0c0b09;  /* Change this to any dark color */
}
```

### Change fonts
1. Go to [fonts.google.com](https://fonts.google.com)
2. Pick a font → click "Get font" → "Get embed code"
3. Replace the `<link>` in `<head>` with the new one
4. Update `font-family` in the CSS:
   ```css
   body { font-family: 'Your New Font', sans-serif; }
   .display-heading { font-family: 'Your Heading Font', serif; }
   ```

### Add or remove form fields
Copy the `form-control` block pattern:
```html
<div class="form-control mb-4">
  <label class="label pb-1"><span class="label-text-custom">Your Label</span></label>
  <input type="text" id="field-yourfield" placeholder="..." class="input input-md w-full" />
</div>
```
Then add its validation in the JavaScript section.

### Connect to a real form service (Formspree example)
1. Sign up at [formspree.io](https://formspree.io)
2. Create a form and get your endpoint URL (e.g. `https://formspree.io/f/abcdefgh`)
3. In the JavaScript, replace the `setTimeout` simulation with:
```javascript
const response = await fetch('https://formspree.io/f/abcdefgh', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    name: document.getElementById('field-name').value,
    email: document.getElementById('field-email').value,
    message: document.getElementById('field-message').value,
  })
});
```

---

## Quick Reference: Common DaisyUI Classes

| Component | Classes |
|---|---|
| Text input | `input input-md` / `input input-lg` |
| Textarea | `textarea textarea-md` |
| Dropdown | `select select-md` |
| Button | `btn btn-primary` / `btn btn-ghost` |
| Checkbox | `checkbox` |
| Loading spinner | `loading loading-spinner loading-sm` |
| Alert/toast | `alert` |

Full DaisyUI docs: **[daisyui.com/components](https://daisyui.com/components/)**

---

## File at a glance

```
contact-form.html
│
├── <head>
│   ├── Google Fonts link
│   ├── DaisyUI CSS link
│   ├── Tailwind JS script
│   └── <style> — custom CSS (colors, layout, animations)
│
└── <body>
    ├── Ambient glow divs (decorative)
    ├── .contact-card (CSS Grid)
    │   ├── .card-left  — contact info panel
    │   └── .card-right — <form> with all fields
    ├── #toast          — success notification
    └── <script>        — character counter + validation + submit logic
```

---

Happy building! 🚀
