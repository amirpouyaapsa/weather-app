# Weather App · Karyar Project · [README_FA](README_FA.md)

## Live Demo

**[View the Demo Website](https://amirpouyaapsa.github.io/weather-app/)**

Click the link above to explore the live version of the project.

# Week Zero: The `defer` Attribute in JavaScript

## 1. The Problem

The browser reads an HTML file from top to bottom. A page is built from two parts:

- **HTML:** the skeleton of the page (buttons, headings, forms)
- **JavaScript:** the behavior of the page (clicks, showing messages)

If the `script` tag is inside `head`:

```html
<head>
    <script src="script.js"></script>
</head>
```

The browser downloads and runs the file right there, and stops reading the HTML until it finishes.

Two problems come up:

1. A large file delays the display of the page.
2. If the script needs an element that has not been created yet, it cannot find it and throws an error.

## 2. The Solution: `defer`

```html
<head>
    <script src="script.js" defer></script>
</head>
```

Browser behavior with `defer`:

1. It downloads the JavaScript file **at the same time** as it reads the HTML.
2. It does **not** stop reading the HTML for the download.
3. It postpones running the script until **after the HTML is fully parsed**.
4. It runs multiple `defer` scripts **in the order they are written**.

> **Note:** `defer` scripts run before the `DOMContentLoaded` event.

## 3. Example

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Learning defer</title>
    <script src="script.js" defer></script>
</head>
<body>

    <button id="myButton">Click me</button>

</body>
</html>
```

**script.js**

```javascript
const button = document.getElementById("myButton");

button.addEventListener("click", function () {
    alert("Hello!");
});
```

Execution steps:

1. The browser downloads `script.js` but holds off running it.
2. The HTML is read to the end and the button is created.
3. The script runs, finds the button, and attaches the click event to it.
4. When the button is clicked, the message `Hello!` is displayed.

**Without `defer`** (script inside `head`): the code runs before the button is created, the value of `button` is `null`, and `addEventListener` throws an error.

## 4. `defer` in the Head or the Script at the End of `body`?

The second approach is to place the script right before `</body>`:

```html
<body>

    <button id="myButton">Click me</button>

    <script src="script.js"></script>
</body>
```

This also works, because the button was created before the script.

| Feature | `defer` in `head` | Script at the end of `body` |
|---|---|---|
| Download start | At the same time as reading the HTML | When the browser reaches the script tag |
| HTML stops during download | No | Yes |
| Execution time | After the HTML is fully parsed | When the browser reaches the script tag |
| Access to elements | All elements of the page | Elements placed above the script |
| Execution order of multiple scripts | Preserved | In the order written on the page |

**Speed difference:** With `defer`, the download starts from the beginning and happens at the same time as reading the HTML. With the end-of-`body` approach, the download starts later. The size of this difference depends on file size and network speed.

## 5. Important Notes

- `defer` only affects **external** scripts (with `src`) and has no effect on inline scripts.
- If the script needs page elements, `defer` in `head` is a suitable choice.
- A script at the end of `body` is also a valid approach, but its download starts later.
- If the execution order of multiple files matters, `defer` preserves the order. `async` does **not** guarantee this.