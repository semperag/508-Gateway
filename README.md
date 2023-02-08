# 508 Compliance Gateway
Hi! Use this repo to find a quick solution to get rid of some pesky 508 compliance alerts.
For the purposes of demostrating 508 compliance issues, I will be using ANDI (link here) accessibilty alerts.
Table of Contents:
- DataTables

DataTables (https://datatables.net/)
issue: <a> without [href] may not be recognized as a link; add [role=link] or [href].
img:
Solution: Now since these <a> tags are created dynamically by our dataTable, we must also dynamically add either roles of 'link' or hrefs to these elements. Another problem that happens is that whenever we click on one of these elements our dataTable reloads with new data in the body and completely new <a> tags at the footer (great upside down smile). Hold on, dataTable documention provides us with a neat option called drawCallback (link), this option allows us to run a function whenever our dataTable is redrawen or loads in with new data (which means new <a> tags aswell) :). 
Code:
$(window).on('load', function() { 
  makeDataTableNavLinks508();
  addDataTableDraw508();
  document.body.addEventListener("click", addDataTableDraw508, false);
});
// Update data table's draw callback to call function below when data table 
// reloads/rerenders with new page numbers at footer
function addDataTableDraw508() {
  document.querySelectorAll('table').forEach(function(table) {
  const dataTable = $(table);
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
