# QA Test Cases

Manual functional test cases for the JSON & XML formatter. Run in a modern browser with the page open from `index.html`. The Tailwind stylesheet is loaded from a CDN, so an internet connection is needed for expected styling.

## Smoke

Run these checks to confirm the main user workflows work.

| ID | Test | Steps | Expected result |
|---|---|---|---|
| SMK-01 | Initial page | Open `index.html`. | Input and output panels load; input is empty; Repair and Convert are hidden. |
| SMK-02 | Format JSON | Select **JSON**, enter `{"name":"Ada","active":true}`, click **Format / Prettify**. | Output contains indented, valid JSON. Status is **Valid JSON**. |
| SMK-03 | Format XML | Select **XML**, enter `<root><name>Ada</name></root>`, click **Format / Prettify**. | Output contains valid, indented XML. Status is **Valid XML**. |
| SMK-04 | Convert JSON to XML | Select **JSON**, enter `{"person":{"name":"Ada"}}`. | **Convert to XML** appears. Click it. Output is valid XML with a `person` root and a `name` child. |
| SMK-05 | Convert XML to JSON | Select **XML**, enter `<person><name>Ada</name></person>`. | **Convert to JSON** appears. Click it. Output is JSON representing `person.name` as `"Ada"`. |
| SMK-06 | Minify and copy output | Format a multi-line JSON object, click **Minify**, then **Copy Output**. | Output becomes compact valid JSON; input is unchanged. Copy Output copies the compact result. |
| SMK-07 | Repair common JSON | Enter `{name: 'Ada',}`. | Repair appears. Click **Repair**. Output is valid JSON with the key and string quoted; input remains unchanged. |
| SMK-08 | Repair incomplete XML | Select **XML**, enter `<root><item>value</root>`. | Repair appears with the first issue highlighted. Click **Repair**. Output is well-formed XML with the missing `item` close tag inserted; input remains unchanged. |
| SMK-09 | Load sample and convert | Click **Load Sample**, then **Convert to XML**. | Sample JSON loads and formats. Conversion succeeds; output is valid XML and escapes the ampersand in “JSON & XML Formatter.” |

## Regression

Run these checks after changes to validation, repair, formatting, conversion, or the editor.

| ID | Test | Steps | Expected result |
|---|---|---|---|
| REG-01 | Auto-detect JSON and XML | With **Auto-detect** selected, enter valid JSON, then valid XML. | JSON is treated as JSON; XML is treated as XML. The available conversion action matches the detected format. |
| REG-02 | Explicit format overrides detection | Enter valid JSON with **XML** selected, then valid XML with **JSON** selected. | Validation and available actions follow the selected format. Mismatched or invalid input does not offer Convert. |
| REG-03 | Repair visibility and diagnostics | Try empty input, valid JSON, invalid JSON, valid XML, and invalid XML; change the format selector while input remains. | Repair is visible only for non-empty invalid input. The first identifiable error row is highlighted and the input-side description updates to that issue; valid or empty input clears both. |
| REG-04 | JSON repair coverage | Test unquoted keys, single-quoted strings, a missing comma between properties, a missing comma between array values (`[1 2]`), a trailing comma, and an unfinished object or array. | Supported issues are repaired into valid JSON and reported. `[1 2]` is repaired as `[1, 2]`. The source input remains unchanged. |
| REG-05 | Unsafe JSON repair rejection | Try `{"a":}`, `{"a":1} trailing`, and `{"a":"\q"}`. | Repair fails clearly; no invalid or stale result is presented as a successful repair. The input-side diagnostic uses a concise line-based explanation, not a raw browser parser error. |
| REG-06 | XML repair safety | Test missing and mismatched closing tags, then an unclosed quoted attribute, unclosed comment, and unmatched closing tag. | Missing/mismatched closing tags are repaired where supported. Malformed constructs without a safe repair fail with a visible error; source input is unchanged. |
| REG-07 | JSON output stays valid after XML escaping | Convert JSON containing `&`, `<`, `>`, quotes, and apostrophes in text and attribute values. | Output parses as XML and parsed values match the original JSON values. |
| REG-08 | XML attributes round-trip | Convert `<root><item id="7">value</item></root>` to JSON, then convert that JSON back to XML. | JSON retains `id` and `value`; resulting XML retains the `root` element, `item` element, attribute, and text. |
| REG-09 | XML mixed content round-trip | Convert `<p>Hello <b>world</b> !</p>` to JSON and back. | JSON uses ordered `#content`; reconverted XML keeps text and child-element order and preserves the separating spaces. |
| REG-10 | Repeated and interleaved XML children | Convert `<root><a>1</a><a>2</a></root>` and `<root><a>1</a><b>2</b><a>3</a></root>` to JSON and back. | Repeated names are represented without losing occurrences. Interleaved child order is retained in `#content`. |
| REG-11 | CDATA formatting and conversion | Format and convert `<root><![CDATA[a < b & c]]></root>`. Also test CDATA inside mixed content. | Formatting keeps the CDATA content/section. Conversion represents it as `#cdata` and reconversion retains its content and position. |
| REG-12 | Mixed-content formatting preserves text | Format `<p>a<b>b</b> <i>c</i></p>`. | Output remains valid XML and its parsed text content remains exactly `ab c` (no inserted indentation or trimmed separator). |
| REG-13 | Mixed-content minification preserves text | Put `<p>a<b>b</b> <i>c</i></p>` in output and click **Minify**. | Output stays valid XML; parsed text content remains exactly `ab c`. |
| REG-14 | Element-only XML minification | Minify `<root>\n  <item>value</item>\n</root>`. | Formatting-only indentation between elements is removed; output parses as XML and retains element/text values. |
| REG-15 | Invalid XML is not minified | Put malformed XML in output and click **Minify**. | Minification fails with a visible error; invalid XML is not reported as successfully minified. |
| REG-16 | Clear resets input diagnostics | Enter invalid input, then click **Clear**. | Input and output clear, error description and highlight disappear, Repair and Convert hide, and status returns to **Ready**. |
| REG-17 | Repeated actions do not reuse stale output | Produce a successful output, then convert valid JSON containing an invalid XML element name, such as `{"root":{"not valid":"x"}}`. Also verify a failed repair after a successful output. | Failure is shown explicitly, the output is cleared, and prior output is not mistaken for the failed action’s result. |
| REG-18 | Output minify with no result | Leave output empty and click **Minify**. | A clear “no output” message is shown; the app does not claim success. |
| REG-19 | Copy output with empty/non-empty result | Click **Copy Output** when empty, then with output present (grant clipboard permission if prompted). | Empty output is a no-op. Non-empty output copies successfully and the UI gives copy feedback. |
| REG-20 | Long input and editor synchronization | Enter content spanning many lines, scroll the input, and trigger validation errors near the bottom. | The highlight stays aligned with the corresponding input line while scrolling; editor remains editable and selectable. |

## Execution record

For each run, record browser/version, date, tester, and each case as **Pass**, **Fail**, or **Blocked**. Include the input, actual output/status, and a screenshot or console error for failures.
