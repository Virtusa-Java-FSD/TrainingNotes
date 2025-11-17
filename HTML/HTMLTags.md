
### Document Metadata Tags
- **`<!DOCTYPE html>`**: Defines the document type and HTML version.
- **`<html>`**: The root element of an HTML document.
- **`<head>`**: Contains metadata, title, links, scripts, and styles.
- **`<title>`**: Sets the title of the document shown in the browser title bar.
- **`<meta>`**: Defines metadata about an HTML document (e.g., charset, author, viewport).
- **`<link>`**: Defines the relationship between the current document and an external resource (e.g., stylesheet).

### Content Sectioning Tags
- **`<body>`**: Contains the contents of an HTML document, such as text, images, links, and other elements.
- **`<header>`**: Defines a header for a document or section.
- **`<nav>`**: Defines a set of navigation links.
- **`<main>`**: Specifies the main content of a document.
- **`<section>`**: Defines a section in a document.
- **`<article>`**: Defines an article.
- **`<aside>`**: Defines content aside from the content it is placed in (e.g., sidebar).
- **`<footer>`**: Defines a footer for a document or section.
- **`<h1>` to `<h6>`**: Define HTML headings, `<h1>` being the highest (most important) and `<h6>` the lowest (least important).

### Text Content Tags
- **`<p>`**: Defines a paragraph.
- **`<a>`**: Defines a hyperlink.
- **`<span>`**: Defines a section in a document.
- **`<em>`**: Defines emphasized text (typically italicized).
- **`<strong>`**: Defines important text (typically bold).
- **`<small>`**: Defines smaller text.
- **`<br>`**: Inserts a single line break.
- **`<i>`**: Defines a part of text in an alternate voice or mood (typically italicized).
- **`<b>`**: Defines bold text.
- **`<u>`**: Defines underlined text.
- **`<mark>`**: Defines marked/highlighted text.
- **`<del>`**: Defines deleted (strikethrough) text.
- **`<ins>`**: Defines inserted text.

### List Tags
- **`<ul>`**: Defines an unordered (bulleted) list.
- **`<ol>`**: Defines an ordered (numbered) list.
- **`<li>`**: Defines a list item.
- **`<dl>`**: Defines a description list.
- **`<dt>`**: Defines a term/name in a description list.
- **`<dd>`**: Defines a description of a term/name in a description list.

### Table Tags
- **`<table>`**: Defines a table.
- **`<caption>`**: Defines a table caption.
- **`<tr>`**: Defines a table row.
- **`<th>`**: Defines a table header cell.
- **`<td>`**: Defines a table data cell.
- **`<thead>`**: Groups the header content in a table.
- **`<tbody>`**: Groups the body content in a table.
- **`<tfoot>`**: Groups the footer content in a table.

### Form Tags
- **`<form>`**: Defines an HTML form for user input.
- **`<input>`**: Defines an input control.
- **`<textarea>`**: Defines a multi-line text input control.
- **`<button>`**: Defines a clickable button.
- **`<label>`**: Defines a label for an `<input>` element.
- **`<select>`**: Defines a drop-down list.
- **`<option>`**: Defines an option in a drop-down list.
- **`<fieldset>`**: Groups related elements in a form.
- **`<legend>`**: Defines a caption for a `<fieldset>`.
- **`<datalist>`**: Specifies a list of pre-defined options for an `<input>` element.
- **`<output>`**: Defines the result of a calculation.

### Image and Multimedia Tags
- **`<img>`**: Embeds an image.
- **`<audio>`**: Embeds sound content.
- **`<video>`**: Embeds video content.
- **`<source>`**: Defines multiple media resources for `<audio>` and `<video>`.
- **`<track>`**: Defines text tracks for `<video>` and `<audio>`.
- **`<embed>`**: Embeds external content.
- **`<object>`**: Embeds an external resource.
- **`<param>`**: Defines parameters for an `<object>`.

### Embedded Content Tags
- **`<iframe>`**: Embeds another HTML page within the current page.

### Scripting Tags
- **`<script>`**: Defines client-side JavaScript.
- **`<noscript>`**: Defines alternative content for users that do not support client-side scripts.

### Styling Tags
- **`<style>`**: Defines CSS styles.

### Semantic Tags
- **`<article>`**: Defines an article.
- **`<section>`**: Defines a section.
- **`<nav>`**: Defines navigation links.
- **`<aside>`**: Defines content aside from the content it is placed in.
- **`<header>`**: Defines a header for a document or section.
- **`<footer>`**: Defines a footer for a document or section.
- **`<main>`**: Specifies the main content of a document.
- **`<figure>`**: Specifies self-contained content, like illustrations, diagrams, photos, code listings, etc.
- **`<figcaption>`**: Defines a caption for a `<figure>` element.
- **`<details>`**: Defines additional details that the user can view or hide.
- **`<summary>`**: Defines a visible heading for a `<details>` element.

### Example of HTML Document with Various Tags

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Common HTML Tags</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Welcome to My Website</h1>
    <nav>
      <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#about">About</a></li>
        <li><a href="#contact">Contact</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section id="home">
      <h2>Home Section</h2>
      <p>This is the home section. Here is a <a href="https://www.example.com">link</a>.</p>
      <img src="image.jpg" alt="Example Image">
    </section>
    <section id="about">
      <h2>About Section</h2>
      <p>This is the about section.</p>
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </section>
    <section id="contact">
      <h2>Contact Section</h2>
      <form action="/submit-form" method="post">
        <label for="name">Name:</label>
        <input type="text" id="name" name="name">
        <label for="email">Email:</label>
        <input type="email" id="email" name="email">
        <button type="submit">Submit</button>
      </form>
    </section>
  </main>
  <footer>
    <p>&copy; 2024 My Website</p>
  </footer>
  <script src="script.js"></script>
</body>
</html>
```

This example document uses a variety of HTML tags to demonstrate a typical webpage structure, including headings, paragraphs, links, images, lists, forms, and sections for navigation and content organization.