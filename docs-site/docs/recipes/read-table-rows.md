---
description: "Read HTML table rows with Selenium Boot Locator.rows(), find rows by column value, assert cells, and handle short rows and group headings."
id: read-table-rows
title: Read table rows
sidebar_label: Read table rows
---

# Read table rows

Read an HTML table as data instead of walking its cells by hand. Call `rows()` on a
locator that points at the `<table>`, and you get back one map per row, keyed by
column header.

## Read, find, and assert on rows

Example table with three columns (Last Name, First Name, and Due) and two rows:
```html
<table id="table1">
  <thead>
    <tr><th>Last Name</th><th>First Name</th><th>Due</th></tr>
  </thead>
  <tbody>
    <tr><td>Smith</td><td>John</td><td>$50.00</td></tr>
    <tr><td>Doe</td><td>Jason</td><td>$100.00</td></tr>
  </tbody>
</table>
```

```java
List<Map<String, String>> rows = $("#table1").rows();
// [{Last Name=Smith, First Name=John, Due=$50.00},
//  {Last Name=Doe,   First Name=Jason, Due=$100.00}]
```

`rows()` returns a plain `List`, so finding a row by a column's value is ordinary Java:

```java
Map<String, String> doe = rows.stream()
        .filter(row -> row.get("Last Name").equals("Doe"))
        .findFirst()
        .orElseThrow();
// {Last Name=Doe, First Name=Jason, Due=$100.00}
```

Then assert on a cell by its column header:

```java
import org.testng.Assert;

// Check Jason Doe's due amount
Assert.assertEquals(doe.get("Due"), "$100.00");
// fails with: expected [$100.00] but found [$90.00] if the cell says $90.00
```

The examples below use variations of the same `#table1` table. Keep its `<thead>`
and three column headers unchanged, and replace only the `<tbody>` as shown in
each section. Read the rows again after loading each variation; each Java block
is a separate example rather than additional statements in the same method.

## Short rows

When a data row has fewer `<td>` cells than there are headers, `rows()` still
includes every header key. Missing trailing cells have the value `""`, not `null`.

In the HTML below, Smith's final `<td>` has been removed from the original example,
while Doe's row is unchanged. Replace only the original table's `<tbody>` with
this fragment; keep `<table id="table1">` and its `<thead>`:

```html
<tbody>
  <tr><td>Smith</td><td>John</td></tr>
  <tr><td>Doe</td><td>Jason</td><td>$100.00</td></tr>
</tbody>
```

After loading the modified table, use the same `$("#table1").rows()` call as in
the core example. Select Smith's row and assert both that the `Due` key exists
and that its value is an empty string. This distinguishes a missing cell value
from a missing map key:

```java
List<Map<String, String>> rows = $("#table1").rows();
Map<String, String> smith = rows.get(0);
// {Last Name=Smith, First Name=John, Due=}
Assert.assertTrue(smith.containsKey("Due"));
Assert.assertEquals(smith.get("Due"), "");
// Both assertions pass: the Due key is retained and its missing cell is mapped to "".
```

Cells are matched to headers by position. Omitting a middle `<td>` shifts the
remaining values to earlier columns; use an empty `<td></td>` to preserve that
column's position. Cells beyond the number of headers are ignored.

## Group rows containing only `<th>`

After the table header has been selected, rows with no `<td>` cells are skipped.
A `<th>`-only group heading does not become a data row or change the header keys:

Starting with the short-row variation above, add a group heading before Smith's
row. Its single `<th>` spans all three columns, and it has no `<td>` cells.
Keep the original `<table id="table1">` and `<thead>` again, replacing only
the `<tbody>` with:

```html
<tbody>
  <tr><th colspan="3">Outstanding accounts</th></tr>
  <tr><td>Smith</td><td>John</td></tr>
  <tr><td>Doe</td><td>Jason</td><td>$100.00</td></tr>
</tbody>
```

Load this table variation and read it with the same `rows()` call. Assert that
the result contains only the two data rows, in order, and that their names are
still available under the original `Last Name` header. The group heading must
not appear as an extra data row:

```java
List<Map<String, String>> rows = $("#table1").rows();
// [{Last Name=Smith, First Name=John, Due=},
//  {Last Name=Doe, First Name=Jason, Due=$100.00}]
Assert.assertEquals(rows.size(), 2);
Assert.assertEquals(rows.get(0).get("Last Name"), "Smith");
Assert.assertEquals(rows.get(1).get("Last Name"), "Doe");
// All three assertions pass: the group heading is skipped, leaving Smith and Doe in order.
```

Keep the column headers in `<thead>` as shown above. Without a `<thead>` header,
the first row containing `<th>` cells supplies the keys, so a group heading
before the column headers would be interpreted as the header instead.
