## Application Form using Google Sheets
1. Create a new Google spreadsheet or type `sheets.new` in browser URL
2. Click `Extensions` from toolbar and then `App Script`
3. Paste the following code in editor.

```
var sheetName = 'Sheet1'
var scriptProp = PropertiesService.getScriptProperties()

function intialSetup () {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet()
  scriptProp.setProperty('key', activeSpreadsheet.getId())
}

function doPost (e) {
  var lock = LockService.getScriptLock()
  lock.tryLock(10000)

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'))
    var sheet = doc.getSheetByName(sheetName)

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0]
    var nextRow = sheet.getLastRow() + 1

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header]
    })

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow])

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  catch (e) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  finally {
    lock.releaseLock()
  }
}
```
4. Click `Deploy` button on top right.
5. If asking for `Select type` Click `setting / gear` icon from left side of the appeared window.
6. Provide Description and set rules and make sure to set the access to `Anyone` and Deploy the script.
7. Authorize access by logging into your google account.
8. Copy the URL and paste at lastof the `index.html` file.
9. Make sure to have the `Attributes / Name` of column and the `name` of input tag in `index.html` must be same. 

### Visit Demo : https://application-form-2.netlify.app/ ###
### Google Sheet link ###
https://docs.google.com/spreadsheets/d/1a-KoFuCQdgwLIPZfHnckoosMuVVwb_jnOZXJCL-0hZM/edit?usp=sharing

Fill the form and check the result in sheets live.
