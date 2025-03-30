<h1>PYTHON TECHNICAL NOTES</h1>

---


**Contents**:

- [`python -m`](#python--m)
- [Method binding in Python](#method-binding-in-python)
  - [Understanding method storage in Python classes](#understanding-method-storage-in-python-classes)
  - [Key takeaways](#key-takeaways)
- [Attribute sharing among objects in Python](#attribute-sharing-among-objects-in-python)
  - [Key rules](#key-rules)
  - [Examples](#examples)
  - [How to avoid class variable sharing?](#how-to-avoid-class-variable-sharing)
- [Changing a value of a list, array or dictionary](#changing-a-value-of-a-list-array-or-dictionary)
- [Python NumPy `np.where` vs. `np.argwhere`](#python-numpy-npwhere-vs-npargwhere)
  - [1. `np.where(condition)`](#1-npwherecondition)
    - [Purpose:](#purpose)
    - [Output:](#output)
    - [Example:](#example)
  - [2. `np.argwhere(condition)`](#2-npargwherecondition)
    - [Purpose](#purpose-1)
    - [Output](#output-1)
    - [Example](#example-1)
  - [Key differences](#key-differences)
  - [When to use which?](#when-to-use-which)
- [Python module name resolution](#python-module-name-resolution)
  - [SIDE NOTE 1: `site` module](#side-note-1-site-module)
  - [SIDE NOTE 2: The import system](#side-note-2-the-import-system)
  - [Accessing modules/packages apart from default routes](#accessing-modulespackages-apart-from-default-routes)
- ["Compiled" Python files (about `__pycache__`)](#compiled-python-files-about-__pycache__)
- [`.extend` to add the elements of an iterable to a list](#extend-to-add-the-elements-of-an-iterable-to-a-list)
- [Read further](#read-further)

---

# `python -m`
The `-m` switch in Python is a command-line option that allows you to run a module as a script. This means you can execute Python code directly from the command line without the need for an external script file through which to import the module and run the code. In other words, by using `-m`, you can invoke Python modules as standalone programs.

# Method binding in Python
**_Are methods duplicated for each instance in Python?_**

## Understanding method storage in Python classes
When you define a class in Python and instantiate it multiple times, **the methods are not duplicated for each instance**. Instead, they are stored at the class level and shared across instances. Note, however, that each instance has its own binding that associates it to the class method.

Binding is the association of an identifier (i.e. a name) to a variable or function. In Python, method binding happens dynamically. When a method is accessed via an instance, Python wraps the method in a bound method object, which includes the instance as the first argument (note that a method is a function defined in the namespace of a class and accessible via the class' instances).

Python automatically binds instance methods to a bound method object by inserting the instance as the first argument (however, if a method is decorated with `@staticmethod`, this automatic insertion does not occur). Note that while each instance has its own bindings, the method itself is shared across instances; in other words, while the method reference is stored at the class level, each instance creates its own bound method object.

---

**In brief**:

- Methods in a class are stored:
    - In the class's `__dict__`
    - Not in each instance's `__dict__`
- Each instance gets a reference to the class methods <br> _Rather than creating a new copy_

---

> **References**:
>
> - _When an instance method object is called, the underlying function (`__func__`) is called, inserting the class instance (`__self__`) in front of the argument list. For instance, when `C` is a class which contains a definition for a function `f()`, and `x` is an instance of `C`, calling `x.f(1)` is equivalent to calling `C.f(x, 1)`._ ([_3. Data model_, "Methods on code objects", (Python documentation reference)](https://docs.python.org/3/reference/datamodel.html#methods-on-code-objects))
> - _In general, methods work as follows. When a non-data attribute of an instance is referenced, the instance’s class is searched. If the name denotes a valid class attribute that is a function object, references to both the instance object and the function object are packed into a method object. When the method object is called with an argument list, a new argument list is constructed from the instance object and the argument list, and the function object is called with this new argument list._ ([_9. Classes_ (Python documentation tutorial)](https://docs.python.org/3/tutorial/classes.html))
> - _Static method objects provide a way of defeating the transformation of function objects to method objects described above. A static method object is a wrapper around any other object, usually a user-defined method object. When a static method object is retrieved from a class or a class instance, the object actually returned is the wrapped object, which is not subject to any further transformation. Static method objects are also callable. Static method objects are created by the built-in `staticmethod()` constructor._ ([_3. Data model_, "Static method objects", (Python documentation reference)](https://docs.python.org/3/reference/datamodel.html#static-method-objects))

---

**Example**:

```python
class Agent:
    def __init__(self, name):
        self.name = name  # Instance-specific attribute

    def move(self):
        print(f"{self.name} is moving.")  # Shared method

# Creating multiple instances
agent1 = Agent("A1")
agent2 = Agent("A2")

# Checking method identity
print(agent1.move is agent2.move)  # False (bound to different instances)
print(agent1.move.__func__ is agent2.move.__func__)  # True (same underlying function)
```

---

**Explanation**:

- `agent1.move is agent2.move` → `False` <br> _Because bound methods include instance context_
- `agent1.move.__func__ is agent2.move.__func__` → `True` <br> _Because the actual function is shared at the class level_

## Key takeaways
- Methods are stored once at the class level
- Instances refer to shared methods, avoiding duplication 
- Only instance attributes are stored uniquely per instance <br> _These attributes are those initalised within_ `.__init__`

This design ensures:

- Memory efficiency
- Consistent behavior across instances

# Attribute sharing among objects in Python
## Key rules
Instantiating a subclass does not cause different subclass instances to share variables or methods unless those variables are class-level (i.e. shared at the class level rather than being instance-specific).

- **Instance variables are unique**
  - Each subclass instance gets its own copy of instance variables
  - These are set in the __init__ method
  - They are independent for each object
- **Class variables (`ClassName.variable`) are shared**
  - All instances of the class and its subclasses share it
  - Can be modified at the instance level <br> _This modifies it for all instances, so be careful_

## Examples
**Instance variables (not shared)**:

```python
class Parent:
    def __init__(self):
        self.instance_var = []  # Each instance gets a new list

class Child1(Parent):
    pass

class Child2(Parent):
    pass

c1 = Child1()
c2 = Child2()

c1.instance_var.append(1)
print(c1.instance_var)  # [1]
print(c2.instance_var)  # [] (Not shared)
```

Each instance gets its own `instance_var`, so changes to one do not affect the other.

---

**Class variables (shared)**:

```python
class Parent:
    class_var = []  # Shared among all instances

class Child1(Parent):
    pass

class Child2(Parent):
    pass

c1 = Child1()
c2 = Child2()

c1.class_var.append(1)
print(c1.class_var)  # [1]
print(c2.class_var)  # [1] (Shared!)
```

Here, `class_var` is shared across all instances because it is defined at the class level.

## How to avoid class variable sharing?
If you want to ensure each instance gets its own variable, always define variables inside __init__ rather than at the class level.

# Changing a value of a list, array or dictionary
Mutable data structures like lists, arrays, objects, and dictionaries are references to objects in memory. When you pass them to functions or reassign them to other identifiers, they still refer to the same object. This means that changing the contents of a mutable data structure (like modifying a list) will alter the original object, even if it was passed to a function or reassigned. Thus, alterations made to mutable structures within functions are reflected outside the function's scope as well.

# Python NumPy `np.where` vs. `np.argwhere`
_The following is generated by ChatGPT-4 but validated by me._

NumPy provides powerful functions for element selection and indexing, two of which are `np.where` and `np.argwhere`. While they may seem similar, they serve different purposes and return results in distinct formats. Understanding their differences is crucial for efficient data manipulation in NumPy.

## 1. `np.where(condition)`

### Purpose:
- Returns indices where the condition is `True`
- Usable for element-wise selection when extra parameters are given

### Output:
If only a condition is given, it returns a tuple of arrays, one for each dimension. If `x` and `y` are provided, it acts like a vectorised `if-else`.

### Example:
```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
indices = np.where(a > 3)
print(indices)  # (array([1, 1, 1]), array([0, 1, 2]))
```

Here, `np.where(a > 3)` returns 2 arrays:

- Array at tuple position 0 for row indices
- Array at tuple position 1 for column indices

**NOTE**: _If array is 1D, tuple position 1 is empty._

**Conditional selection example**:

```python
b = np.where(a > 3, 100, 0)
print(b)
```

Replaces values greater than 3 with `100`, and others with `0`.

## 2. `np.argwhere(condition)`

### Purpose
Returns indices where the condition is `True` in a row-wise format.

### Output
A 2D array; each row represents the index of a matching element.

### Example
```python
indices = np.argwhere(a > 3)
print(indices)  # [[1 0]
                #  [1 1]
                #  [1 2]]
```

Unlike `np.where`, `np.argwhere` returns a structured 2D array of index pairs.

## Key differences
| Feature | `np.where(condition)` | `np.argwhere(condition)` |
| --- | --- | --- |
| Output format | Tuple of index arrays | 2D array of indices |
| Dimension | Returns separate 1D arrays | Returns a single 2D array |
| Use Case | Works for both selection and indexing | Used mainly for indexing |

## When to use which?
- Use **`np.where`** when you need a tuple of indices or conditional selection
- Use **`np.argwhere`** when you need a structured list of coordinates

---

> **References**:
> 
> - NumPy documentation: [https://numpy.org/doc/stable](https://numpy.org/doc/stable)
> - StackOverflow discussions on `np.where` vs. `np.argwhere`

# Python module name resolution
How does the Python interpreter resolve module/package references?

---

When a module is imported using a given name, the interpreter first searches for a built-in module with that name (note that these module names are also listed in `sys.builtin_module_names`). If not found, it then searches for a Python source file (i.e. a file with the extension `.py`) with the given name in a list of directories given by the **module search path**.

A module search path (which includes the path(s) of one or more directories) is initialised when Python starts (i.e. when the interpreter is run). This module search path may be accessed at `sys.path`. `sys.path` is initialised from these location (in order):

- Current working directory
- Directories listed in the `PYTHONPATH` environment variable <br> _Has the same syntax as the shell variable `PATH`_
- Installation-dependent default <br> _By convention, a `site-packages` directory, handled by the `site` module_

**NOTE**: _`PYTHONPATH` affects all installed Python versions/environments. For more nuanced ways of modifying the module search path, see the `site` module._

> **References**:
> 
> - [The initialization of the `sys.path` module search path](https://docs.python.org/3/library/sys_path_init.html)
> - [The Module Search Path](https://docs.python.org/3/tutorial/modules.html#the-module-search-path)

---

## SIDE NOTE 1: `site` module
The `site` module is automatically imported during initialisation (the automatic import can be suppressed using the interpreter's `-S` option), and importing this module normally appends site-specific paths to the module search path and adds callables, including `help()`, to the built-in [namespace](#namespace-what-is-it-and-why-learn-about-it).

> **Reference**: [`site` — Site-specific configuration hook](https://docs.python.org/3/library/site.html#module-site)

## SIDE NOTE 2: The import system
Python code in one module gains access to the code in another module by the process of importing it. The import statement is the most common way of invoking the import machinery, but it is not the only way. Functions such as `importlib.import_module()` and built-in `__import__()` can also be used to invoke the import machinery.

The import statement combines two operations; it searches for the named module, then it binds the results of that search to a name in the local scope. The search operation of the import statement is defined as a call to the `__import__()` function, with the appropriate arguments. The return value of `__import__()` is used to perform the name binding operation of the import statement. See the import statement for the exact details of that name binding operation.

> **Reference**: [The import system](https://docs.python.org/3/reference/import.html)

---

## Accessing modules/packages apart from default routes
**NOTE**: _CWD = Current Working Directory_

**1. Run script from the directory the module/package**:

Change the CWD execute the script like this:

```bash
cd directory/with/module
python directory/with/script/script.py
```

From this new CWD, the package is accessible directly.

---

**2. Modify `PYTHONPATH` in the desired script**:

Manually add the module/package-containing directory to Python's module search path, which can be accessed in code via the `sys` module as `sys.path`. For example:

```python
python
import sys

# Adding the desired directory `sys.path`:
sys.path.append("absolute/path/of/directory/with/module")

import desired_module # This will now work
```

---

**3. Set `PYTHONPATH` environment variable**:

Add the module/package-containing directory to the `PYTHONPATH` environment variable directly. For example:

```bash
export PYTHONPATH=absolute/path/of/directory/with/module
```

Now you can run the script with the import statement from anywhere.

---

**4. Use a relative import (not recommended)**:

Within `script.py`, you can do a relative import:

```python
from "relative/path/of/directory/with/module" import desired_module
```

Relative imports are best avoided for standalone scripts.

# "Compiled" Python files (about `__pycache__`)
To speed up loading modules, Python caches the compiled version of each module in the `__pycache__` directory under the format: `module.version.pyc`, where the version encodes the format of the compiled file; it generally contains the Python version number. For example, in CPython release 3.3, the compiled version of `spam.py` would be cached as `__pycache__/spam.cpython-33.pyc`. This naming convention allows compiled modules from different releases and different versions of Python to coexist.

Python checks the modification date of the source against the compiled version to see if it's out of date and needs to be recompiled. This is a completely automatic process. _Also, the compiled modules are platform-independent, so the same library can be shared among systems with different architectures._

Python does not check the cache in two circumstances. First, it always recompiles and does not store the result for the module that's loaded directly from the command line. Second, it does not check the cache if there is no source module. To support a non-source (compiled only) distribution, the compiled module must be in the source directory, and there must not be a source module.

> **Reference**: ["Compiled" Python files](https://docs.python.org/3/tutorial/modules.html#the-module-search-path)

# `.extend` to add the elements of an iterable to a list
**NOTE**: `.extend` is a method defined for `list` objects.

---

Consider you have 2 lists:

```python
x = [...]
y = [...]
```

Now, let us say we want to append `y`'s elements to `x`. Using...

```python
x.extend(y) # List extension
```

... is functionally equivalent to...

```python
x = x + y # List concatenation
```

---

However, extension is more efficient than concatenation because:

- Extension modifies `x` in place <br> _This avoids the creation of a new list_
- Concatenation creates a new list <br> _Hence, it copies elements from both `x` and `y`, and then reassigns `x`_

---

More precisely, if...

- N = `len(x)`
- K = `len(y)`

... then the time complexities are as follows:

- List concatenation: O(N + K) (new list creation and copying)
- List extension: O(K) (appending K elements to `x` in-place)

---

This makes `.extend()` the preferred choice for efficiency.

# Read further
- [Dash and Plotly](./dash-and-plotly.md)
- [PyQt/PySide](./pyqt-pyside.md) <br> _PyQt/PySide technical notes_