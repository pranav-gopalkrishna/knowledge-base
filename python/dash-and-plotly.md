<h1>PYTHON PLOTLY AND DASH</h1>

---

**Contents**:

- [Dash (Python-based framework)](#dash-python-based-framework)
- [Periodic live updates in Dash](#periodic-live-updates-in-dash)
- [Limit axes ranges of a Plotly graph](#limit-axes-ranges-of-a-plotly-graph)
- [Shape rendering vs. background image rendering in Plotly graphs](#shape-rendering-vs-background-image-rendering-in-plotly-graphs)
  - [Why is a background image faster?](#why-is-a-background-image-faster)
    - [1. Plotly's shape rendering is expensive](#1-plotlys-shape-rendering-is-expensive)
    - [2. Rasterized backgrounds are faster](#2-rasterized-backgrounds-are-faster)
  - [3. Avoids DOM overload](#3-avoids-dom-overload)
  - [When to use a background image vs. Plotly shapes](#when-to-use-a-background-image-vs-plotly-shapes)
- [Add a background image to a Plotly graph](#add-a-background-image-to-a-plotly-graph)
  - [Method 1: Using image URL](#method-1-using-image-url)
  - [Method 2: Using image in base 64 encoding](#method-2-using-image-in-base-64-encoding)
  - [Method 3: Using PIL (Python Imaging Library)](#method-3-using-pil-python-imaging-library)
  - [Alternative figure method for the same result](#alternative-figure-method-for-the-same-result)
  - [KEY POINT: Using local files as backgrounds in Plotly graphs](#key-point-using-local-files-as-backgrounds-in-plotly-graphs)

---

# Dash (Python-based framework)
Dash is a low-code Python framework for rapidly building web-based data applications ([source](https://dash.plotly.com/)).  
It is designed for creating interactive analytical web applications **without requiring extensive front-end development knowledge** (e.g., JavaScript, HTML, or CSS).  

---

**KEY POINT: Dash is built on Flask**:

Dash is **built on top of Flask**, which is a lightweight **WSGI (Web Server Gateway Interface) web framework**. WSGI is a specification that defines how web servers interact with Python-based web applications. Dash extends Flask by adding higher-level abstractions for building interactive user interfaces, including **inputs, buttons, graphs, and other UI elements**. Dash **seamlessly integrates Plotly.js**, a browser-based graphing library, to simplify visualisation development.

---

**_How do Dash and Flask interact?_**

- Dash **relies on Flask** for handling **HTTP requests and responses**, but **abstracts away** much of Flask’s routing and request-handling logic
- Unlike a pure Flask app (where developers must manually define routes, manage state, and serve UI components), Dash **automatically generates the required Flask routes and request-response system** behind the scenes
- The developer primarily focuses on defining the **layout** (UI components) and **callbacks** (interactivity logic), while Dash manages URL handling and state updates internally

---

**_Can a Dash application be built using Flask alone?_**

A Dash application can be recreated in Flask, but it would require:

1. **Manually setting up Flask routes** to serve different components and handle HTTP requests
2. **Embedding Plotly.js** within the frontend manually.  
3. **Handling state updates and interactivity** using AJAX or WebSockets, which Dash manages automatically 

Thus, while Flask alone can replicate a Dash app’s functionality, Dash dramatically simplifies development by handling these complexities for the developer.  

---

**KEY TAKEAWAYS**:

- Dash uses Flask to handle HTTP requests and responses but abstracts this from the developer
- A Dash-like application can be implemented using Flask alone, but it requires significantly more manual work

# Periodic live updates in Dash
Components in Dash usually update through user interaction like selecting a dropdown, dragging a slider, or hovering over points. If you are building an application for monitoring, you may want to update components in your application every few seconds or minutes.

The `dcc.Interval` component allows you to update components on a predefined interval. The `n_intervals` property is an integer that is automatically incremented every time interval milliseconds pass. You can listen to this variable inside your app's callback to fire the callback on a predefined interval.

> **Reference**: [_Live Updating Components_ from **dash.plotly.com**](https://dash.plotly.com/live-updates)

---

**More on the `dcc.Interval` component**:

Note that this component is essentially a constantly progressing component, which generates intervals indefinitely as per the given parameters. As per its docstring, it is a component that repeatedly increments a counter `n_intervals` with a fixed time delay between each increment; thus, interval is good for triggering a component on a recurring _and indefinite_ basis. The time delay is set with the property `interval` in milliseconds. Note that you can set an upper limit to the number of times a counter is incremented using the `max_intervals` argument, but by default, there is no upper limit.

**Keyword arguments for instantiating `dcc.Interval`**:

- `id` (string; optional)
    - The ID of this component
    - Used to identify dash components in callbacks
    - Needs to be unique across all of the components in an app
- `disabled` (boolean; optional)
    - If True, the counter will no longer update
- `interval` (number; default 1000)
    - Number of milliseconds before counter increment
- `max_intervals` (number; default -1)
    - Number of times the interval will be fired
    - If -1, then the interval has no limit (the default)
    - If 0 then the interval stops running
- `n_intervals` (number; default 0)
    - Number of times the interval has passed

# Limit axes ranges of a Plotly graph
Let `fig` be a `plotly.graph_objects.Figure` object. This object represents a plottable figure. It has an attribute, `layout`, which is the data structure containing all the information about the layout of the figure, including margins, axis specifications, dimensions and more. Now, to limit the ranges of one or more axes (i.e. either the x-axis or the y-axis), regardless of however you modified the layout before, you can simply update the layout as follows:

**For x-axis**:

```python
# Assume `x_1` and `x_2` are defined numerical values
fig.update_layout(xaxis_range=[x_1, x_2])
```

**For y-axis**:

```python
# Assume `y_1` and `y_2` are defined numerical values
fig.update_layout(yaxis_range=[y_1, y_2])
```

**For both axes**:

```python
fig.update_layout(xaxis_range=[x_1, x_2], yaxis_range=[y_1, y_2])
```

_Previous modifications hold even as x and/or y ranges are limited._

# Shape rendering vs. background image rendering in Plotly graphs
_The content of the first 2 sections was largely generated by ChatGPT-4._

## Why is a background image faster?
### 1. Plotly's shape rendering is expensive
- **Plotly shapes** (rectangles, lines, polygons, etc.) **are vector-based** and treated as **interactive elements**
- When you animate agents, Plotly **recalculates the entire figure**, which includes **all static elements (shapes, grid, warehouse objects, etc.)**, leading to **slower rendering**

**_Using a static background image means fewer redraws, improving performance._**

### 2. Rasterized backgrounds are faster
- **A PNG/JPG image is pre-rendered and drawn as a single layer** rather than as multiple **SVG/vector elements** 
- Since **Plotly animations update only the foreground elements (agents)**, the image remains **static**, **reducing unnecessary computations**

**_This reduces the number of graphical elements Plotly needs to handle per frame._**  

## 3. Avoids DOM overload
- Each **Plotly shape or trace adds elements** to the browser’s **DOM (Document Object Model)**.  
- A complex warehouse layout with hundreds of shelves, grids, and paths **bloats the DOM**, making updates slow.  

**_A background image avoids DOM bloat and keeps animations smooth._**

---

## When to use a background image vs. Plotly shapes

| Approach | Pros | Cons | Best For |
|----------|------|------|----------|
| **Background Image** (PNG/JPG in `layout.images`) | 🚀 **Fast rendering**<br>📉 **Lower CPU/GPU usage**<br>🖼️ **Static environment** | ❌ **Less interactivity** (can't hover/select objects)<br>📏 **Resolution-dependent** | **Large & complex static environments** (e.g., warehouse layouts, maps) |
| **Plotly Shapes/Traces** | 🎯 **Interactive** (hover effects, selections)<br>🔄 **Easier to update dynamically** | 🐢 **Slow for complex layouts**<br>🔄 **Redraws everything on each update** | **Small, frequently changing environments** |

# Add a background image to a Plotly graph
## Method 1: Using image URL
```python
import plotly.graph_objects as go
# Using image URL
image_url = "https://..." # Elipses are just for demo
print("Adding image...")
fig.add_layout_image(
    dict(
        source=image_url,
        xref="x",
        yref="y",
        x=0,
        y=3,
        sizex=2,
        sizey=2,
        sizing="stretch",
        opacity=0.5,
        layer="below"))
```

## Method 2: Using image in base 64 encoding
```python
import plotly.graph_objects as go
import base64
# Using base64 encoding and decoding
def b64_image(image_filename):
    with open(image_filename, 'rb') as f:
        image = f.read()
    return 'data:image/png;base64,' + base64.b64encode(image).decode('utf-8')
print("Adding image...")
fig.add_layout_image(
    dict(
        source=b64_image(image_path),
        xref="x",
        yref="y",
        x=0,
        y=3,
        sizex=2,
        sizey=2,
        sizing="stretch",
        opacity=0.5,
        layer="below"))
```

## Method 3: Using PIL (Python Imaging Library)
```python
import plotly.graph_objects as go
from PIL import Image
pil_image = Image.open(image_path)
print("Adding image...")
fig.add_layout_image(
    dict(
        source=pil_image,
        xref="x",
        yref="y",
        x=0,
        y=3,
        sizex=2,
        sizey=2,
        sizing="stretch",
        opacity=0.5,
        layer="below"))
```

## Alternative figure method for the same result
The following...

```python
fig.add_layout_image(
    dict(
        source=pil_image,
        xref="x",
        yref="y",
        x=0,
        y=3,
        sizex=2,
        sizey=2,
        sizing="stretch",
        opacity=0.5,
        layer="below"))
```

... is equivalent to...

```python
environment_layout_fig.update_layout(
    images=[
        dict(
            source=pil_image,
            xref="x",
            yref="y",
            x=0,
            y=3,
            sizex=2,
            sizey=2,
            sizing="stretch",
            opacity=0.5,
            layer="below")])
```

---

**NOTE**: _You can include more than one image, it seems, but I have not tried that yet._

## KEY POINT: Using local files as backgrounds in Plotly graphs
Directly putting the local file path in the "source" argument in the above methods does not work; it seems Plotly's implementation is not built to handle local file paths. Hence, to use local images, use method 2 or method 3.