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

{/* Part 2 (Lisr-iiil): short rows and <th>-only group rows */}