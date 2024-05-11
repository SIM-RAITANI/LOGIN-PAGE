# LOGIN-PAGE
I have created a simple login page for our annual cultural fest AURORA. The login page simply takes input from the user and stores the details automatically to the google sheet linked to it.<br><br>

Follow the steps to integrate in your project:<br>
Step-1 : Create a google sheet. Create desired column with the same name as used in input tag of form tag.<br>
Step-2 : Go to "Extensions" and open Appscript. Paste this code:<br><br>


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

<br><br><br>

Step-3: Run the following code in the Appscript. <br>
Step-4:Deploy the project and generate project URL and paste it to JS .



