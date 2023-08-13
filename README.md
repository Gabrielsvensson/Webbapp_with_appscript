# Webbapp_with_appscript  This is a simple foundation for those not able to take advantage of big databases.
The script is intended for many users, therefore caching and grouping data together for less request but heavier. 
Next, I transport the data into a CSV file for simple future analysis. When you want to extract values from the CSV file,
go into Google Sheets and do =Importdata(URL). The best practice would be to include it in a query function for filtering, and sorting.

// Create a web app
function doGet() {
  return HtmlService.createHtmlOutputFromFile('index');
}
This part of the code sets up a web app. A web app is like a webpage, but it's generated and controlled using code. The doGet() function is the entry point of the web app. It returns the content of an HTML file called 'index', creating the user interface for the app.

// Append data to a CSV file
function appendToCSV(data) {
  var validCodes = ['x', 'y', 'z'];

  if (validCodes.includes(data)) {
    var cache = CacheService.getScriptCache();
    var cachedData = cache.get('csvData');

    if (!cachedData) {
      cachedData = data;
    } else {
      cachedData += '\n' + data;
    }

    var cacheCount = Number(cache.get('cacheCount')) || 0;
    cacheCount++;

    if (cacheCount >= 10) {
      appendCSVToFile(cachedData);
      cache.put('csvData', '');
      cache.put('cacheCount', '0');
    } else {
      cache.put('csvData', cachedData);
      cache.put('cacheCount', cacheCount.toString());
    }

    return 'Data added to cache.';
  } else {
    return 'No active code.';
  }
}
This function (appendToCSV) is responsible for adding data to a CSV file, which is a simple way to store structured data in rows and columns, like a table.

validCodes is an array containing codes that are considered valid.
The code checks if the provided data matches any of the valid codes.
It uses a "cache" to temporarily store data before writing it to the CSV file. A cache is like a temporary storage space to hold information.
If there's no cached data, it stores the data; otherwise, it adds the data to the existing cached data with a new line in between.
The function also maintains a count of how many times data has been cached.
When the cache count reaches 10 or more, it calls a function appendCSVToFile to write the cached data to the actual CSV file in Google Drive. Then, it clears the cache and resets the cache count.
If the cache count is less than 10, it just updates the cache with the new data and the updated count.
It returns a message indicating whether the data was added to the cache or if the provided code was invalid.

// Append cached data to CSV file
function appendCSVToFile(data) {
  var folder = DriveApp.getFolderById('(drive)Folder id'); // Replace with your folder ID
  var file = folder.getFilesByName('FileName').next(); // Get the existing CSV file
  var csvContent = file.getBlob().getDataAsString() + '\n' + data;
  file.setContent(csvContent);
}
This function (appendCSVToFile) is responsible for adding the cached data to the CSV file stored in Google Drive.

It gets the target folder where the CSV file is located using its folder ID.
Then, it retrieves the existing CSV file by its name.
It reads the content of the existing CSV file, adds the new data to it, and sets the updated content back to the file.
Overall, this code snippet demonstrates a way to manage and optimize the process of collecting and storing data from a web app into a CSV file, using caching to batch and control the data writing process. It also utilizes Google Apps Script to interact with Google Drive and provide a user-friendly web interface.
