# Automated Invoice Generator Using Word

This Studio-ready project reads invoice data from Excel and creates a formatted Word invoice for each record.

## Packages to confirm in Studio

- `UiPath.Word.Activities` 2.5.1 — Word document activities
- `UiPath.Excel.Activities` 3.5.1 — Excel input activities
- `UiPath.System.Activities` 26.10.0 — variables, loops, Try Catch, logging

Use the exact activity labels surfaced by the installed Activities panel. Do not hand-edit XAML.

## Files

- Input: `Input\InvoiceData.csv`
- Output: `Output\Invoices\Invoice_<InvoiceNumber>.docx`
- Studio-created files: `Main.xaml`, `project.json`

## Variables

| Name | Type | Value |
|---|---|---|
| `inputCsv` | `String` | `Path.Combine(Environment.CurrentDirectory, "Input", "InvoiceData.csv")` |
| `inputWorkbook` | `String` | `Path.Combine(Environment.CurrentDirectory, "Input", "InvoiceData.xlsx")` only if CSV reading is unavailable |
| `outputFolder` | `String` | `Path.Combine(Environment.CurrentDirectory, "Output", "Invoices")` |
| `dtInvoices` | `System.Data.DataTable` | empty |
| `invoiceNumber` | `String` | empty |
| `customerName` | `String` | empty |
| `course` | `String` | empty |
| `amount` | `String` | empty |
| `dueDate` | `String` | empty |
| `invoicePath` | `String` | empty |

## Activity sequence

1. Root **Try Catch**.
2. Use the installed CSV-reading activity if it is available in Studio, set its path to `inputCsv`, output `dtInvoices`, and enable headers.
3. If no compatible CSV activity is available, import the unchanged CSV into Excel as `Input\InvoiceData.xlsx`, then use **Use Excel File** and **Read Range** with headers.
4. Add **For Each Row in Data Table** with `dtInvoices`.
5. Assign fields from `CurrentRow("InvoiceNumber")`, `CustomerName`, `Course`, `Amount`, and `DueDate` to the string variables.
6. Assign `invoicePath = Path.Combine(outputFolder, "Invoice_" & invoiceNumber & ".docx")`.
7. Add **Use Word File** with `invoicePath` and enable creation of a new document when offered.
8. Add **Write Text** activities for the invoice title, customer, course, amount, and due date. Use a Word table or separate paragraphs for readable formatting.
9. Add **Replace Text** for a placeholder such as `{{INVOICE_NUMBER}}` if a Word template is used.
10. Save through the Word scope's document save behavior or the exact **Save Document** activity shown in the installed package.
11. Add **Log Message** after each invoice: `"Created invoice: " & invoicePath`.
12. In Catch, log `exception.ToString` at Error level. In Finally, log completion.

## Run and verify

### Invoice calculation and naming

The sample contains one line item per invoice, so `Subtotal = Amount`, `Tax = 0`, and `Total = Subtotal + Tax`. If tax is later introduced, use `Tax = Subtotal * TaxRate` and `Total = Subtotal + Tax`. The required filename is `Invoice_<InvoiceNumber>.docx`, for example `Invoice_INV-1001.docx`.

Run `Main.xaml`. Confirm one DOCX per row under `Output\Invoices`, and verify the title, customer, course, amount, due date, subtotal, tax, and total. Do not mark PASS until Studio execution succeeds.
