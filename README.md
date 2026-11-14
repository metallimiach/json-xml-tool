# DataFormatter.dev

A lightweight, browser-based tool for formatting, validating, repairing, minifying, and converting JSON and XML.

## Features

- Auto-detect JSON or XML, or select a format manually.
- Format valid input and show validation errors inline, with the first error line highlighted when available.
- Repair common JSON syntax issues and recover supported XML tag-closing errors. Repairs are written to the output; the original input is kept.
- Convert JSON to XML or XML to JSON. The conversion button appears when the selected or detected input is valid.
- Minify the current output and copy it to the clipboard.
- Load a sample or clear the input.

XML conversion uses a JSON representation for XML features that do not map directly to JSON:

- `@attributes` stores element attributes.
- `#text` stores text when it appears alongside attributes.
- `#content` preserves the order of mixed text, child elements, and CDATA sections.
- `#cdata` identifies CDATA content inside `#content`.

The converter infers the XML root name from a single top-level JSON property; otherwise, it uses `root`.

## Run locally

Open `index.html` in a modern browser. No build step or package installation is required. Tailwind CSS is loaded from a CDN, so the page needs an internet connection to load its styles.

JSON/XML parsing, formatting, repair, and conversion run in the browser. The app does not send the data being processed to a server.

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE).
