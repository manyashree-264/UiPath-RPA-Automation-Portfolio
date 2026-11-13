# Browser-Based Information Collection

This Studio-ready project opens a public demonstration website, extracts visible text, and writes a timestamped text result.

## Package

Confirm `UiPath.UIAutomation.Activities` 26.10.5 and `UiPath.System.Activities` 26.10.0 in Studio. Use the modern UI automation activities surfaced by the Activities panel.

## Files

- Input: `Input\BrowserConfig.txt`
- Output: `Output\BrowserResult.txt`
- Studio-created files: `Main.xaml`, `project.json`

## Variables

| Name | Type | Value |
|---|---|---|
| `targetUrl` | `String` | `"https://example.com"` |
| `pageHeading` | `String` | empty |
| `pageDescription` | `String` | empty |
| `outputPath` | `String` | `Path.Combine(Environment.CurrentDirectory, "Output", "BrowserResult.txt")` |

## Activity sequence

1. Root **Try Catch** with Error-level **Log Message** in Catch.
2. Add **Use Application/Browser**. Indicate a browser window and set its URL to `targetUrl`, or use the modern browser targeter to attach to the opened page.
3. In the Activities panel, search for navigation using `Navigate`, `Go To URL`, and `Browser` terms. Select the exact modern navigation activity available in UIAutomation 26.10.5 and set its URL to `targetUrl`. If the URL is configured directly on Use Application/Browser, no separate navigation activity is needed.
4. Add **Get Text** targeting the page heading and save to `pageHeading`.
5. Add a second **Get Text** targeting the introductory paragraph and save to `pageDescription`.
6. Add **Log Message** entries for both extracted values.
7. Add a text-writing activity available in the installed System package to write `pageHeading` and `pageDescription` to `outputPath`. If using a file-writing activity, confirm the exact label in Activities search before saving.
8. Do not add **Click** or **Type Into** for the initial `https://example.com` test because the page requires neither. Add them only for a later public demonstration page that genuinely requires interaction, using selectors captured by Studio.

## Run and verify

Run with an active internet connection and the browser extension enabled. Confirm the browser opens `example.com`, the Output panel logs extracted text, and `Output\BrowserResult.txt` contains the heading and paragraph. Selectors must be captured by Studio’s modern targeter, not hand-authored.
