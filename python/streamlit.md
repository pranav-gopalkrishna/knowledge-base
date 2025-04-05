<h1>STREAMLIT</h1>

---

**Contents**:

- [Installation](#installation)
- [Main concepts](#main-concepts)

---

# Installation
> **Key reference**: [Streamlit installation](https://docs.streamlit.io/get-started/installation)

**NOTE**: _To run Streamlit applications using the `streamlit` command after installing it, make sure the path of the directory in which the Streamlit scripts are stored is added to the PATH environment variable (Windows)._

# Main concepts
> **Key reference**: [Streamlit main concepts](https://docs.streamlit.io/get-started/fundamentals/main-concepts)

Write a few Streamlit commands into a normal Python script, then you run it with `streamlit run your_script.py [-- script args]` (`your_script.py` is a placeholder for your actual file). As soon as you run the script as shown above, a local Streamlit server gets initialised and your app opens in a new tab in your default web browser. The app is your canvas, where you can draw charts, text, widgets, tables, and more.

Streamlit commands decide what is added to the app. For example `st.text` writes raw text to your app, and `st.line_chart` draws a line chart, etc.. Refer to Streamlit's [API documentation](https://docs.streamlit.io/develop/api-reference) to see the available commands.