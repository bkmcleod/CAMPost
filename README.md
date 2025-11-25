<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Text Transformer</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      margin: 0;
      padding: 1.5rem;
      max-width: 960px;
    }
    h1 {
      font-size: 1.5rem;
      margin-bottom: 0.5rem;
    }
    .layout {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 1rem;
      margin-top: 1rem;
    }
    textarea {
      width: 100%;
      min-height: 260px;
      box-sizing: border-box;
      padding: 0.5rem;
      font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
      font-size: 0.9rem;
      resize: vertical;
    }
    .controls {
      margin-top: 0.75rem;
      display: flex;
      flex-wrap: wrap;
      gap: 0.75rem 1.5rem;
      align-items: center;
    }
    .checkbox-group {
      display: flex;
      flex-direction: column;
      gap: 0.35rem;
      margin-top: 0.75rem;
    }
    label {
      font-size: 0.85rem;
      display: inline-flex;
      align-items: center;
      gap: 0.35rem;
      cursor: pointer;
    }
    button {
      padding: 0.4rem 0.9rem;
      border-radius: 4px;
      border: 1px solid #555;
      background: #f2f2f2;
      font-size: 0.9rem;
      cursor: pointer;
    }
    button:hover {
      filter: brightness(0.96);
    }
    .small {
      font-size: 0.75rem;
      color: #666;
    }
    @media (max-width: 800px) {
      .layout {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>
<body>
  <h1>Text Transformer</h1>
  <p class="small">
    Paste text on the left, choose transformations, then click "Transform".
  </p>

  <div class="layout">
    <div>
      <h2 class="small">Input</h2>
      <textarea id="inputText" placeholder="Paste your text here"></textarea>
    </div>
    <div>
      <h2 class="small">Output</h2>
      <textarea id="outputText" readonly placeholder="Result appears here"></textarea>
    </div>
  </div>

  <div class="checkbox-group">
    <label>
      <input type="checkbox" id="cbTrim" checked />
      Trim leading/trailing whitespace
    </label>
    <label>
      <input type="checkbox" id="cbCollapseSpaces" />
      Collapse multiple spaces to a single space
    </label>
    <label>
      <input type="checkbox" id="cbNormalizeNewlines" checked />
      Normalize Windows/Mac newlines to "\n"
    </label>
    <label>
      <input type="checkbox" id="cbUppercase" />
      Convert to UPPERCASE
    </label>
    <label>
      <input type="checkbox" id="cbRemoveBlankLines" />
      Remove completely blank lines
    </label>
    <!-- Add more checkboxes here as needed -->
  </div>

  <div class="controls">
    <button id="btnTransform">Transform</button>
    <button id="btnCopy">Copy output</button>
    <span class="small" id="status"></span>
  </div>

  <script>
    // Core transformation pipeline definition.
    // Each entry: { id: 'checkboxId', fn: text => newText }
    const TRANSFORMS = [
      {
        id: "cbTrim",
        fn: text => text.trim()
      },
      {
        id: "cbCollapseSpaces",
        fn: text => text.replace(/ {2,}/g, " ")
      },
      {
        id: "cbNormalizeNewlines",
        fn: text => text.replace(/\r\n?/g, "\n")
      },
      {
        id: "cbUppercase",
        fn: text => text.toUpperCase()
      },
      {
        id: "cbRemoveBlankLines",
        fn: text => text
          .split(/\r?\n/)
          .filter(line => line.trim() !== "")
          .join("\n")
      }
      // Add more transformation objects here
    ];

    const inputEl = document.getElementById("inputText");
    const outputEl = document.getElementById("outputText");
    const statusEl = document.getElementById("status");
    const btnTransform = document.getElementById("btnTransform");
    const btnCopy = document.getElementById("btnCopy");

    function runPipeline() {
      let text = inputEl.value;

      for (const t of TRANSFORMS) {
        const checkbox = document.getElementById(t.id);
        if (checkbox && checkbox.checked) {
          text = t.fn(text);
        }
      }

      outputEl.value = text;
      statusEl.textContent = "Transformed at " + new Date().toLocaleTimeString();
    }

    btnTransform.addEventListener("click", runPipeline);

    btnCopy.addEventListener("click", async () => {
      try {
        await navigator.clipboard.writeText(outputEl.value);
        statusEl.textContent = "Output copied to clipboard";
      } catch (err) {
        statusEl.textContent = "Copy failed (browser permissions)";
      }
    });

    // Optional: auto-run when input changes
    // inputEl.addEventListener("input", runPipeline);
  </script>
</body>
</html>
