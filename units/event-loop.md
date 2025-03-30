# Event-loop
**_An asychronous programming concept_** (see [asynchrony](./asynchrony.md))

**DEFINITION 1**:

Structure that continuously:

- Monitors asynchronous events (i.e. detect them)
- Dispatches asynchronous events (i.e. send them to event handlers)

---

**DEFINITION 2**:

The event loop is an asycnhronous runtime model that...

- ... waits for
- ... dispatches

events or messages in a program.

---

The event loop works by:

- Making a request to some internal or external event provider <br> _E.g. program code, apps widgets, external apps_
- Waiting for the response (which comes when an event is available)
- Calling the relevant event handler (thus "dispatching" the event)

> **Reference**: [_Event loop_, **Wikipedia.org**](https://en.wikipedia.org/wiki/Event_loop)

## In JavaScript
_JavaScript is single-threaded, processing only 1 task at a time._

The JavaScript engine executes a script from the top of the file and works its way down. It creates the execution contexts (i.e. namespaces) and pushes and pops functions onto and off the **call stack** in the execution phase. Furthermore, since a website/web-based app may have other components besides a JavaScript runtime, there may be other callbacks and tasks that the JavaScript runtime must manage over time; these tasks are stored in a **callback queue** (or task queue), which the JavaScript refers to in order to add further tasks to its call stack for execution.

---

**_The event loop is a constantly running process that monitors both the callback queue and the call stack._**

---

> **Reference**: [_JavaScript Event Loop_, **JavaScriptTutorial.net**](https://www.javascripttutorial.net/javascript-event-loop/)