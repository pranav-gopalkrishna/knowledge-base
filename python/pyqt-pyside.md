<h1>PYQT/PYSIDE</h1>

**_Qt, PyQt/PySide and PyQtGraph notes_**

---

**Contents**:

- [PyQt vs. PySide](#pyqt-vs-pyside)
  - [APIs](#apis)
  - [Distribution licensing](#distribution-licensing)
- [PyQtGraph distribution licensing](#pyqtgraph-distribution-licensing)
- [`QApplication` and event loops](#qapplication-and-event-loops)
- [Widgets](#widgets)
- [Command-line arguments to the Qt application](#command-line-arguments-to-the-qt-application)
- [Window](#window)
  - [What is a window?](#what-is-a-window)
  - [Creating a window](#creating-a-window)
  - [`QMainWindow` widget](#qmainwindow-widget)
    - [About](#about)
    - [Customising](#customising)
      - [Adding a central widget](#adding-a-central-widget)
      - [Fixing window size/size-range](#fixing-window-sizesize-range)
- [Events, signals and slots](#events-signals-and-slots)
- [Event handling in PyQt5](#event-handling-in-pyqt5)
- [Event hierarchies](#event-hierarchies)
  - [Python object hierarchy: _Inheritance forwarding_](#python-object-hierarchy-inheritance-forwarding)
  - [Application layout hierarchy: _Layout forwarding_](#application-layout-hierarchy-layout-forwarding)
- [Hide plotting axes](#hide-plotting-axes)

---

**NOTE**: _`PyQt6` and `PySide6` have very similar interfaces, and all the following notes apply to both PyQt5 and PySide6 unless explicitly stated otherwise. Furthermore, most notes applicable to the above two are also applicable to `PyQt5` and vice versa; assume this to be the case unless explicitly stated otherwise._

# PyQt vs. PySide
## APIs
Both packages are wrapping the same library: Qt. Anything you learn with one library will be easily applied to a project using the other. Also, no matter with one you choose to use, it's worth familiarising yourself with the other so you can make the best use of all available online resources: using PyQt6 tutorials to build your PySide6 applications for example, and vice versa.

**NOTE**: _PyQt5 has very similar API to PyQt6 and PySide6._

> **Reference**: [_PyQt6 vs PySide6_, **PythonGUIs.com**](https://www.pythonguis.com/faq/pyqt6-vs-pyside6)

## Distribution licensing
A notable difference between these 2 Python bindings for Qt, i.e. PyQt and PySide, is licensing, with PyQt being available under a GPL, and PySide6 under a LGPL license. If you want to distribute your software but not share the source code of your software: (1) using PyQt, you need to purchase a commercial license from Riverbank (the developer of PyQt), and (2) using PySide, you do not need to purchase any commercial license and can freely use PySide.

> **References**:
> 
> - [_PyQt6 vs PySide6_, **PythonGUIs.com**](https://www.pythonguis.com/faq/pyqt6-vs-pyside6)
> - [_PyQt vs PySide Licensing_](https://www.pythonguis.com/faq/pyqt-vs-pyside/)

---

**About LGPL**:

The GNU Lesser General Public License (LGPL) is a variant of the GPL that offers more flexibility in certain scenarios. While LGPL also promotes software freedom, it has a less restrictive copyleft nature compared to GPL. One of the key attributes of LGPL is its ability to allow proprietary software to dynamically link with libraries licensed under LGPL without requiring the proprietary software to be licensed under LGPL as well.

This attribute makes LGPL particularly suitable for libraries and frameworks that are intended to be used by both open-source and proprietary software. By allowing proprietary software to link with LGPL-licensed libraries, LGPL encourages wider adoption and usage of the library, even in commercial applications.

_The following is less relevant but still worth noting_...

If modifications are made to the LGPL-licensed library itself, those modifications must be released under LGPL. This ensures that any improvements or changes to the library are contributed back to the open-source community. This requirement helps maintain the collaborative nature of open-source development and ensures that the library continues to evolve and improve over time.

**NOTE**: _GPL is preferrable to LGPL if strong copyleft is desired, since GPL ensures that all derivative works are also open-source. However, if we are not concerned with making our work open-source or ensuring that derivative works are also open source, LGPL is preferrable._

> **Reference**: [_GPL vs. LGPL_, **ThisVsThat.com**](https://thisvsthat.io/gpl-vs-lgpl)

---

**Other resources/evidence for licensing**:

- [_GNU Lesser General Public License_, **Wikipedia.org**](https://en.wikipedia.org/wiki/GNU_Lesser_General_Public_License)
- [_GNU Lesser General Public License version 3_, **OpenSource.org**](https://opensource.org/license/lgpl-3-0)
- [PySide official documentation](https://pypi.org/project/PySide/)
- [PySide6 official documentation](https://pypi.org/project/PySide6/)

# PyQtGraph distribution licensing
PyQtGraph is distributed under the MIT License, which is a **permissive open source license**. Permissive licenses offer significant freedom, allowing users to use, modify, and redistribute the software with minimal restrictions. Specifically, it allows proprietisation (i.e. distributing software commercially without having to share the source code).

> **References**:
>
> - [**mit-license.org**](https://mit-license.org/)
> - [_Open Source Licensing Explained: A Comprehensive Guide_, **TuxCare.com**](https://tuxcare.com/blog/open-source-licensing-explained/)
> - [_MIT License_, **choosealicense.com**](https://choosealicense.com/licenses/mit/)

# `QApplication` and event loops
The core of every Qt application is the QApplication class. Every application needs one — _and only one_ — QApplication object to function. This object holds the **event loop** of your application — _the loop which governs all user interaction with the GUI._

Each interaction with your application (e.g. key press, mouse click, mouse movement) generates an **event** which is placed on the **event queue**. _In the event loop, the queue is checked on each iteration and if a waiting event is found, the event and control is passed to the specific event handler for the event._ The event handler deals with the event, then passes control back to the event loop to wait for more events. **_There is only one running event loop per application._**

---

**Summary**:

- QApplication holds the Qt event loop
- One QApplication instance required
- Your application sits waiting in the event loop... <br> ..._until an action is taken_
- There is only one event loop running at any time

---

> **Reference**: [_Creating your first app with PyQt5_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/creating-your-first-pyqt-window/)

# Widgets
In Qt, like in most GUI frameworks, widget is the name given to a component of the UI that the user can interact with. User interfaces are made up of multiple widgets, arranged within the window. Qt comes with a large selection of widgets available and even allows you to create your own custom and customised widgets.

> **Reference**: [_PyQt5 Widgets_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/pyqt-basic-widgets/)

# Command-line arguments to the Qt application
We create an instance of QApplication, passing in `sys.argv`, which is Python list containing the command line arguments passed to the application. If you know you won't be using command line arguments to control Qt you can pass in an empty list instead. Examples are given below.

---

**App with command-line arguments**:

```python
from from PyQt5.QtWidgets import QApplication
app = QApplication(sys.argv)
```

**App without command-line arguments**:

```python
from from PyQt5.QtWidgets import QApplication
app = QApplication([])
```

---

> **Reference**: [_Creating your first app with PyQt5_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/creating-your-first-pyqt-window/)

# Window
> **Reference**: [_Creating your first app with PyQt5_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/creating-your-first-pyqt-window/)

## What is a window?
A window is a software component that holds the user-interface of your application. Every application needs at least one (but can have more). Application will (by default) exit when last window is closed.

## Creating a window
**_In Qt, all top level widgets are windows_**, that is, they don't have a parent and are not nested within another widget or layout. This means you can technically create a window using any widget you like. Importantly, widgets without a parent are invisible by default. So, after creating the window object, we must always call `.show()` to make it visible. You can remove the `.show()` and run the app, but you'll have no way to quit it!

---

**Example window creation**:

```python
from from PyQt5.QtWidgets import QWidget
window = QWidget() # Instantiating a widget without a parent
# NOTE: A widget without a parent is a window

window.show() # To ensure the above widget is visible
```

## `QMainWindow` widget
### About
As mentioned previously, in Qt, any widgets can be windows. However, most widgets have limited user-control functionality, and may offer just a single control (or no control). To address the complex needs of a fully-fledged application window, we have `QMainWindow`, which is a pre-made widget that provides a lot of standard window features useful in apps, including toolbars, menus, a statusbar, dockable widgets, etc.

### Customising
#### Adding a central widget
The core Qt widgets are always imported from the `QtWidgets` namespace, as are the `QMainWindow` and `QApplication` classes. When using `QMainWindow` we use `.setCentralWidget` to place a widget in the `QMainWindow`; by default it takes the whole of the window.

#### Fixing window size/size-range
The window is, by default, freely resizable; if you grab any corner with your mouse you can drag and resize it to any size you want. While it can be good to let your users resize your applications, sometimes you may want to place restrictions on minimum or maximum sizes, or lock a window to a fixed size. In Qt sizes are defined using a `QSize` object from the `PyQt5.QtCore` namespace. This accepts width and height parameters (in that order). The `QSize` instance is passed to `.setFixedSize()` (a method defined for `QWidget`, i.e. for any widget class, including `QMainWindow`). You can also pass it to `.setMinimumSize()` and `.setMaximumSize()` to set the minimum and maximum sizes respectively.

**NOTE**: _There are also methods of `QWidget` that allow the fixing of the sizes/size-ranges of particular dimensions, i.e. of the width or height alone, namely `setMinimumWidth`, `setMinimumHeight`, `setMaximumWidth` and `setMaximumHeight._

# Events, signals and slots
Events within a system (e.g. a website) or process (e.g. a running application) is a change in the state of some component within the system or process (e.g. mouse pointer, component interactions, component attribute changes, etc.). The main loop detects various events and sends them to the event object. In the event model, there are three participants.

- Event source
- Event object
- Event target

An event source is a change in the state of an object that generates an event. An event object is an object that encapsulates a state change in the event source. The event target is the object that wants to be notified about the event. Note that the event source object represents the task of processing an event to the event target.

---

**NOTES**:

- Every interaction the user has with a Qt application is an event
- There are many types of event
- Each event type represents a different type of interaction

---

Signals are notifications emitted by widgets when something happens; in other words, signals are notifications emitted by a widget in response to an event related to the widget. There are a number of widget-related events (e.g. button press, change in input text box, change in window title, etc.). _Many signals are initiated by user action (i.e. user-interaction events), but this is not a rule._

---

"Slot" is the name Qt uses for the receivers of signals. In Python, any callable (e.g. function, method, etc.) in your application can be used as a slot, simply by connecting the signal to it. If the signal sends data, then the receiving function will receive that data too. Many Qt widgets also have their own built-in slots (i.e. built-in methods that can be used as slots), meaning you can hook Qt widgets together directly.

---

**Summary**:

- Event is a state change of something in the application's context
- PyQt5 uses a unique signal and slot mechanism to handle events
- Signals and slots are used for communication between objects
- When a particular event occurs, the signal is fired

---

> **References**:
>
> - [_PyQt5 Signals, Slots & Events_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/pyqt-signals-slots-events/)
> - [_PyQt events_, **Learn Python PyQt**](https://pythonpyqt.com/pyqt-events/)

# Event handling in PyQt5
Qt represents these events using event objects which package up information about what happened. These events are passed to specific event handlers on the widget where the interaction occurred. By defining custom (or extended) event handlers, you can alter the way your widgets respond to these events. Event handlers are defined just like any other method, but the name is specific for the type of event they handle. You can intercept events associated with a widget (e.g. main window, button, input text area, etc.) by sub-classing the widget and overriding the event handler method on the class. You can choose to filter, modify, or ignore events, passing them up to the normal handler for the event by calling the parent class function with `super()`.

---

**Event objects**:

Inside the event handlers you have access to an event object (since event handlers take the event object as a parameter). This object contains information about the event and can be used to respond differently depending on what exactly has occurred.

---

> **Reference**: [_PyQt5 Signals, Slots & Events_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/pyqt-signals-slots-events/)

# Event hierarchies
> **Reference**: [_PyQt5 Signals, Slots & Events_, **PythonGUIs.com**](https://www.pythonguis.com/tutorials/pyqt-signals-slots-events/)

## Python object hierarchy: _Inheritance forwarding_
A widget may be a subclass of another widget; not necessarily a "child" within the application layout, but simply the subclass as defined in Python. You may want to intercept an event, do something with it, yet still trigger the default event handling behavior; this is because, if your object is inherited from a standard widget, it will likely have sensible behavior implemented by default. You can trigger this by calling up to the parent implementation using `super()`.

**NOTE**: _This is the Python parent class, not the PyQt` .parent()`._

## Application layout hierarchy: _Layout forwarding_
A widget may be contained/wrapped in another widget within the application layout; in other words, a widget may be a child of another widget. This is due to the composite and nested design of any application beyond a blank window. Here, the widgets are not necessarily _subclasses_ of each other, as defined in Python; whether or not they are subclasses is irrelevant here, since only the widget placement within the layout is relevant here.

When events are created for user interaction with the UI, these events are passed to the uppermost widget in the UI (i.e. the interacted widget which is the child of every other interacted widget). E.g. if you have a button in a window and click the button, the button will receive the event first. If the child widget cannot handle the event, or chooses not to, the event will be forwarded to the parent widget, which will be given a turn. This forwarding continues all the way up the layout hierarchy, until the event is handled or it reaches the main window.

---

In your own event handlers:

- You can mark an event as handled calling `.accept()` <br> _This will stop forwarding the event_
- You can mark it as unhandled by calling `.ignore()` <br> _This will continue forwarding the event_

**Marking as handled**:

```python
class CustomButton(QPushButton)
    def mousePressEvent(self, e):
        e.accept()
```

**Marking as unhandled**:

```python
class CustomButton(QPushButton)
    def event(self, e):
        e.ignore()
```

---

**Summary**:

If you want your widget to appear transparent to events, you can safely ignore events which you have actually responded to in some way. Similarly, you can choose to accept events you are not responding to in order to silence them.

# Hide plotting axes
> **Reference**: [_Is there a way to hide the x and y axis when using the ScatterPlotItem and PlotCurveItem_, **StackOverflow.com**](https://stackoverflow.com/questions/57030482/is-there-a-way-to-hide-the-x-and-y-axis-when-using-the-scatterplotitem-and-plotc)

`pyqtgraph.PlotWidget` has the method `hideAxis`, which takes a string argument ("bottom" for bottom axis (usually x-axis) and "left" for left axis (usually y-axis)). For an example usage, let `plot_widget` by a `pyqtgraph.PlotWidget` object; then, we do:

```python
plot_widget.hideAxis("bottom")
plot_widget.hideAxis("left")
```