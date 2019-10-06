---
title: "Use editable extension of Bootstrap Table in Bootstrap v4"
date: 2019-10-06T07:43:40+08:00
categories: [BootstrapTable]
---

Since x-editable has not been updated for a long time, so [Bootstrap v4 is not supported](https://github.com/wenzhixin/bootstrap-table/issues/3705), but many people ask how to use editable in v4 of Bootstrap Table. Here we have tried on how to use it.

You can see that someone has submitted PR and added support for Bootstrap v4. Here we use [https://github.com/Talv/x-editable/tree/develop](https://github.com/Talv/x-editable/tree/develop) directly. Since it is not published to npm, we can only use the link on GitHub directly, here we use the [jsdelivr](https://www.jsdelivr.com) tool. The files we need are in this directory [bootstrap4-editable] (https://cdn.jsdelivr.net/gh/Talv/x-editable@develop/dist/bootstrap4-editable/).

The files that need to be imported include:

* bootstrap4-editable/css/bootstrap-editable.css
* bootstrap4-editable/js/bootstrap-editable.min.js
* extensions/editable/bootstrap-table-editable.min.js

```html
<link href="https://cdn.jsdelivr.net/gh/Talv/x-editable@develop/dist/bootstrap4-editable/css/bootstrap-editable.css" rel="stylesheet">
<link href="https://unpkg.com/bootstrap-table@1.15.4/dist/bootstrap-table.min.css" rel="stylesheet">

<script src="https://cdn.jsdelivr.net/gh/Talv/x-editable@develop/dist/bootstrap4-editable/js/bootstrap-editable.min.js"></script>
<script src="https://unpkg.com/bootstrap-table@1.15.4/dist/bootstrap-table.min.js"></script>
<script src="https://unpkg.com/bootstrap-table@1.15.4/dist/extensions/editable/bootstrap-table-editable.min.js"></script>

<table id="table"
  data-pagination="true"
  data-show-export="true"
  data-url="json/data1.json">
  <thead>
    <tr>
      <th data-field="id">ID</th>
      <th data-field="name" data-editable="true">Item Name</th>
      <th data-field="price" data-editable="true">Item Price</th>
    </tr>
  </thead>
</table>

<script>
  $(function() {
    $('#table').bootstrapTable()
  })
</script>
```

Online example: https://live.bootstrap-table.com/code/wenzhixin/854

It can be seen that it is now working well.
