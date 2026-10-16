---
description: "TODO (Lisr-iiil): SEO description, under 160 characters"
id: read-table-rows
title: Read table rows
sidebar_label: Read table rows
---

# Read table rows

Read an HTML table as data instead of walking its cells by hand. Call `rows()` on a
locator that points at the `<table>`, and you get back one map per row, keyed by
column header:

   <!-- Part 1 (May): snippets: read rows, find a row by column value, assert on a cell -->

   <!-- Part 2 (Lisr-iiil): short rows and <th>-only group rows -->