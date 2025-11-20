### CSS Grid Layout

CSS Grid Layout is a two-dimensional layout system for the web. It allows you to create complex layouts using rows and columns. Grid provides more control over both the layout's columns and rows, making it ideal for more complex designs.

#### Basic Concepts

- **Grid Container**: The element on which `display: grid` is applied. It's the direct parent of all the grid items.
- **Grid Item**: The direct children of the grid container.
- **Grid Line**: The lines that divide the grid into cells. They can be horizontal or vertical.
- **Grid Track**: The space between two grid lines. It can be a row or a column.
- **Grid Cell**: The smallest unit of the grid. It is the space between four grid lines.
- **Grid Area**: A rectangular space made up of one or more grid cells.

#### Basic Example

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; /* Three equal columns */
  grid-template-rows: auto; /* Rows adjust to content height */
  gap: 10px; /* Gap between grid items */
}

.item1 {
  grid-column: 1 / 3; /* Span from column line 1 to 3 */
  grid-row: 1; /* First row */
}

.item2 {
  grid-column: 3; /* Third column */
  grid-row: 1; /* First row */
}

.item3 {
  grid-column: 1 / 4; /* Span all columns */
  grid-row: 2; /* Second row */
}
```

```html
<div class="container">
  <div class="item1">Item 1</div>
  <div class="item2">Item 2</div>
  <div class="item3">Item 3</div>
</div>
```

#### Grid Properties

- `display: grid;` or `display: inline-grid;`
- `grid-template-columns`: Defines the columns of the grid.
- `grid-template-rows`: Defines the rows of the grid.
- `grid-template-areas`: Defines named grid areas.
- `grid-column-gap` or `column-gap`: Sets the gap between columns.
- `grid-row-gap` or `row-gap`: Sets the gap between rows.
- `gap`: Sets the gaps between rows and columns simultaneously.
- `grid-column`: Specifies a grid item's start and end position within the columns.
- `grid-row`: Specifies a grid item's start and end position within the rows.
- `justify-items`: Aligns grid items along the row axis.
- `align-items`: Aligns grid items along the column axis.
- `justify-content`: Aligns the grid within the container along the row axis.
- `align-content`: Aligns the grid within the container along the column axis.

### CSS Flexbox Layout

CSS Flexbox Layout is a one-dimensional layout method for arranging items in rows or columns. It excels at distributing space within an item and aligning content vertically and horizontally.

#### Basic Concepts

- **Flex Container**: The element on which `display: flex` is applied. It becomes the parent of all flex items.
- **Flex Item**: The direct children of the flex container.
- **Main Axis**: The primary axis along which flex items are laid out (horizontal by default).
- **Cross Axis**: The axis perpendicular to the main axis (vertical by default).

#### Basic Example

```css
.container {
  display: flex;
  justify-content: space-between; /* Distribute space between items */
  align-items: center; /* Align items vertically center */
  height: 100px; /* Example height */
}

.item {
  flex: 1; /* Grow items to fill container */
  margin: 0 10px; /* Margin between items */
}
```

```html
<div class="container">
  <div class="item">Item 1</div>
  <div class="item">Item 2</div>
  <div class="item">Item 3</div>
</div>
```

#### Flexbox Properties

- `display: flex;` or `display: inline-flex;`
- `flex-direction`: Defines the direction of the main axis (row, row-reverse, column, column-reverse).
- `flex-wrap`: Controls whether flex items wrap onto multiple lines (nowrap, wrap, wrap-reverse).
- `flex-flow`: Shorthand for `flex-direction` and `flex-wrap`.
- `justify-content`: Aligns items along the main axis (flex-start, flex-end, center, space-between, space-around, space-evenly).
- `align-items`: Aligns items along the cross axis (flex-start, flex-end, center, baseline, stretch).
- `align-content`: Aligns flex lines within the flex container (flex-start, flex-end, center, space-between, space-around, stretch).
- `align-self`: Overrides `align-items` for individual flex items (auto, flex-start, flex-end, center, baseline, stretch).
- `order`: Controls the order of flex items.
- `flex-grow`: Defines how much a flex item should grow relative to the rest.
- `flex-shrink`: Defines how much a flex item should shrink relative to the rest.
- `flex-basis`: Defines the default size of an element before the remaining space is distributed.
- `flex`: Shorthand for `flex-grow`, `flex-shrink`, and `flex-basis`.

### Example: Combining Grid and Flexbox

Combining Grid and Flexbox can create powerful layouts. Use Grid for the overall layout and Flexbox for the alignment of components within grid items.

```css
/* Grid Container */
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

/* Grid Items */
.grid-item {
  background-color: lightgray;
  padding: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
}
```

```html
<div class="grid-container">
  <div class="grid-item">Item 1</div>
  <div class="grid-item">Item 2</div>
  <div class="grid-item">Item 3</div>
</div>
```

In this example, the grid container uses Grid Layout to define a three-column layout, and each grid item uses Flexbox to center its content.

### Conclusion

Both CSS Grid and Flexbox are powerful tools for creating responsive and flexible web layouts. CSS Grid is ideal for two-dimensional layouts, while Flexbox is better suited for one-dimensional layouts. Understanding and combining these layout systems can help you build complex and responsive designs efficiently.