# Student Data Management Using Excel

This project reads student records, calculates grades and pass/fail status, filters passing students, and writes an organized Excel report.

## Studio setup

Create a **Windows Process** named `Student-Excel-Management` in this folder. Use Visual Basic expressions unless your Studio policy requires C#.

Install or confirm **UiPath.Excel.Activities 3.5.1** and **UiPath.System.Activities 26.10.0**.

## Files and paths

- Input: `Input\StudentRecords.csv`
- Output: `Output\StudentReport.xlsx`
- Main workflow: `Main.xaml` (created and saved by Studio)

Use relative expressions:

```vb
inputCsv = Path.Combine(Environment.CurrentDirectory, "Input", "StudentRecords.csv")
outputExcel = Path.Combine(Environment.CurrentDirectory, "Output", "StudentReport.xlsx")
```

## Variables

| Name | Type | Default |
|---|---|---|
| `inputCsv` | `String` | `Path.Combine(Environment.CurrentDirectory, "Input", "StudentRecords.csv")` |
| `outputExcel` | `String` | `Path.Combine(Environment.CurrentDirectory, "Output", "StudentReport.xlsx")` |
| `dtStudents` | `System.Data.DataTable` | empty |
| `dtPassing` | `System.Data.DataTable` | empty |
| `studentName` | `String` | empty |
| `marks` | `Int32` | `0` |
| `grade` | `String` | empty |
| `status` | `String` | empty |

## Activity sequence

1. Add **Try Catch** as the root activity.
2. In `Try`, add **Use Excel File** with `ExcelFile` set to `outputExcel` and enable **Create if not exists** if shown.
3. Read the CSV with the compatible CSV activity shown in your installed Activities panel. If no CSV reader is available, create `Input\StudentRecords.xlsx` in Excel by importing the unchanged CSV, then use **Read Range** with headers. Do not invent an activity or edit XAML.
4. Add `Grade` and `Status` columns to `dtStudents` before the loop. Use the DataTable column activity available in Studio, or two **Add Data Column** activities if that exact System activity is present. Set column names to `Grade` and `Status`, both with type `String`.
5. Add **For Each Excel Row** or **For Each Row in Data Table**, depending on the exact activity shown in Studio. Use `dtStudents` as the source.
5. Inside the loop, add **Assign** activities:
   - `marks = Convert.ToInt32(CurrentRow("Marks").ToString)`
   - `grade = If(marks >= 90, "A", If(marks >= 75, "B", If(marks >= 60, "C", If(marks >= 50, "D", "F"))))`
   - `status = If(marks >= 50, "Pass", "Fail")`
   - `CurrentRow("Grade") = grade`
   - `CurrentRow("Status") = status`
6. Add **If** with condition `marks >= 50`. In Then, log `"Passed: " & CurrentRow("StudentName").ToString`; in Else, log the failed student.
7. Use **Filter Data Table** with `dtStudents`, filter `Marks >= 50`, and output `dtPassing`. Confirm the filter syntax in the activity designer.
8. Add **Write Range** inside the Excel scope. Write `dtStudents` to `All Students` and `dtPassing` to `Passing Students`, both starting at `A1` with headers enabled.
9. In `Catch`, add **Log Message** at Error level with `exception.ToString`.
10. In `Finally`, log `Student Excel processing finished.`

### Calculation rules

- 90–100: A
- 75–89: B
- 60–74: C
- 50–59: D
- Below 50: F and Fail
- `Marks >= 50` means Pass; otherwise Fail.

## Run and verify

1. Save `Main.xaml` from Studio.
2. Ensure `Output` exists and is empty or contains only prior test results.
3. Run from Studio with **Debug File** or **Run**.
4. Verify the Output panel contains the pass/fail log messages and no unhandled exception.
5. Open `Output\StudentReport.xlsx`. Expected grades are S001=B, S002=C, S003=D, S004=A, and S005=F. `Passing Students` must contain S001–S004; S005 must be excluded.

Do not mark this project as tested until the workflow has executed successfully in Studio and the workbook has been inspected.
