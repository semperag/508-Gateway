# 508 Compliance Gateway
Hi! 😃 Use this repo to find a quick solution to get rid of some pesky 508 compliance alerts.

For the purposes of demostrating 508 compliance issues, I will be using [ANDI](https://www.ssa.gov/accessibility/andi/help/install.html) accessibilty alerts.

## Table of Contents:
- [DataTables](https://github.com/semperag/508-Gateway/blob/main/README.md#datatables-link)

## DataTables [:link:](https://datatables.net/)

### Issue: 

[`<a>` without [href] may not be recognized as a link; add [role=link] or [href].](https://www.ssa.gov/accessibility/andi/help/alerts.html?not_recognized_as_link)

![image](https://user-images.githubusercontent.com/61445226/217795022-70f11785-3b8a-465d-900e-99927d8dd16c.png)

### Solution: 
Now since these <a> tags are created dynamically by our DataTable, we must also dynamically add either roles of 'link' or hrefs to these elements. Another problem that happens is that whenever we click on one of these elements our DataTable reloads with new data in the body and completely new <a> tags at the footer 😑. Hold on, DataTable's documention provides us with a neat option called [drawCallback](https://datatables.net/reference/option/drawCallback), this option allows us to run a function whenever our DataTable is redrawen or loads in with new data (which means new <a> tags aswell) 😅. Now we can just add roles of 'links' to the pages at the footer every time DataTable is redrawen.

### Code:

```JS
/* 
 * This code uses JQuery, it is possible to refactor this to only use Vanilla JS.
 * Most projects have an index file, or a header file that all other files import or are children files from.
 * Put this code in its own file and use 'defer' on the script. <script src="fileName.js" defer></script>.
 * Import this file to the 'index' file.
 *
 * When the webpage first loads in, we add in roles 'links' and update DataTable's drawCallback.
 * Everytime the DataTable reloads itself with new elements (typically by clicking a different DataTable page 
 * or searching), we just add in the roles 'links' again to the reloaded DataTable.
 */
$(window).on('load', function() { 
  makeDataTableNavLinks508();
  addDataTableDraw508();
  document.body.addEventListener("click", addDataTableDraw508, false);
});
  
// Update DataTable's drawCallback to call function below when DataTable reloads/rerenders itself
function addDataTableDraw508() {
  document.querySelectorAll('table').forEach(function(table) {
    const dataTable = $(table);
    // Check table was NOT already updated and the table IS a DataTable
    if (!dataTable.hasClass('compliant508') && $.fn.dataTable.isDataTable(dataTable)) {
      dataTable.addClass('compliant508');
      dataTable.on('draw.dt', function() {
        makeDataTableNavLinks508();
      });
    }
  });
}

// Add role 'link' to page numbers at footer of data table
function makeDataTableNavLinks508() {
  document.querySelectorAll('.fg-button').forEach(function(button) {
    button.setAttribute('role', 'link');
  });
}
```
