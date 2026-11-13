# Integrated Student Administration Automation

This Studio-ready project coordinates browser collection, Excel processing, Word generation, and PowerPoint generation through child workflows.

## Architecture

`Main.xaml` is the orchestrator:

1. Initialize relative paths and a shared `DataTable`.
2. Invoke `BrowserCollection.xaml` and receive `BrowserSummary`.
3. Invoke `ExcelProcessing.xaml` with the input CSV and receive `StudentTable` and `PassingTable`.
4. Invoke `WordReport.xaml` with `StudentTable`, `BrowserSummary`, and an output path.
5. Invoke `PowerPointSummary.xaml` with `StudentTable`, `PassingTable`, and an output path.
6. Log completion and output locations.

Use separate child workflows created through Studio’s **New Workflow** command. Do not hand-author invocation XML.

## Packages

- `UiPath.UIAutomation.Activities` 26.10.5
- `UiPath.Excel.Activities` 3.5.1
- `UiPath.Word.Activities` 2.5.1
- `UiPath.Presentations.Activities` 2.6.0-preview
- `UiPath.System.Activities` 26.10.0

## Main variables and arguments

| Name | Type | Direction |
|---|---|---|
| `inputFolder` | `String` | variable |
| `outputFolder` | `String` | variable |
| `inputCsv` | `String` | variable |
| `targetUrl` | `String` | variable |
| `studentTable` | `System.Data.DataTable` | variable |
| `passingTable` | `System.Data.DataTable` | variable |
| `browserSummary` | `String` | variable |
| `wordOutput` | `String` | variable |
| `presentationOutput` | `String` | variable |

Required child-workflow arguments:

- BrowserCollection.xaml: `in_TargetUrl As String` (In), `out_BrowserSummary As String` (Out)
- ExcelProcessing.xaml: `in_InputCsv As String` (In), `out_StudentTable As DataTable` (Out), `out_PassingTable As DataTable` (Out)
- WordReport.xaml: `in_StudentTable As DataTable` (In), `in_BrowserSummary As String` (In), `in_OutputPath As String` (In)
- PowerPointSummary.xaml: `in_StudentTable As DataTable` (In), `in_PassingTable As DataTable` (In), `in_OutputPath As String` (In)

Shared paths:

- `inputCsv = Path.Combine(Environment.CurrentDirectory, "Input", "StudentAdministration.csv")`
- `outputFolder = Path.Combine(Environment.CurrentDirectory, "Output")`
- Excel output: `Path.Combine(outputFolder, "StudentReport.xlsx")`
- Word output: `Path.Combine(outputFolder, "StudentReport.docx")`
- PowerPoint output: `Path.Combine(outputFolder, "StudentSummary.pptx")`
- Browser output: `Path.Combine(outputFolder, "BrowserResult.txt")`

### Invoke Workflow File mappings

In Main.xaml, use **Invoke Workflow File** and Studio’s Import Arguments UI:

| Child | Child argument | Main expression |
|---|---|---|
| BrowserCollection.xaml | `in_TargetUrl` | `targetUrl` |
| BrowserCollection.xaml | `out_BrowserSummary` | `browserSummary` |
| ExcelProcessing.xaml | `in_InputCsv` | `inputCsv` |
| ExcelProcessing.xaml | `out_StudentTable` | `studentTable` |
| ExcelProcessing.xaml | `out_PassingTable` | `passingTable` |
| WordReport.xaml | `in_StudentTable` | `studentTable` |
| WordReport.xaml | `in_BrowserSummary` | `browserSummary` |
| WordReport.xaml | `in_OutputPath` | `wordOutput` |
| PowerPointSummary.xaml | `in_StudentTable` | `studentTable` |
| PowerPointSummary.xaml | `in_PassingTable` | `passingTable` |
| PowerPointSummary.xaml | `in_OutputPath` | `presentationOutput` |

## Main activity sequence

1. Root **Try Catch**.
2. Use **Assign** for paths based on `Environment.CurrentDirectory`.
3. Add **Invoke Workflow File** for the browser child and map arguments in the Import Arguments dialog.
4. Add **Invoke Workflow File** for the Excel child and map the two output DataTables.
5. Add **Invoke Workflow File** for the Word child and map the shared data plus output path.
6. Add **Invoke Workflow File** for the PowerPoint child and map the shared data plus output path.
7. Add **Log Message** for each completed stage and final output path.
8. In Catch, log `exception.ToString` at Error level. Use Finally for a final lifecycle log.

## Independent child testing

Run and verify BrowserCollection, ExcelProcessing, WordReport, and PowerPointSummary separately before creating Main.xaml. Confirm each child’s output file or output argument, then run Main.xaml in this order: Browser → Excel → Word → PowerPoint. Add an Info-level log before and after every invocation and an Error-level log in the Main Catch block.

## Child workflows

- `BrowserCollection.xaml`: Use Application/Browser → Navigate To → Get Text → Log Message.
- `ExcelProcessing.xaml`: Read source data → For Each Row/Excel Row → Assign grade/status → If → Filter Data Table → output DataTables → Log Message.
- `WordReport.xaml`: Use Word File → Write Text/Replace Text → save → Log Message.
- `PowerPointSummary.xaml`: Presentation document scope → Add Slide → Add Text to Slide → optional Insert Image → save → Log Message.

## Run and verify

Create every child workflow and argument in Studio first, then create `Main.xaml` and use Studio’s argument-mapping UI. Run the integrated project only after each child has been executed independently. Verify the browser log, Excel workbook, Word document, and PowerPoint file under `Output`. This project remains Studio-ready and untested until the complete sequence executes successfully.
