# UIWaves

Rule-based natural language table SDK that runs fully in the browser without AI APIs.

## Features

- Framework agnostic, plain TypeScript
- Floating assistant UI with prompt input
- Rule-based prompt parser with synonym support
- Command registry based architecture
- Intent validation before execution
- Undo and redo support
- HTML table adapter as single DOM mutation layer
- Event bus for extensibility
- ESM, UMD, and IIFE builds

## Install

```bash
npm install uiwavesbot
```

The package runs in the browser and does not require an AI API key.

## Publish

Build, test, and publish from this package directory:

```bash
npm login
npm version patch
npm publish
```

The `prepublishOnly` script automatically runs lint, tests, and the production
build before publishing. Only the generated `dist` directory is included in the
published package.

## Build

```bash
npm run build
```

Build artifacts:

- dist/uiwaves.es.js
- dist/uiwaves.umd.js
- dist/uiwaves.min.js
- dist/index.d.ts

## Usage

### ESM or CommonJS

```ts
import { UIWaves } from "uiwavesbot";

const instance = UIWaves.init({
  target: "#paymentsTable", // you table with id
  theme: "light"
});
```

For CommonJS projects:

```js
const { UIWaves } = require("uiwavesbot");
```

### React

Initialize UIWaves after the table is mounted and destroy the instance when the
component is removed:

```tsx
import { useEffect } from "react";
import { UIWaves } from "uiwavesbot";

export function PaymentsTable() {
  useEffect(() => {
    const instance = UIWaves.init({
      target: "#paymentsTable",
      theme: "light",
      position: "bottom-right",
      enableQuickColumnActions: true
    });

    return () => instance.destroy();
  }, []);

  return (
    <table id="paymentsTable">
      <thead>
        <tr>
          <th>Bank</th>
          <th>Amount</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>ICICI</td>
          <td>120000</td>
          <td>Failed</td>
        </tr>
      </tbody>
    </table>
  );
}
```

### Angular

Use the table element id in the template and initialize UIWaves in
`ngAfterViewInit` so the table is available in the DOM:

```ts
import { AfterViewInit, Component, OnDestroy } from "@angular/core";
import { UIWaves } from "uiwavesbot";

@Component({
  selector: "app-payments-table",
  standalone: true,
  template: `
    <table id="paymentsTable">
      <thead>
        <tr>
          <th>Bank</th>
          <th>Amount</th>
          <th>Status</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>ICICI</td>
          <td>120000</td>
          <td>Failed</td>
        </tr>
      </tbody>
    </table>
  `
})
export class PaymentsTableComponent implements AfterViewInit, OnDestroy {
  private instance?: UIWaves;

  ngAfterViewInit(): void {
    this.instance = UIWaves.init({
      target: "#paymentsTable",
      theme: "light",
      position: "bottom-right",
      enableQuickColumnActions: true
    });
  }

  ngOnDestroy(): void {
    this.instance?.destroy();
  }
}
```

### Browser script

Use with jsDelivr CDN to integrate in your app:

```html
<script src="https://cdn.jsdelivr.net/npm/uiwavesbot@0.3.2/dist/uiwaves.min.js"></script>
<script>
  const instance = window.UIWaves.init({ target: "#paymentsTable" }); //you table with id

  instance.on("afterCommand", ({ action, prompt }) => {
    console.log(`Executed ${action}: ${prompt}`);
  });
</script>
```

## Supported Commands

- Remove Amount column
- Delete Amount column
- Hide Status
- Show Bank
- Sort by Amount descending
- Group by Status
- Filter Amount greater than 10000
- Highlight Failed rows
- Search ICICI
- Rename Amount to Total
- Set Amount width 20rem
- Align Amount content to right
- Add tooltip on Amount
- Freeze first column
- Reset table

## Architecture

- parser/PromptParser.ts defines stable parser interface
- parser/RegexParser.ts is current local rule-based parser
- core/Engine.ts orchestrates parsing, validation, and command execution
- adapter/HtmlTableAdapter.ts is the only place where DOM updates occur
- commands/* provide undoable command units

This allows replacing RegexParser with a future LLM parser without changing adapter, commands, or engine contracts.

## Development

```bash
npm run lint
npm run test
npm run build
```

## Example

Open examples/basic.html after running build.
