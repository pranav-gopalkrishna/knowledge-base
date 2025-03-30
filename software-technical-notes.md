<h1>SOFTWARE TECHNICAL NOTES</h1>

---

**Contents**:

- [Adding a path to the PATH variable in Windows](#adding-a-path-to-the-path-variable-in-windows)
- [Scope](#scope)
- [Namespace: What is it and why learn about it?](#namespace-what-is-it-and-why-learn-about-it)
  - [Definitions](#definitions)
  - [Relevance of learning about namespace rules](#relevance-of-learning-about-namespace-rules)
  - [Types of namespaces in Python](#types-of-namespaces-in-python)
- [Separation of concerns](#separation-of-concerns)
- [Server vs. server-side application](#server-vs-server-side-application)
  - [Key differences](#key-differences)
  - [Why this distinction matters](#why-this-distinction-matters)
- [GUI vs. CLI: Event-driven vs. Sequential](#gui-vs-cli-event-driven-vs-sequential)

---

# Adding a path to the PATH variable in Windows
PATH is an environment variable in Windows that is a string containing the paths that are going to be checked by the command line interface whenever a script name is entered. If the script is in one of the directories referred to by path, it can be run directly as a command using command line. However, note that once you update the PATH variable, restart the command line application (e.g. Command Prompt or Powershell) for the changes to be registered by the command line application.

> **Key reference**: [_What is the use of Python -m flag?_ from **GeeksForGeeks**](https://www.geeksforgeeks.org/what-is-the-use-of-python-m-flag/)

# Scope
Scope measures the visibility and lifetime of a variable.

# Namespace: What is it and why learn about it?
_Help define the scope of a variable or function._

## Definitions
**Definition 1**:

The place where a variable is stored. Namespaces are implemented as dictionaries. There are the local, global and built-in namespaces as well as nested namespaces in objects. Namespaces support modularity by preventing naming conflicts. For instance, the functions `builtins.open` and `os.open()` are distinguished by their namespaces. Namespaces also aid readability and maintainability by making it clear which module implements a function. For instance, writing `random.seed()` or `itertools.islice()` makes it clear that those functions are implemented by the random and itertools modules, respectively.

> **Reference**: [namespace (Python Glossary)](https://docs.python.org/3/glossary.html#term-namespace)

---

**Definition 2**:

A namespace is a mapping from names to objects. Most namespaces are currently implemented as Python dictionaries, but that's normally not noticeable in any way (except for performance), and it may change in the future. Examples of namespaces are: the set of built-in names (containing functions such as `abs()`, and built-in exception names); the global names in a module; and the local names in a function invocation. In a sense the set of attributes of an object also form a namespace. The important thing to know about namespaces is that there is absolutely no relation between names in different namespaces; for instance, two different modules may both define a function `maximize` without confusion — users of the modules must prefix it with the module name.

> **Reference**: [Python Scopes and Namespaces](https://docs.python.org/3/tutorial/classes.html#python-scopes-and-namespaces)

---

## Relevance of learning about namespace rules

Class definitions play some neat tricks with namespaces, and you need to know how scopes and namespaces work to fully understand what's going on. Incidentally, knowledge about this subject is useful for any advanced Python programmer.

> **Reference**: [Python Scopes and Namespaces](https://docs.python.org/3/tutorial/classes.html#python-scopes-and-namespaces)

## Types of namespaces in Python
- Built-in namespace:
    - Contains Python's built-in functions and objects
    - Available throughout the execution of the program
- Global namespace:
    - Contains names defined at the module level
    - Created when the module is loaded
    - Persists until the script ends
- Local namespace:
    - Created when a function is called
    - Contains function arguments and locally defined variables
    - Destroyed when the function exits
- Enclosing namespace (closure scope):
    - Refers to non-local names in enclosing functions

# Separation of concerns
Separations of concerns is the approach of dividing units of functionality to self-contained modules that interact with other modules via an interface that is not dependent on module implementation. Why do this?

- **Key reason**: Preventing the cascading effect of errors <br> _Hence, errors in one functionality minimally affect others_
- Flexibility and extensibility
- Readability and solution simplification <br> _In both conceptual solution and practical implementation_

# Server vs. server-side application
## Key differences
| **Aspect** | **Server** | **Server-Side Application** |
| --- | --- | --- |
| **Definition** | A physical or virtual machine that hosts services and handles requests. | Software running on a server that processes logic, manages data, and responds to client requests. |
| **Role** | Provides resources (e.g., CPU, memory, network access) and runs applications. | Handles business logic, interacts with databases, and serves client requests. |
| **Example** | A cloud instance, a physical data center machine, or a local machine running a service. | A Flask, FastAPI, or Dash application running on a server. |
| **Persistence** | Exists independently of applications; can run multiple applications. | Runs on a server but may stop/restart independently. |
| **Analogy** | A restaurant building. | The chefs and kitchen staff preparing meals (server-side logic). |

## Why this distinction matters
- **Know what is running where** <br> _E.g. a Dash app is a server-side application while Zoho Catalyst is the server hosting it_
- **Optimize accordingly** <br> _E.g. reducing HTTP overhead by replacing frequent requests with WebSockets_
- **Avoid unnecessary complexity** <br> _E.g. not treating the server as more than a host unless needed_

This differentiation ensures clarity in:

- Coding
- Debugging
- Deployment

# GUI vs. CLI: Event-driven vs. Sequential
Unlike a console mode application, which is executed in a sequential manner, a GUI based application is event driven. Functions or methods are executed in response to user’s actions like clicking on a button, selecting an item from a collection or a mouse click etc., called events. All GUI applications are event-driven. Application events are generated primarily from users, but they can also be generated by other means, such as an Internet connection, a window manager, or a timer.

> **References**:
> 
> - [_PyQt5 - Signals & Slots_, **tutorialspoint.com**](https://www.tutorialspoint.com/pyqt5/pyqt5_signals_and_slots.htm)
> - [_PyQt events_, **Learn Python PyQt**](https://pythonpyqt.com/pyqt-events/)