---
description: "TODO (Lisr-iiil): SEO description, under 160 characters"
id: read-table-rows
title: Read table rows
sidebar_label: Read table rows
---

# Read table rows

Read an HTML table as data instead of walking its cells by hand. Call `rows()` on a
locator that points at the `<table>`, and you get back one map per row, keyed by
column header.

{/* Part 1 (May): snippets: read rows, find a row by column value, assert on a cell */}

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

{/* Part 2 (Lisr-iiil): short rows and <th>-only group rows */}

## Short rows

When a data row has fewer `<td>` cells than there are headers, `rows()` still
includes every header key. Missing trailing cells have the value `""`, not `null`.
For example, replace the table's `<tbody>` with:

```html
<tbody>
  <tr><td>Smith</td><td>John</td></tr>
  <tr><td>Doe</td><td>Jason</td><td>$100.00</td></tr>
</tbody>
```

```java
List<Map<String, String>> rows = $("#table1").rows();
Map<String, String> smith = rows.get(0);
Assert.assertTrue(smith.containsKey("Due"));
Assert.assertEquals(smith.get("Due"), "");
```

Cells are matched to headers by position. Omitting a middle `<td>` shifts the
remaining values to earlier columns; use an empty `<td></td>` to preserve that
column's position. Cells beyond the number of headers are ignored.
