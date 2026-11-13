# Automated PowerPoint Presentation Generator

This Studio-ready project creates a presentation from structured slide data.

## Package

Confirm `UiPath.Presentations.Activities` 2.6.0-preview is installed and visible in Studio. This is a preview package. Do not assume activity names, properties, layouts, or save behavior from another version.

## Files

- Input: `Input\PresentationData.csv`
- Optional image: `Input\automation-image.png`
- Output: `Output\UiPath-Student-Overview.pptx`
- Studio-created files: `Main.xaml`, `project.json`

## Variables

| Name | Type | Value |
|---|---|---|
| `inputCsv` | `String` | `Path.Combine(Environment.CurrentDirectory, "Input", "PresentationData.csv")` |
| `outputPptx` | `String` | `Path.Combine(Environment.CurrentDirectory, "Output", "UiPath-Student-Overview.pptx")` |
| `imagePath` | `String` | `Path.Combine(Environment.CurrentDirectory, "Input", "automation-image.png")` |
| `dtSlides` | `System.Data.DataTable` | empty |
| `slideTitle` | `String` | empty |
| `slideBody` | `String` | empty |

## Activity sequence

1. Root **Try Catch** with Error-level **Log Message** in Catch.
2. Read the CSV using the compatible CSV activity shown in Studio, or import it into Excel and use **Use Excel File** + **Read Range**. Output to `dtSlides`.
3. Search Activities for the installed presentation document/container activity. Select only the activity shown by Studio and configure its output path using the properties displayed there.
4. Search for the package’s slide-creation activity and create one title slide using the layout offered in Studio.
5. Search for the package’s text activity and add title/body text using the properties displayed by the designer.
6. Add **For Each Row in Data Table** around the content-slide creation logic. For each row, assign title/body and add a slide.
7. Search for the package’s image activity and use it only when `File.Exists(imagePath)` is true; otherwise log a Warning and continue.
8. Save through the presentation container or the exact save activity shown in Studio.
9. Log the output path after saving.

## Run and verify

Run `Main.xaml` in Studio. Open the generated PPTX and verify a title slide plus one slide per CSV row. Record the exact activity labels and properties selected in Studio in this README after implementation. If an expected capability is unavailable, stop and document the genuine alternative rather than inventing XML.
