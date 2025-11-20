Media queries are a key component of responsive web design. They allow you to apply CSS rules based on the characteristics of the device or viewport, such as width, height, orientation, resolution, and more. This enables you to create flexible and adaptive layouts that work well on a variety of devices, from mobile phones to large desktop monitors.

### Basic Syntax

A media query consists of a media type and one or more expressions that check the conditions of the media feature, such as width or height.

```css
@media (condition) {
  /* CSS rules here */
}
```

### Common Media Features

Here are some common media features used in media queries:

- `width`: The width of the viewport.
- `height`: The height of the viewport.
- `min-width`: The minimum width of the viewport.
- `max-width`: The maximum width of the viewport.
- `min-height`: The minimum height of the viewport.
- `max-height`: The maximum height of the viewport.
- `orientation`: The orientation of the device (portrait or landscape).
- `aspect-ratio`: The ratio of the width to the height of the viewport.

### Examples

#### 1. Basic Media Query

Applying different styles for devices with a viewport width of 600px or less:

```css
@media (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
```

#### 2. Min-Width and Max-Width

Applying different styles for devices with a viewport width between 600px and 1200px:

```css
@media (min-width: 600px) and (max-width: 1200px) {
  body {
    background-color: lightgreen;
  }
}
```

#### 3. Orientation

Applying different styles based on the device orientation:

```css
/* Portrait orientation */
@media (orientation: portrait) {
  body {
    background-color: lightcoral;
  }
}

/* Landscape orientation */
@media (orientation: landscape) {
  body {
    background-color: lightgoldenrodyellow;
  }
}
```

#### 4. Combining Conditions

Combining multiple conditions in a media query:

```css
@media (min-width: 600px) and (orientation: landscape) {
  body {
    background-color: lightseagreen;
  }
}
```

### Media Queries in Responsive Design

Media queries are often used to create responsive designs that adjust the layout based on the screen size. Here's an example of a responsive layout using media queries:

```css
/* Default styles for mobile devices */
.container {
  width: 100%;
  padding: 10px;
}

.column {
  width: 100%;
  margin-bottom: 20px;
}

/* Styles for tablets (min-width: 600px) */
@media (min-width: 600px) {
  .container {
    width: 80%;
    margin: 0 auto;
  }

  .column {
    width: 48%;
    display: inline-block;
    vertical-align: top;
  }

  .column:not(:last-child) {
    margin-right: 4%;
  }
}

/* Styles for desktops (min-width: 992px) */
@media (min-width: 992px) {
  .column {
    width: 30%;
  }

  .column:not(:last-child) {
    margin-right: 5%;
  }
}
```

### Media Queries in Bootstrap

Bootstrap utilizes media queries to create a responsive grid system. Here are the breakpoints Bootstrap uses by default:

- `xs` (extra small): <576px
- `sm` (small): ≥576px
- `md` (medium): ≥768px
- `lg` (large): ≥992px
- `xl` (extra large): ≥1200px
- `xxl` (extra extra large): ≥1400px

Example of using Bootstrap's media queries:

```css
/* Extra small devices (phones, less than 576px) */
@media (max-width: 575.98px) { ... }

/* Small devices (tablets, 576px and up) */
@media (min-width: 576px) and (max-width: 767.98px) { ... }

/* Medium devices (desktops, 768px and up) */
@media (min-width: 768px) and (max-width: 991.98px) { ... }

/* Large devices (large desktops, 992px and up) */
@media (min-width: 992px) and (max-width: 1199.98px) { ... }

/* Extra large devices (larger desktops, 1200px and up) */
@media (min-width: 1200px) and (max-width: 1399.98px) { ... }

/* Extra extra large devices (larger desktops, 1400px and up) */
@media (min-width: 1400px) { ... }
```

### Conclusion

Media queries are essential for building responsive websites that adapt to different devices and screen sizes. They allow you to apply specific styles based on various conditions, ensuring a better user experience across a wide range of devices.