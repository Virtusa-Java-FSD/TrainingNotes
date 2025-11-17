CSS (Cascading Style Sheets) provides a wide range of properties that allow you to style and format HTML elements on web pages. Here's a list of some basic CSS properties commonly used to style elements, particularly for lists (`<ul>`, `<ol>`, and `<li>` elements):

### List Properties

1. **`list-style-type`**
   - Specifies the type of marker for list items.
   - Values: `disc`, `circle`, `square`, `decimal`, `decimal-leading-zero`, `lower-roman`, `upper-roman`, `lower-alpha`, `upper-alpha`, `none`, etc.
   ```css
   ul {
     list-style-type: disc;
   }
   ```

2. **`list-style-image`**
   - Specifies an image as the marker for list items.
   - Value: `url('image.jpg')`, `none`, etc.
   ```css
   ul {
     list-style-image: url('bullet.png');
   }
   ```

3. **`list-style-position`**
   - Specifies the position of the list item markers (inside or outside the list item box).
   - Values: `inside`, `outside`.
   ```css
   ul {
     list-style-position: outside;
   }
   ```

### Basic Text and Font Properties

4. **`color`**
   - Specifies the text color.
   - Values: `#RRGGBB`, `rgb()`, color names (e.g., `black`, `red`).
   ```css
   ul {
     color: #333333;
   }
   ```

5. **`font-family`**
   - Specifies the font family for text.
   - Values: `serif`, `sans-serif`, `monospace`, custom font names, etc.
   ```css
   ul {
     font-family: Arial, sans-serif;
   }
   ```

6. **`font-size`**
   - Specifies the font size.
   - Values: `12px`, `1em`, `1.5rem`, etc.
   ```css
   ul {
     font-size: 16px;
   }
   ```

7. **`font-weight`**
   - Specifies the font weight (normal, bold, etc.).
   - Values: `normal`, `bold`, `bolder`, `lighter`, `100` to `900`.
   ```css
   ul {
     font-weight: bold;
   }
   ```

### Basic Box Model Properties

8. **`padding`**
   - Specifies the padding (space) inside the element.
   - Values: `10px`, `1em`, `1%`, etc. (can be specified for each side: `padding-top`, `padding-right`, `padding-bottom`, `padding-left`).
   ```css
   ul {
     padding: 10px;
   }
   ```

9. **`margin`**
   - Specifies the margin (space) outside the element.
   - Values: `10px`, `1em`, `1%`, etc. (can be specified for each side: `margin-top`, `margin-right`, `margin-bottom`, `margin-left`).
   ```css
   ul {
     margin: 20px;
   }
   ```

10. **`border`**
    - Specifies the border around the element.
    - Values: `1px solid black`, etc. (can be specified for each side: `border-top`, `border-right`, `border-bottom`, `border-left`).
    ```css
    ul {
      border: 1px solid #cccccc;
    }
    ```

### Display and Positioning Properties

11. **`display`**
    - Specifies how an element is displayed.
    - Values: `block`, `inline`, `inline-block`, `flex`, `grid`, etc.
    ```css
    ul {
      display: block;
    }
    ```

12. **`position`**
    - Specifies the positioning method used for an element.
    - Values: `static`, `relative`, `absolute`, `fixed`.
    ```css
    ul {
      position: relative;
    }
    ```

### Miscellaneous Properties

13. **`background-color`**
    - Specifies the background color of an element.
    - Values: `#RRGGBB`, `rgb()`, color names.
    ```css
    ul {
      background-color: #f0f0f0;
    }
    ```

14. **`text-align`**
    - Specifies the horizontal alignment of text.
    - Values: `left`, `right`, `center`, `justify`.
    ```css
    ul {
      text-align: center;
    }
    ```

15. **`text-decoration`**
    - Specifies the decoration added to text.
    - Values: `none`, `underline`, `overline`, `line-through`, `blink`.
    ```css
    ul {
      text-decoration: underline;
    }
    ```

These are just a few basic CSS properties commonly used for styling lists and text on web pages. CSS provides a wide range of properties for layout, typography, colors, and more, enabling you to create visually appealing and functional web pages.